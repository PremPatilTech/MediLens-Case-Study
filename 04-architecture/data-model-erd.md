# Data model (ERD)

| | |
|---|---|
| **Belongs in** | Architecture (during build) |
| **Written** | Nov 2026 (reconstructed after build) |
| **Evidence** | `database/schema.sql` and the services that write to the database |
| **Confidence** | Observed, except `lead_deep_enrichment` columns (Derived from code) |

## Entity relationship diagram

```mermaid
erDiagram
    scrape_runs ||--o{ leads : "produced"
    leads ||--o| lead_enrichment : "has one"
    leads ||--o{ lead_deep_enrichment : "has many"
    leads ||--o{ lead_outreach : "has many"
    leads ||--o{ lead_errors : "logs"
    sender_profiles ||--o{ lead_outreach : "writes as"

    scrape_runs {
        uuid id PK
        text_array queries
        numeric lat
        numeric lng
        int zoom
        text country_code
        int leads_found
        int leads_new
        int leads_updated
        text status
        timestamptz started_at
        timestamptz completed_at
        text error_message
    }
    leads {
        text place_id PK
        text business_name
        text category
        text full_address
        text city
        text state
        text phone
        text website
        numeric google_rating
        int review_count
        numeric latitude
        numeric longitude
        text source_query
        uuid scrape_run_id FK
        timestamptz scraped_at
    }
    lead_enrichment {
        uuid id PK
        text place_id FK "unique"
        text email
        text email_valid
        text all_emails_found
        text website_phone
        text owner_name
        text linkedin_profiles
        text facebook
        text instagram
        text company_description
        text crawl_status
        int pages_crawled
        boolean sitemap_used
        timestamptz enriched_at
    }
    lead_deep_enrichment {
        uuid id PK
        text place_id FK
        text status
        json personal_contacts "inferred"
        int confidence "inferred"
    }
    sender_profiles {
        uuid id PK
        text profile_name
        text owner_name
        text company_name
        text service_description
        text value_proposition
        text target_industry
        boolean is_default
    }
    lead_outreach {
        uuid id PK
        text place_id FK
        uuid profile_id FK
        text channel "email, linkedin, whatsapp"
        text tone "professional, conversational, direct"
        text subject_line
        text message_body
        int personalization_score "1 to 5"
        text status "draft, sent, replied"
        timestamptz sent_at
        text model_used
    }
    lead_errors {
        uuid id PK
        text place_id FK
        text run_type
        text error_type
        int http_status
        timestamptz created_at
    }
```

## Other objects

| Object | Purpose |
|---|---|
| `enrichment_runs` | Log of enrichment batches (counts, status). No foreign key to other tables. |
| `leads_full` | In the setup script a view that adds the computed score. **In the live database it is an empty plain table**, so the app computes the score in the browser (L-07). |
| `compute_lead_quality_score(...)` | The database scoring function. Present in the setup script; not used by the live app (see [`algorithms.md`](algorithms.md)). |
| `update_updated_at_column()` trigger | Keeps `updated_at` current on leads, enrichment and profiles. |
| `set_outreach_sent_at()` trigger | Sets `sent_at` the first time a message becomes *sent*. |

## Rules enforced by the database

- One account per place (`place_id` primary key).
- One enrichment row per account (unique `place_id`).
- One message per (account, profile, channel): regenerating replaces, it does not duplicate.
- Channel, tone and status can only hold the listed values (check constraints).
- Deleting an account deletes its enrichment and outreach (cascade).

## Gaps found while documenting

- The code reads and writes `lead_deep_enrichment`, but **the original setup script did not create it**. Fixed 23 Nov 2026 by `database/migration_security_hardening.sql`, which is also applied to the live database.
- `leads_full` differs between the setup script (view) and the live database (empty table). Tracked as L-07.
- Access rules added 23 Nov 2026: row-level security on every table, a policy for signed-in users only, no privileges for the public key, and an `allowed_signups` table with a trigger that closes registration to approved emails.
