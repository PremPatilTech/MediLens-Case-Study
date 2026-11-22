# Decision log

| | |
|---|---|
| **Belongs in** | Delivery (kept throughout) |
| **Written** | Nov 2026 (reconstructed after build) |
| **Evidence** | Code and repository history. Exact decision dates were not recorded |
| **Confidence** | Derived |

Format: context, decision, alternatives, consequences.

### D-01 Narrow to healthcare and pharma
- **Context:** an earlier general lead-generation prototype had no defined buyer.
- **Decision:** build for one domain where accounts, decision-makers and tone are specific.
- **Alternatives:** keep it general; pick another vertical.
- **Consequences:** clearer data model and messaging; smaller addressable audience; needs domain validation.

### D-02 Native server code instead of an external workflow tool
- **Context:** the prototype used an automation tool for scraping, enrichment and message generation.
- **Decision:** move each step into server code inside the app (the services in `lib/services`).
- **Alternatives:** keep the workflows; split into separate services.
- **Consequences:** one deployable app and easier debugging; but long operations run inside web-request time limits (120 to 300 seconds).

### D-03 Next.js and Supabase
- **Decision:** one framework for pages and endpoints; a managed database with auth.
- **Consequences:** fast build; lock-in to those platforms; row-level security and ownership still to be designed.

### D-04 Where the score is computed
- **Decision:** the setup script computes the reachability score in a SQL function exposed through a view; the app also carries a simpler browser formula as a fallback.
- **What happened:** the live database has `leads_full` as an empty plain table, so the browser formula is the one in use.
- **Consequences:** the score works, but there are two definitions and the live one lacks the no-website cap. Decision needed: one formula, defined once (L-07).

### D-05 Drafts for human review
- **Decision:** the system generates drafts; a person edits and sends.
- **Alternatives:** automatic sending and sequences.
- **Consequences:** protects sender reputation and avoids spam; slower for the user. Kept as a product principle.

### D-06 Free-tier model during validation
- **Decision:** use a free-tier LLM model for extraction and writing while the product is unproven.
- **Consequences:** near-zero cost; quality and uptime are less predictable, which shows in the generic fallback message.

### D-07 Cache with background refresh
- **Decision:** keep recent data in the browser for 3 minutes and refresh quietly.
- **Consequences:** instant navigation; briefly stale data; each tab has its own cache.

### D-08 Mail through a personal mail account
- **Decision:** send through an ordinary mail account for the prototype.
- **Consequences:** simplest set-up; daily sending limits and deliverability risk; not suitable beyond a pilot.

### D-10 Lock the product down before publishing (23 Nov 2026)
- **Context:** writing this case study exposed unauthenticated endpoints and a database open to the public key.
- **Decision:** fix and re-test them before publishing anything.
- **Consequences:** sign-up is now closed and some features wait on a framework upgrade; the case study can describe the product without describing an open door.

### D-09 Publish documents, not code
- **Decision (Nov 2026):** keep the source private and publish this case study.
- **Why:** the product has unresolved security gaps, and the documents show the product thinking without exposing the implementation.
