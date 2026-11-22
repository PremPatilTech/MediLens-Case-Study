# Scope and non-goals

| | |
|---|---|
| **Belongs in** | Definition (before build) |
| **Written** | Nov 2026 (reconstructed after build) |
| **Evidence** | PRD, working app |
| **Confidence** | Derived |

## In scope for v1 (built)

Sign-in, map and radius search, deduplicated storage, website enrichment, deep enrichment of decision-makers, reachability score and tiers, account list with filters and export, detail panel, sender profiles, multi-channel message generation, email send, territory insights.

## Explicitly out of scope

| Item | Why not now |
|---|---|
| Team accounts, roles, shared pipelines | Need real multi-user demand first |
| CRM integration | Premature before the core flow is validated |
| Scheduled sequences and follow-ups | Raises spam and compliance risk; validate the manual flow first |
| Inbox and reply tracking | Needs mail-provider integration; replies are marked by hand |
| Selling or exporting contact lists as a product | Data-protection risk |
| Clinical advice or product claims in messages | Regulated; the tool only drafts introductions |
| Mobile app | Web covers the first users |
| Billing | No users yet |

## Constraint

Messages are **drafts for human review**. The product never sends without a person choosing to.
