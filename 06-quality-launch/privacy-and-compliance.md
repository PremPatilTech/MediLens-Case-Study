# Privacy and compliance plan

| | |
|---|---|
| **Belongs in** | Quality and launch (before launch) |
| **Written** | Nov 2026 |
| **Evidence** | What the product collects (code and schema); general public knowledge of the rules named |
| **Confidence** | Derived. **This is a product-planning document, not legal advice.** A qualified adviser must review it before launch. |

## 1. Data inventory

| Data | Source | Personal data? | Where stored |
|---|---|---|---|
| Business name, address, phone, rating, hours, coordinates | Maps search results | Mostly business data; a sole practitioner's details can be personal | `leads` |
| Business emails and phones from websites | Public web pages | Often generic; sometimes tied to a named person | `lead_enrichment` |
| Named people, titles, direct emails, phones, LinkedIn links | Team and contact pages (deep enrichment) | **Yes** | `lead_deep_enrichment` |
| Sender profiles (name, organisation, pitch) | The user | Yes (the user) | `sender_profiles` |
| Generated messages | The system | May contain a recipient's name | `lead_outreach` |
| User accounts | Sign-up | Yes | Supabase auth |
| Search location | Browser or IP | Yes (the user) | Browser, search runs |

## 2. Rules to check before use in each target market

| Area | Examples to review | Why it matters here |
|---|---|---|
| Data protection | India DPDP Act 2023; GDPR and UK GDPR for EU and UK recipients; other national laws | Named people and direct contacts are personal data; notice, lawful basis, rights and transfers apply |
| Electronic marketing | Email rules by country (for example CAN-SPAM in the US, CASL in Canada, ePrivacy rules in the EU and UK) | Unsubscribe, sender identity and consent rules differ for individuals and companies |
| Messaging platforms | WhatsApp business policy; platform rules on cold messages | Opt-in is normally required for business messaging |
| Professional networks | The platform's terms on automated collection | The product records LinkedIn links that sites publish; it does not read the network itself, and must not start to |
| Healthcare marketing | Local pharma and healthcare marketing codes and regulator guidance | Messages to clinicians may be restricted; the product must not make clinical or product claims |
| Provider terms | Map data licence, website terms, LLM provider data terms | Storage and reuse of results; personal data sent to a model provider |

## 3. Principles for the product

1. **Minimise:** keep only what outreach needs; do not collect personal data when a business inbox is enough.
2. **Be transparent:** every message states who the sender is and why they were contacted.
3. **Let people opt out:** an unsubscribe line in every email and a suppression list that blocks future contact.
4. **Allow deletion:** a person can ask for their data to be removed, and it is removed from every table.
5. **Limit retention:** delete accounts and contacts not used within a set period (to be decided with the adviser).
6. **Keep a human in the loop:** drafts only; no automatic sending.
7. **Protect access:** sign-in on every endpoint, per-user ownership, secrets on the server.

## 4. Control status

| Control | Status |
|---|---|
| Sign-in for pages | Built |
| Sign-in for API endpoints | Built (23 Nov 2026) |
| Database closed to the public key; sign-ups closed | Built (23 Nov 2026) |
| Per-user data ownership | **Gap** |
| Unsubscribe line in email | **Gap** |
| Suppression list | **Gap** |
| Deletion on request | **Gap** |
| Retention schedule | **Gap** |
| Record of sources per contact | Partly (source query and URL fields exist for enrichment) |
| Draft-only sending | Built |
| Privacy notice and terms of use | **Gap** |
| Processor agreements (database, model, hosting) | To be reviewed |

## 5. Decisions needed

See [`open-questions.md`](../07-reference/open-questions.md): target countries for the pilot, whether to collect personal contacts at all in v1, and who reviews compliance.
