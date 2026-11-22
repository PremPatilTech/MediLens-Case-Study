# API specification

| | |
|---|---|
| **Belongs in** | Architecture (during build) |
| **Written** | Nov 2026 (reconstructed after build) |
| **Evidence** | `app/api/*/route.ts` |
| **Confidence** | Observed (including a no-side-effect check that the endpoints answer without a sign-in) |

All endpoints are JSON over HTTPS. **Authentication: a signed-in session is required on every `/api/*` route.** A request without one gets `401 {"error":"Unauthorized"}`. This was not true until 23 Nov 2026 (see [`known-limitations.md`](../06-quality-launch/known-limitations.md), L-01); it is now tested on the live app.

| Method and path | Purpose | Request body | Success | Errors | Time limit |
|---|---|---|---|---|---|
| `POST /api/scrape` | Search places and store them | `query` or `queries[]`, `lat`, `lng`, optional `locationName`, `zoom`, `countryCode`, `language`, `resultsPerQuery` | Run summary (found, new, updated) | 400 invalid JSON or search failure | 300 s |
| `POST /api/enrich` | Website enrichment for accounts | `place_ids[]` (non-empty) | `leadsProcessed`, `emailsFound`, `phonesFound`, `hardBlocked` | 400 empty or invalid list | 120 s |
| `POST /api/deep-enrich` | Find decision-makers for one account or a batch | `place_id` or `place_ids[]` | Contacts, confidence, pages crawled, model used | 400 missing id or no website; 500 database error | 120 s |
| `POST /api/outreach` | Generate messages | `profile_id`, `place_ids[]`, optional `channel` (email, linkedin, whatsapp), `tone` (professional, conversational, direct) | Counts and the generated messages | 400 missing profile or accounts | 120 s |
| `GET /api/outreach` | List all outreach records with business name and city | none | Array of records | 500 on error | |
| `POST /api/send-email` | Send an email and mark it sent | `outreachId`, `to`, `body`, optional `subject`, `senderName` | `success`, `messageId`, `sentTo`, `isTest` | 400 missing field or too long; 401 not signed in; 403 recipient is not a stored contact of the lead; 404 unknown message; 503 mail not configured (message stays a draft); 500 send failure | |
| `GET /auth/callback` | Complete sign-in (OAuth or magic link) | Query parameters from the provider | Redirect | | |

## Defaults and behaviour

- `channel` defaults to `email`; `tone` defaults to `professional`.
- `/api/deep-enrich` reads the business name and website from the database; it does not trust values sent by the browser.
- `/api/send-email` sends to a test recipient when test mode is configured, and only to a contact stored for the message's lead.
- Without mail credentials, `/api/send-email` returns 503 and leaves the message as a draft.

## Still to add

1. Check that the signed-in user owns the records they act on (needs a user or workspace ID on the data).
2. Rate limits per user and per IP, especially on scrape, outreach and send-email.
3. Batch-size limits and error messages that do not leak internals.
