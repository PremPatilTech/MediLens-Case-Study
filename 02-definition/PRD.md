# Product requirements document (PRD)

| | |
|---|---|
| **Belongs in** | Definition (before build) |
| **Written** | Nov 2026 (reconstructed after build) |
| **Evidence** | Code, database schema, deployed app, screenshots |
| **Confidence** | Observed for behaviour; Derived for rationale |

Status legend: **Built** = present in the working app or code. **Gap** = needed but missing or incomplete. **Proposed** = not built.

## 1. Purpose

Let a sales or liaison user go from a territory to a reviewed, personalised message to healthcare businesses, with a score that says who is reachable first.

## 2. Functional requirements

| ID | Requirement | Priority | Status |
|---|---|---|---|
| **Access** | | | |
| FR-01 | Users sign in (email and password, magic link), and reset a forgotten password. Sign-up is limited to approved emails. | Must | Built (sign-up closed 23 Nov 2026) |
| FR-02 | All product pages and API endpoints require a signed-in user. | Must | Built (API gap fixed and tested 23 Nov 2026) |
| **Discover** | | | |
| FR-03 | Search by sector or specialty near the user or anywhere on a map, with a radius of 1 to 50 km. | Must | Built |
| FR-04 | Detect the user's location (cached, then device location, then IP fallback) and let them change it. | Should | Built (per code and original README) |
| FR-05 | Save each search as a run and show recent runs. | Should | Built |
| FR-06 | Avoid duplicates: the same place is stored once. | Must | Built (place ID is the key) |
| **Enrich** | | | |
| FR-07 | For each business with a website, extract emails, phones, social links and an owner name, and check that the email domain can receive mail (MX check). | Must | Built |
| FR-08 | Deep enrichment: find and read about, team and contact pages and identify named decision-makers with title, email, phone and LinkedIn where published. | Should | Built |
| FR-09 | Record the crawl outcome (pages read, blocked, no website) so a failure is visible. | Should | Built |
| **Score** | | | |
| FR-10 | Give each account a 0 to 100 score and a tier (Ready, Review, Low, Skip). | Must | Built (live app uses the browser formula; see L-07) |
| FR-11 | An account with no website cannot score above 25. | Must | **Gap in the live app** (the cap exists only in the database formula, which is not active) |
| **Review** | | | |
| FR-12 | List accounts with search, filters (contact type, enrichment status, search run), sorting (score, name, rating, recency, distance) and export. | Must | Built |
| FR-13 | Open a detail panel per account with contact, company and outreach tabs. | Must | Built |
| FR-14 | Select many accounts and act on them together. | Should | Built |
| **Outreach** | | | |
| FR-15 | Save sender profiles (name, organisation, target segment, description, value proposition) and mark one as default. | Must | Built |
| FR-16 | Generate a message per account for email, LinkedIn or WhatsApp in a chosen tone. | Must | Built |
| FR-17 | Show a personalisation score from 1 to 5 for each generated message. | Should | Built |
| FR-18 | View, edit, copy and filter messages by status (draft, sent, replied). | Must | Built |
| FR-19 | Send an email from the app and mark it sent with a timestamp. | Should | Built (needs mail credentials) |
| **Understand** | | | |
| FR-20 | Show territory insights: tier mix, sector mix, geography, data completeness, top accounts. | Should | Built |

## 3. Non-functional requirements

| ID | Requirement | Status |
|---|---|---|
| NFR-01 | Page-to-page navigation feels instant (cache with background refresh). | Built (3-minute cache) |
| NFR-02 | Secrets stay on the server and data is only readable by signed-in users who own it. | Keys: built. Signed-in-only access: built 23 Nov 2026 (row-level security, public key revoked, sign-ups closed). Per-user ownership: **Gap** |
| NFR-03 | Works in light and dark themes. | Built |
| NFR-04 | Long operations (scrape, enrich) finish or fail visibly within platform time limits. | Built (route time limits of 120 to 300 seconds) |
| NFR-05 | Personal data is handled lawfully, with a way to remove a person's data. | **Gap**: no deletion workflow yet (see privacy) |
| NFR-06 | Automated tests protect the scoring and enrichment logic. | **Gap**: no test suite in the repository |

## 4. Out of scope for v1

Team accounts and roles, shared pipelines, CRM sync, two-way email inbox, scheduled sequences, mobile app, billing.

## 5. Dependencies

See [`dependencies-and-cost.md`](../05-delivery/dependencies-and-cost.md).

## 6. Open questions

See [`open-questions.md`](../07-reference/open-questions.md).
