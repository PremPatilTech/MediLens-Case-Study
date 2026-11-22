# End-to-end data flow

| | |
|---|---|
| **Belongs in** | Architecture (during build) |
| **Written** | Nov 2026 (reconstructed after build) |
| **Evidence** | Services and API routes in the source code |
| **Confidence** | Observed |

## The pipeline

```mermaid
flowchart TD
    A[1. Search<br/>sector + location + radius] --> B[Maps search API<br/>returns places]
    B --> C{Seen this place<br/>in this run?}
    C -- yes --> X[Skip duplicate]
    C -- no --> D[Parse address:<br/>city, state, zip]
    D --> E[(leads<br/>upsert on place_id)]
    E --> F[2. Website enrichment]
    F --> G{Has website?}
    G -- no --> H[Mark no website<br/>lower score]
    G -- yes --> I[Fetch up to 3 pages<br/>rotate browser identity]
    I --> J[Extract emails, phones,<br/>social links, owner name,<br/>description]
    J --> K[Drop noise, rank emails]
    K --> L[Check MX record<br/>for email domain]
    L --> M[(lead_enrichment)]
    M --> N[3. Deep enrichment<br/>on demand]
    N --> O[Read homepage,<br/>pick about/team/contact pages]
    O --> P[LLM reads pages,<br/>returns named people,<br/>titles, contacts]
    P --> Q[(lead_deep_enrichment)]
    M --> R[4. Score<br/>computed in the browser<br/>from the stored fields]
    Q --> R
    R --> S[Tier: Ready, Review,<br/>Low, Skip]
    S --> T[5. Outreach generation<br/>profile + channel + tone]
    T --> U[LLM returns JSON:<br/>subject, body, note, score 1-5]
    U --> V[(lead_outreach<br/>status = draft)]
    V --> W[6. Human review<br/>edit, copy]
    W --> Y{Send by email?}
    Y -- yes --> Z[SMTP send<br/>status = sent, time stamped]
    Y -- no --> Z2[Copy and send elsewhere,<br/>mark sent by hand]
```

## Data stores touched at each step

| Step | Reads | Writes |
|---|---|---|
| Search | Search form | `scrape_runs`, `leads` |
| Enrich | `leads` | `lead_enrichment`, `lead_errors` |
| Deep enrich | `leads` | `lead_deep_enrichment` |
| Score | `leads`, `lead_enrichment` | Nothing (computed when the page loads) |
| Generate | `leads`, enrichment, `sender_profiles` | `lead_outreach` |
| Send | `lead_outreach` | `lead_outreach.status` and `sent_at` |

## Personal data in the flow

Steps 2 and 3 can collect names, emails and phone numbers of individuals who published them on a business website. See [`privacy-and-compliance.md`](../06-quality-launch/privacy-and-compliance.md).
