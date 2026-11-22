# Known limitations, security review and documentation debt

| | |
|---|---|
| **Belongs in** | Quality and launch (before launch) |
| **Written** | Nov 2026; security items fixed and re-tested on 23 Nov 2026 |
| **Evidence** | Code review, the deployed app, no-side-effect tests of the endpoints and the database, the database provider's security scanner |
| **Confidence** | Observed |

Found while writing this case study. **Severity:** Critical = fix before anyone uses it; High = fix before a pilot; Medium = fix soon; Low = polish.

## Summary of what changed on 23 Nov 2026

| Area | Before | After |
|---|---|---|
| API endpoints | Answered without signing in | Signed-out requests get 401 on every route (tested) |
| Database access | No row-level security; the public key could read, change and delete every table | Row-level security on; the public key has no access; reads and writes were refused in a re-test |
| Sign-up | Open to anyone | Closed; only approved emails can register (a throwaway sign-up was refused) |
| Redirects | Login and sign-in callback accepted any redirect address | Only in-app paths allowed |
| Browser headers | HTTPS only | Frame blocking, no content sniffing, referrer and permissions policies added |
| Email sending | Any signed-in user could send to any address; a missing mail setup was reported as sent | Recipient must be a stored contact of that message's lead; without mail setup it returns an error and leaves the draft |
| Packages | 24 known advisories | 7 left, all tied to the web framework's major version |
| Deep Enrich | Failed on the live database (table missing) | Table created; the lookup works |

## Security

| # | Finding | Severity | Status |
|---|---|---|---|
| L-01 | API endpoints worked without signing in (including a read of stored messages). | Critical | **Fixed 23 Nov 2026** (middleware; tested signed out and signed in) |
| L-16 | **The database was open to the public key**: no row-level security, so anyone could read, change or delete data. Only test data was exposed. | Critical | **Fixed 23 Nov 2026** (policies for signed-in users; the public key revoked; re-tested) |
| L-17 | Sign-up was open, so anyone could create an account and use every feature. | High | **Fixed 23 Nov 2026** (allow-list trigger) |
| L-18 | The login page and sign-in callback accepted any redirect address, including a script address and a "@host" trick. | Medium | **Fixed 23 Nov 2026** (only in-app paths; tested against attack strings) |
| L-19 | No frame-blocking or other browser security headers. | Medium | **Fixed 23 Nov 2026** (verified on the live site) |
| L-20 | Email endpoint could mail any address for any signed-in user. | High | **Fixed 23 Nov 2026** |
| L-21 | 24 vulnerable packages. | High | **Partly fixed**: 7 remain, all in the web framework version in use. Needs a planned major upgrade with full regression testing. |
| L-02 | No owner on records: every signed-in user sees all data. Acceptable only while sign-up is closed. | High | Open (needed before more than one customer) |
| L-22 | No rate limits on endpoints. | Medium | Open |
| L-23 | The auth provider's leaked-password check is off. | Low | Open (a provider setting) |
| L-03 | No deletion, opt-out or suppression workflow for personal contacts. | High | Open |

## Correctness

| # | Finding | Severity | Status |
|---|---|---|---|
| L-04 | When the LLM output cannot be read, a **generic fallback template** is saved with a default personalisation score of 4. The one draft in the test run is this template, so the 4.0 average on the Outreach page is not a quality signal. | High | Open |
| L-05 | Without mail credentials the send endpoint reported a simulation but marked the message as sent. | High | **Fixed 23 Nov 2026** |
| L-06 | The setup script did not create `lead_deep_enrichment`, which Deep Enrich needs. | High | **Fixed 23 Nov 2026** (migration added to the repository and applied) |
| L-07 | **The live score does not come from the database function.** In the deployed database `leads_full` is an empty plain table (the setup script defines it as a view), so the app falls back to a simpler formula in the browser code, with different weights. The documents first described the database version; they now describe both. | High | Open (decide one formula, make the view real) |
| L-08 | The Dashboard shows "Avg clinical fit 32" while Insights shows "Avg fit score 47" for the same data. They appear to use different denominators (all 20 accounts vs the 7 enriched). | Medium | Open |
| L-09 | The first README listed score tiers of 70, 40, 20 and 0; the code and screens use 75, 50, 25 and 0. | Low | Done in this repository |

## Quality and operations

| # | Finding | Severity | Status |
|---|---|---|---|
| L-10 | No automated tests in the repository. | High | Open |
| L-11 | Long steps run inside one web request (120 to 300 seconds). A large batch can time out. | Medium | Open |
| L-12 | Legacy naming from the earlier prototype remains: the setup script header and the fallback message signature. (The default sender name in the mail endpoint was renamed.) | Medium | Partly fixed |
| L-13 | Provider naming is inconsistent between the first README and the code (two map-search providers are supported in code). | Low | Open |

## Visible interface issues

| # | Finding | Severity | Status |
|---|---|---|---|
| L-14 | The dashboard greeting reads "Good evening, Dr.." with a doubled full stop. | Low | Open |
| L-15 | On the Search page the map's zoom buttons overlap the start of the search box placeholder. | Low | Open |

## How the security review was done (so it can be repeated)

1. Read the middleware, API routes and database policies.
2. Tested each endpoint without a session (empty requests that change nothing).
3. Used the app's public database key, as a stranger could, to attempt reads and no-match writes.
4. Ran the provider's security scanner and the package audit.
5. Searched the full code history for secrets (none found) and for unsafe patterns (none found).
6. After each fix, repeated the same tests on the live app.

## What works well

The pipeline runs end to end on real data (search, enrich, draft); duplicates are prevented at the database; failures are stored; drafts require a human decision; the first security review found issues and each one was fixed and re-tested the same day.
