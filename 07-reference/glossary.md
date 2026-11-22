# Glossary

| | |
|---|---|
| **Belongs in** | Reference (throughout) |
| **Written** | Nov 2026 |
| **Evidence** | Product screens and code |
| **Confidence** | Observed |

| Term | Meaning |
|---|---|
| **Account** | A healthcare business (pharmacy, clinic, hospital, supply store) stored as a record. Called a *lead* in the database. |
| **Search run** | One search (sector, place, radius) and the accounts it produced. |
| **Enrichment** | Reading an account's website to find emails, phones, social links and an owner name. |
| **Deep enrichment** | Reading team and contact pages with an LLM to find named decision-makers. |
| **Decision-maker** | A person who can say yes: a clinic director, practice manager, lead physician, owner. |
| **MX check** | A DNS lookup that shows whether an email domain can receive mail. |
| **Fit score / reachability score** | 0 to 100 measure of how easy and sensible it is to contact an account. |
| **Tier** | Score band: Ready (75 to 100), Review (50 to 74), Low (25 to 49), Skip (0 to 24). |
| **Sender profile / liaison profile** | The identity and pitch the messages are written from. |
| **Channel** | Email, LinkedIn note or WhatsApp. |
| **Tone** | Professional, conversational or direct. |
| **Personalisation score** | 1 to 5 rating attached to a generated message. |
| **Draft / sent / replied** | Message statuses. |
| **Fallback template** | A generic message used when the LLM output cannot be read. |
| **MSL** | Medical science liaison: a field role that builds relationships with clinicians. |
| **RAG, LLM** | Retrieval-augmented generation; large language model. |
| **PRD** | Product requirements document. |
| **RICE / MoSCoW** | Prioritisation methods. |
| **UAT** | User acceptance testing. |
| **RACI** | Responsible, accountable, consulted, informed. |
| **ERD** | Entity relationship diagram. |
| **RLS** | Row-level security: rules in the database that decide who can read or change each row. |
| **Allow-list** | The list of emails permitted to register. |
