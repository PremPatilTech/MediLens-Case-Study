# Risk register

| | |
|---|---|
| **Belongs in** | Delivery (kept throughout) |
| **Written** | Nov 2026 (reconstructed after build) |
| **Evidence** | Code review, no-side-effect tests of the deployed endpoints and database, the provider security scanner, general practice |
| **Confidence** | Observed (R-01, R-06, R-08), Derived or Hypothesis for the rest |

Likelihood and impact: **H** high, **M** medium, **L** low.

| ID | Risk | L | I | Mitigation | Owner | Status |
|---|---|---|---|---|---|---|
| R-01 | API endpoints answered without sign-in (cost, LLM use, email send, data read). Database also open to the public key; sign-up open | H | H | Session required on every route; row-level security; public key revoked; sign-ups closed; all re-tested | Engineering, PM tracks | **Closed 23 Nov 2026** (rate limits and per-user ownership remain, see R-13) |
| R-02 | Contact data of individuals is collected and stored without a lawful basis, notice, opt-out or deletion | M | H | Privacy and compliance plan; deletion and suppression features; legal review | PM | Open |
| R-03 | Emails go out without consent rules (unsubscribe, sender identity) or are marked spam | M | H | Unsubscribe line, suppression list, sending limits, warm-up; human review kept | PM | Open |
| R-04 | Contact data is wrong or stale, so users contact the wrong person | H | M | Confidence shown per contact; MX check; user feedback loop | Engineering | Partly mitigated |
| R-05 | Third-party terms: reading websites, using map data, LLM provider terms | M | M | Respect blocking and robots signals; review each provider's terms before launch | PM | Open |
| R-06 | LLM output fails and a generic template is saved as if personalised (it scores 4 by default) | H | M | Flag fallback messages; do not give them a score; show a warning | Engineering | **Open** |
| R-07 | Platform limits or outages (hosting time limits, free tiers, a single database) | M | M | Move long jobs to background workers; monitor; paid tiers for a pilot | Engineering | Open |
| R-08 | No automated tests, so scoring or enrichment changes break silently | H | M | Tests for the scoring function and extractors before the next change | Engineering | **Open** |
| R-09 | Messages marked *sent* when nothing was delivered, which corrupts reply-rate metrics | M | M | Leave as draft when credentials are missing | Engineering | **Closed 23 Nov 2026** |
| R-10 | Product risk: no real user has used it; demand is unproven | H | H | Interviews and a supervised pilot before any new features | PM | Open |
| R-11 | Setup breaks for new environments (a table missing from the setup script) | H | L | Migration added and applied; add a setup test | Engineering | **Mitigated 23 Nov 2026** |
| R-12 | Documentation disagrees with the code (tiers, providers, branding leftovers), which erodes trust | M | L | Single source of truth in this repository; align the code | PM | In progress |

| R-13 | No per-user data ownership and no rate limits: acceptable only while sign-up is closed and one tenant uses the product | M | H | Add a user or workspace ID, per-user policies and rate limits before a second customer | Engineering | Open |
| R-14 | Web framework version has 7 known advisories (all need a major upgrade) | M | M | Plan the upgrade with regression tests; track advisories | Engineering | Open |
| R-15 | Two score formulas; the live one lacks the no-website cap (L-07) | M | M | One formula, defined once | Engineering | Open |

## Review rhythm

Weekly while a pilot runs; before any release. R-01 gated everything else and is now closed; R-02 and R-03 gate the pilot.
