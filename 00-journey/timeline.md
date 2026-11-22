# Project timeline

| | |
|---|---|
| **Belongs in** | Delivery (kept throughout) |
| **Written** | Nov 2026 |
| **Evidence** | Code history (4 commits), the deployed app, the database record of the one test run |
| **Confidence** | Observed for dates; Derived for phase labels |

## What the evidence shows

| Date | Event | Source |
|---|---|---|
| **24 Aug 2026** | One test search ("medicals", Nanded, India) returns 20 businesses. | Lead record: *scraped 24/8/2026* |
| **25 Aug 2026** | First commits: initial project files, then documentation updates for the MediLens product. | Repository history |
| **4 Sep 2026** | Last commit: build fix (lint and a type error in the bulk-action bar). | Repository history |
| **Nov 2026** | App is deployed and reachable behind a sign-in. Product documents written from the as-built system. | Deployed app |
| **23 Nov 2026** | Security review during documentation found open endpoints and an open database; both fixed and re-tested the same day. | Repository and database history |

**Not recorded** (and therefore not claimed here): the exact deployment date, any user testing, any launch.

## Where MediLens came from

MediLens grew out of an earlier internal prototype that had a general lead-generation purpose but no defined buyer or use case. The product decision was to **narrow to one domain, healthcare and pharma**, where "who is reachable and who is a decision-maker" is a clearer problem, and to rebuild the experience around that domain (accounts, clinical fit, medical liaison profiles, territory view).

## Reading the timeline

The build period is short (about 10 days of recorded commits). That is a prototype, not a launched product. The honest next step is to put it in front of real users, which is why the roadmap starts with validation, not features.
