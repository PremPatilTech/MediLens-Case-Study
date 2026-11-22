# Roadmap and prioritisation

| | |
|---|---|
| **Belongs in** | Definition (before and after launch) |
| **Written** | Nov 2026 (reconstructed after build) |
| **Evidence** | Known gaps from the code review; PM judgement |
| **Confidence** | Hypothesis (proposed, not committed) |

## Prioritisation method

**MoSCoW** for the next release, **RICE** (Reach x Impact x Confidence / Effort) to order the Should-haves. Scores are the PM's estimates for discussion, not measured data.

## Roadmap (proposed)

| Phase | Theme | Items | Exit test |
|---|---|---|---|
| **0. Make it safe** | Fix what blocks any real use | **Done 23 Nov 2026:** sign-in on all API endpoints, row-level security, closed sign-ups, safe redirects, security headers, locked email endpoint, missing table. **Still to do:** rate limits, per-user ownership, one score formula, remove legacy naming, framework upgrade. | A person who is not signed in gets 401 on every endpoint (met). A new setup runs from the scripts alone (migration added). |
| **1. Validate** | Learn before building | 5 to 8 user interviews. Supervised pilot with 3 users on real territories. Basic event analytics. | Metrics in [`success-metrics.md`](success-metrics.md) have baselines. |
| **2. Trust and compliance** | Be allowed to use it | Opt-out and deletion workflow. Suppression list. Unsubscribe line in email. Data retention rules. | Privacy checklist passes. |
| **3. Improve the loop** | Based on pilot findings | Better decision-maker accuracy. Reply tracking. Saved searches. Template library. | Pilot users reach the north-star metric. |
| **4. Scale** | Only if phase 3 works | Teams and roles. CRM sync. Scheduled follow-ups with limits. | Requested by pilot users who would pay. |

## MoSCoW for the next release

| | Items |
|---|---|
| **Must** | Rate limits, per-user ownership, one score formula, opt-out and deletion, unsubscribe line, tests for scoring and API access, framework upgrade. *(API authentication and the missing table were done 23 Nov 2026.)* |
| **Should** | Analytics events, consistent average-score labelling, saved searches |
| **Could** | Template library, CSV import of existing accounts |
| **Won't (now)** | Teams, CRM sync, sequences, mobile app, billing |

## RICE for the Should and Could items (estimates)

| Item | Reach | Impact | Confidence | Effort (person-weeks) | RICE |
|---|---|---|---|---|---|
| Analytics events | 100% | 2 | 90% | 2 | 90 |
| Saved searches | 70% | 1 | 70% | 3 | 16 |
| Template library | 50% | 1 | 60% | 3 | 10 |
| CSV import | 30% | 2 | 50% | 4 | 7.5 |

Reach is the share of expected users; Impact runs 0.25 to 3; Confidence 0 to 100%.
