# Test plan

| | |
|---|---|
| **Belongs in** | Quality (during build) |
| **Written** | Nov 2026 (reconstructed after build) |
| **Evidence** | Code structure; one manual test run. The repository has no automated tests |
| **Confidence** | Derived |

## 1. Current state

- **Automated tests:** none.
- **Manual testing:** one end-to-end run on real data: a search for pharmacies in one city returned 20 accounts; 7 were enriched; one draft was generated. It proves the flow runs; it does not prove correctness.

## 2. Strategy (what to build first)

| Level | What | Why first |
|---|---|---|
| Unit | Scoring function, tier boundaries, email ranking, JSON parsing, address parsing, distance | Pure logic, high risk, cheap to test |
| API | Every endpoint returns 401 without a session; validation errors; batch limits | Closes the biggest risk (L-01) |
| Integration | Search to enrich to score to draft with a fake maps response and a fake LLM | Catches wiring errors |
| End to end | Sign in, search, open account, generate, edit, mark sent | One happy path in a browser |
| Data | Setup scripts create every table the code uses | Catches L-06 |
| Non-functional | Time to first draft for 20 accounts; behaviour at the time limits | Confirms the experience target |

## 3. Priority test cases

| ID | Area | Case | Expected |
|---|---|---|---|
| T-01 | Score | No website, strong other signals | Score is 25 or less (**fails under the live browser formula**, L-07) |
| T-02 | Score | Valid email on the business domain, owner name, phone, 3 pages | 75 or more, tier Ready |
| T-03 | Score | Boundaries at 24, 25, 49, 50, 74, 75 | Tiers Skip, Low, Low, Review, Review, Ready |
| T-04 | Score | Blocked crawl | 10 points lower than the same account unblocked |
| T-05 | Extract | Page with several emails including a no-reply and an image file name | Noise dropped; generic business inbox ranked first |
| T-06 | Extract | Social links that are share or login URLs | Not recorded as profiles |
| T-07 | Dedupe | Same place twice in one search; same place in two searches | One row; counts show updated, not new |
| T-08 | Outreach | LLM returns JSON inside code fences | Parsed correctly |
| T-09 | Outreach | LLM returns broken text | A fallback draft is saved **and flagged** (currently unflagged) |
| T-10 | Outreach | Generate twice for the same account, profile and channel | One draft, replaced |
| T-11 | Send | Credentials missing | Message stays a draft; user is told nothing was sent (**fixed 23 Nov 2026**, verified in code and build) |
| T-12 | Send | Test mode on | Email goes to the test recipient only |
| T-13 | Security | Each API route without a session | 401 (**passes on the live app since 23 Nov 2026**; run manually, not yet automated) |
| T-14 | Setup | Run the setup scripts and the security migration on an empty database, then Deep Enrich | Works (migration added; live database lookup answers) |
| T-16 | Security | Public database key: read and no-match write on every table | Refused (**passes**, manual) |
| T-17 | Security | Sign up with an address not on the allow-list | Refused (**passes**, manual) |
| T-18 | Security | Login redirect set to a script URL, an absolute URL, "//host" or "@host" | Falls back to the home page (**passes** in a unit check) |
| T-19 | Security | Send an email to an address that is not a stored contact of the lead | 403 |
| T-20 | Score | Account with no website under the live formula | Decide the intended rule (see L-07) |
| T-15 | Distance | Two known points | Matches a reference value within 1% |

## 4. Test data rules

Use **fake businesses and fake people**. Never copy real contact data into test fixtures or screenshots.

## 5. Exit criteria for a pilot

All Critical and High items in [`known-limitations.md`](known-limitations.md) closed (the Critical ones were closed on 23 Nov 2026); T-01 to T-15 passing; a UAT sign-off using [`uat-checklist.md`](uat-checklist.md).
