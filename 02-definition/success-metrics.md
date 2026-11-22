# Success metrics

| | |
|---|---|
| **Belongs in** | Definition (before build) |
| **Written** | Nov 2026 (reconstructed after build) |
| **Evidence** | Proposed by the PM; nothing has been measured |
| **Confidence** | Hypothesis |

> **No metric below has been measured.** The one internal test run is too small to count. These are the targets a pilot should be judged against.

| Level | Metric | How to measure | Proposed target (to validate) |
|---|---|---|---|
| North star | Reviewed messages sent per active user per week | Messages with status *sent* | To be set after the pilot baseline |
| Activation | New user completes search, enrich and one draft within the first session | Event log (needs analytics) | 60% |
| Speed | Time from search start to first draft | Timestamps on run and draft | Under 10 minutes for 20 accounts |
| Data quality | Share of enriched accounts with a reachable contact (valid email or phone) | `leads_full` view | Set against the test run: 1 of 20 ready, 1 with a verified email |
| Message quality | Average personalisation score | `lead_outreach` | 4.0 or more *(test run: 4.0, but that single draft was the generic fallback template and 4 is the default score, so it says nothing about quality)* |
| Outcome | Reply rate on sent emails | Status *replied* (manual today) | To be set after the pilot baseline |
| Trust | Emails that bounce or are reported as spam | Mail provider | Under 2% bounce |
| Guardrail | Messages sent without a reviewed draft | Should be zero | 0 |

## What the test run tells us

20 accounts found; 7 enriched; 1 ready; 16 low; 3 skip; 1 draft message (the generic fallback template, not a personalised message); 0 sent. It shows the flow works end to end on one search. It does not show value.
