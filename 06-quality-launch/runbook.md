# Operations runbook

| | |
|---|---|
| **Belongs in** | Launch (before launch) |
| **Written** | Nov 2026 |
| **Evidence** | Setup guide and services in the code |
| **Confidence** | Derived |

## 1. What must be configured (names only, never values)

| Setting | Used for |
|---|---|
| Database URL, public key, service key | Reading and writing data; the service key stays server-side |
| Maps search API key | Searching places |
| LLM API key, base address, model name | Contact extraction and message writing |
| Mail account and app password (optional) | Sending email; without them sending returns an error and the message stays a draft |
| Test recipient (optional) | Redirects every email to one address while testing |
| App URL | Links and redirects |

**Rule:** secrets live in the hosting platform's settings and are never committed.

## 2. New environment

1. Create the database project.
2. Run `schema.sql`, then `migration_add_scrape_run_id.sql`, then **`migration_security_hardening.sql`** (adds the deep-enrichment table, row-level security, no public-key access and closed sign-ups). Add your approved emails to `allowed_signups` first.
3. Add the settings above in the hosting platform.
4. Deploy; open the sign-in page; create a first user.
5. Create one sender profile and mark it default.
6. Run a small test search (5 km, 10 results) and confirm: accounts appear, one enrichment works, one draft is saved.

## 3. Common problems

| Symptom | Likely cause | What to do |
|---|---|---|
| Search returns nothing | Search key missing or out of credit; location wrong | Check the key and credit; try another location |
| Many accounts show "blocked" | Websites refuse automated readers | Expected; use deep enrichment selectively; do not bypass blocks |
| Deep Enrich fails on a new setup | Security migration not run (table missing) | Run `migration_security_hardening.sql` |
| Sign-up says "Sign-ups are closed for this app." | Email not on the allow-list | Add it to `allowed_signups` |
| Send says "Email sending is not configured" | No mail credentials | Add them; the message stays a draft |
| Send says the recipient does not match | The address is not a stored contact of that lead | Use the lead's stored email |
| Drafts all look the same | The LLM failed and fallback templates were used | Check the LLM key and quota; regenerate |
| Send says success but nothing arrives | Test mode is on, so mail goes to the test recipient | Remove the test recipient setting for real sends |
| Operations time out | Large batch inside the time limit | Run smaller batches |
| Sign-in loops | Wrong app URL or auth redirect settings | Fix the redirect URL in the auth settings |

## 4. Monitoring to add before a pilot

Error log review daily; alert on repeated 5xx responses; track LLM failure rate (share of fallback drafts); track map and LLM calls per search for cost.

## 5. Backups and access

Turn on the database's scheduled backups. Limit who holds the service key. Rotate keys if the code or settings were ever exposed.
