# Data structures and algorithms

| | |
|---|---|
| **Belongs in** | Architecture (during build) |
| **Written** | Nov 2026 (reconstructed after build) |
| **Evidence** | `lib/services/*`, `lib/format.ts`, `lib/cache/data-cache.ts`, `database/schema.sql` |
| **Confidence** | Observed |

Each entry says what problem it solves, how it works, and what it costs. *n* is the number of items in the step.

## 1. Deduplicating search results

- **Problem:** a map search returns the same place more than once, especially across several queries.
- **How:** a `Set` of place IDs is kept for the run. A place already in the set is skipped. Saving uses a database **upsert on `place_id`**, so repeat runs update rather than duplicate.
- **Cost:** O(n) time, O(n) memory per run. The database enforces the same rule with a primary key.

## 2. Extracting and ranking emails (website enrichment)

```text
html -> find mailto: links  +  regex over text
     -> drop noise (image file names, tracking and error-monitoring domains,
        no-reply addresses, example domains)
     -> rank: prefer generic business prefixes (info, contact, hello, sales,
        support, office, inquiries) and addresses that match the city/state
     -> take the best, keep the rest in all_emails_found
     -> look up the MX record of its domain  ->  valid / no MX / check failed
```

- **Why rank:** a business page often lists several addresses; the best one for a first message is usually the public contact inbox.
- **Cost:** linear in page size. One DNS lookup per lead.
- **Same approach for** phones, social links (each with a blocklist of share and login URLs) and owner name (a "looks like a person's name" check with a stop-word list).

## 3. Reading the right pages (deep enrichment)

```text
homepage text (first 3000 characters)
  + internal links scored by keywords (about, team, staff, leadership,
    contact, people, story, management)
  -> LLM chooses which sub-pages are worth reading
  + fixed fallback paths (/contact /about /team /staff /leadership
    /our-team /meet-the-team /people /our-people)
  -> fetch each (8 s timeout)  ->  LLM extracts named people as JSON
```

- **Why two layers:** keyword scoring is cheap and predictable; the LLM handles unusual site structures.
- **Batch mode:** a **worker queue with a fixed concurrency of 2** processes many accounts without overloading the LLM or the sites.
- **Cost:** about 3 to 6 page fetches and 2 LLM calls per account.

## 4. Reachability score (0 to 100)

**Important:** two formulas exist. The **live app uses the browser formula (4a)**. The richer **database formula (4b)** is in the setup script but is not active in the deployed database, where `leads_full` is an empty plain table instead of the view. This was found while documenting (limitation L-07). The tiers are the same for both.

| Score | Tier |
|---|---|
| 75 to 100 | Ready |
| 50 to 74 | Review |
| 25 to 49 | Low |
| 0 to 24 | Skip |

### 4a. Browser formula (live)

| Signal | Points |
|---|---|
| Has an email | 30 |
| Email domain passes the MX check | 10 |
| Has a website | 15 |
| Has a phone (listing or website) | 10 |
| Has an owner name | 10 |
| Has a LinkedIn company page | 5 |
| Google rating 4.0 or more | 10 |
| 10 or more Google reviews | 5 |
| Has a category | 5 |
| **Total** | **100** (capped) |

Check against the test run: the one "Ready" account (email, valid MX, website, phone, 24 reviews, category, rating 3.5) = 30 + 10 + 15 + 10 + 5 + 5 = **75**, which is what the screen shows.

*Consequence:* there is **no website cap** in the live formula; an account without a website simply loses 15 points.

### 4b. Database formula (in the setup script, not active live)

| Block | What it rewards | Max |
|---|---|---|
| A. Contact reachability | Valid email (25) or any email (12); business-domain email, +8 if it matches the website; owner name (7); phone on website (5); personal LinkedIn (3) | 48 |
| B. Data depth | Pages crawled (12 for 3 or more, 7 for 1 or 2); description length (8 or 4); sitemap used (2) | 22 |
| C. Business legitimacy | Google rating (up to 10); review count (+8 for 50 or more, down to -5 for none); maps description (4); opening hours (3) | 25 |
| D. Personalisation bonus | Best message score 5 / 4 / 3 gives 10 / 7 / 4 | 10 |

Penalties and gates: **blocked crawl -10; no website caps the score at 25.** The result is clamped to 0 to 100.

**Worked example (illustrative, not a real account):** valid email on the business domain 25 + 8, owner name 7, phone 5, 3 pages crawled 12, description over 100 characters 8, rating 4.6 gives 10, 60 reviews gives 8, maps description 4, opening hours 3 = 90, so **Ready**.

**Recommendation:** pick one formula, make `leads_full` a real view, and delete the other.

## 5. Distance between two points (Haversine)

```text
a = sin²(Δlat/2) + cos(lat1)·cos(lat2)·sin²(Δlon/2)
distance = 2 · R · asin(√a),  R = 6371 km
```

Used to show and sort accounts by distance from the user. O(1) per account, so sorting 1,000 accounts is trivial.

## 6. Stale-while-revalidate cache

- **Structure:** a `Map` from key to `{ data, timestamp }`, with a 3-minute freshness window.
- **Behaviour:** a read returns the stored data immediately and says whether it is stale. If stale, the page refreshes in the background. After a change (a new search, an enrichment) the matching keys are invalidated by pattern.
- **Cost:** O(1) read and write; invalidation is O(k) in the number of keys.
- **Trade-off:** the cache lives in memory in the browser, so a reload starts empty and two tabs do not share it.

## 7. Turning LLM text into data (outreach)

```text
response text
  -> try the expected JSON block
  -> else strip code fences, take the text between the first "{" and last "}"
  -> sanitise: cut the body to 2000 characters, the subject to 200,
     remove leaked "thinking" text, clamp score to 1..5
  -> else use a generic fallback template with score 4
```

- **Why:** model output is not always clean JSON.
- **Risk found:** the fallback template is generic and its score of 4 looks like a real rating. Messages produced this way should be flagged (see known limitations).

## 8. Uniqueness rule for messages

A database constraint on **(account, profile, channel)** with an upsert means regenerating a message replaces the old draft. It keeps one live draft per combination and makes the operation safe to repeat.
