# Dependencies and cost

| | |
|---|---|
| **Belongs in** | Planning (before build) |
| **Written** | Nov 2026 (reconstructed after build) |
| **Evidence** | `package.json`, environment settings listed in the setup guide, services in the code |
| **Confidence** | Observed for dependencies; **no cost figures have been measured** |

## External dependencies

| Dependency | Used for | Failure impact | Cost model | Notes |
|---|---|---|---|---|
| Supabase | Database and sign-in | App unusable | Free tier, then usage-based | Single point of failure |
| Vercel | Hosting | App unreachable | Free tier, then usage-based | Request time limits shape the design |
| Maps search API | Finding businesses | No new accounts | Per request | Check licence terms for storing results |
| LLM API (free-tier model) | Contact extraction and writing | Deep Enrich fails; messages fall back to a generic template | Free during validation | Quality and availability vary |
| Business websites | Contact details | Account stays unenriched | Free | Sites may block automated reading |
| DNS | Email-domain check | Email marked "check failed" | Free | |
| OpenStreetMap tiles and Nominatim | Map and location names | Map or address search degrades | Free, with usage policy | Not for heavy automated use |
| Mail server (SMTP) | Sending | Send fails | Free, with daily limits | Personal accounts are limited |

## Cost model (to be measured in the pilot)

```text
cost per search     = map API calls x price per call
cost per enrichment = pages fetched x 0 (own compute) + LLM tokens x price (0 on free tier)
cost per message    = LLM tokens x price
monthly fixed       = hosting + database tier
```

**Action for the pilot:** log the number of map and LLM calls per search so cost per qualified account can be calculated, and compare it with the value of a reply.

## Capacity limits to design around

- Scrape requests up to 300 seconds and enrich or outreach up to 120 seconds, set by the hosting platform.
- Deep Enrich runs two accounts at a time.
- Gmail-style accounts cap daily sends (check the current limit before a pilot).
