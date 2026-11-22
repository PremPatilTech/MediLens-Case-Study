# Sequence diagrams

| | |
|---|---|
| **Belongs in** | Architecture (during build) |
| **Written** | Nov 2026 (reconstructed after build) |
| **Evidence** | API routes and services in the source code |
| **Confidence** | Observed |

## 1. Search and save accounts

```mermaid
sequenceDiagram
    actor U as User
    participant UI as Search page
    participant API as /api/scrape
    participant M as Maps search API
    participant DB as Supabase
    U->>UI: sector, location, radius
    UI->>API: POST query, lat, lng, zoom, country
    API->>DB: insert scrape_run (status running)
    API->>M: search places
    M-->>API: places
    API->>API: drop duplicates (Set of place IDs), parse address
    API->>DB: upsert leads on place_id
    API->>DB: update scrape_run (counts, status)
    API-->>UI: summary (found, new, updated)
    UI->>UI: invalidate cache, reload list
```

## 2. Website enrichment

```mermaid
sequenceDiagram
    participant UI as Intelligence page
    participant API as /api/enrich
    participant DB as Supabase
    participant W as Business website
    participant D as DNS
    UI->>API: POST place_ids[]
    API->>DB: read leads
    loop each lead with a website
        API->>W: fetch pages (timeout 15 s, rotating browser identity)
        W-->>API: HTML or blocked
        API->>API: extract emails, phones, social, owner name
        API->>D: MX lookup for best email domain
        D-->>API: valid / no MX / check failed
        API->>DB: upsert lead_enrichment (and lead_errors on failure)
    end
    API-->>UI: leadsProcessed, emailsFound, phonesFound, hardBlocked
```

## 3. Deep enrichment (one account)

```mermaid
sequenceDiagram
    actor U as User
    participant P as Detail panel
    participant API as /api/deep-enrich
    participant DB as Supabase
    participant W as Website
    participant L as LLM
    U->>P: click Deep Enrich
    P->>API: POST place_id
    API->>DB: read name and website (never trusts the browser for these)
    API->>DB: insert lead_deep_enrichment (running)
    API->>W: fetch homepage (8 s timeout)
    API->>L: choose useful sub-pages from links
    API->>W: fetch chosen pages plus fixed paths (/contact, /about, /team ...)
    API->>L: extract named people, titles, contacts as JSON
    L-->>API: contacts, confidence, notes
    API->>DB: update row (completed or failed)
    API-->>P: contacts and summary
    P->>P: store in cache, notify list to refresh
```

## 4. Generate outreach

```mermaid
sequenceDiagram
    actor U as User
    participant UI as Generate modal
    participant API as /api/outreach
    participant DB as Supabase
    participant L as LLM
    U->>UI: pick profile, channel, tone, accounts
    UI->>API: POST profile_id, place_ids, channel, tone
    API->>DB: read profile and leads
    loop each account
        API->>L: system prompt + account facts + profile
        L-->>API: JSON (subject, body, note, score, notes)
        API->>API: parse, clamp score to 1-5, use a generic template with score 4 if the JSON is broken
        API->>DB: upsert lead_outreach (draft)
    end
    API-->>UI: messages generated, failed count
```

## 5. Send email

```mermaid
sequenceDiagram
    actor U as User
    participant UI as Outreach page
    participant API as /api/send-email
    participant DB as Supabase
    participant S as SMTP
    U->>UI: Send
    UI->>API: POST outreachId, to, subject, body (signed-in session)
    API->>DB: read the message and the lead's stored contact emails
    alt recipient is not a stored contact of that lead
        API-->>UI: 403 refused
    else mail credentials missing
        API-->>UI: 503 not configured, message stays a draft
    else sent
        API->>S: send (to the test recipient if test mode)
        S-->>API: message ID
        API->>DB: status = sent, time stamped
        API-->>UI: success
    end
```
