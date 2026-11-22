# User stories and acceptance criteria

| | |
|---|---|
| **Belongs in** | Definition (before build) |
| **Written** | Nov 2026 (reconstructed after build) |
| **Evidence** | PRD requirements |
| **Confidence** | Derived |

Format: **As a** role, **I want** goal, **so that** benefit. Acceptance criteria use Given / When / Then.

### US-01 Find accounts nearby (FR-03, FR-04)
As a territory rep, I want to search a sector within a radius of my location so that I get a list of local accounts.
- Given I am signed in and my location is detected, when I enter "pharmacies" and a 5 km radius and start the search, then accounts within that radius appear in my Intelligence list.
- Given my location cannot be detected, when I open the search page, then I can search for a city or address instead.
- Given a business appears twice in results, then it is stored once.

### US-02 See who is reachable (FR-07, FR-10, FR-11)
As a rep, I want each account scored so that I contact the best ones first.
- Given an account has a valid email, a phone and a working website, then its score is higher than an account with only a phone.
- Given an account has no website, then its score is never above 25. *(Not true in the live app today; see limitation L-07.)*
- Given a score of 75 or more, then the tier is **Ready**; 50 to 74 is **Review**; 25 to 49 is **Low**; below 25 is **Skip**.

### US-03 Find the decision-maker (FR-08)
As a liaison, I want named decision-makers for an account so that I address a person, not an inbox.
- Given an account has a website with a team or contact page, when I run Deep Enrich, then any named people with a title appear with their published email, phone or LinkedIn.
- Given nothing is found, then the panel says so, and the crawl status explains why (for example blocked or no website).

### US-04 Keep my voice (FR-15, FR-16, FR-17)
As a liaison, I want messages generated from my profile and tone so that they sound like me.
- Given I have a default profile, when I generate outreach for an account, then a draft is created in the chosen channel and tone.
- Then the draft shows a personalisation score from 1 to 5.
- Given I generate again for the same account, profile and channel, then the draft is replaced, not duplicated.

### US-05 Review before sending (FR-18)
As a rep, I want to read and edit every message before it leaves so that nothing embarrassing is sent.
- Given a draft exists, when I open it, then I can edit the text and copy it.
- Given I mark it sent, then its status becomes *sent* with a timestamp.

### US-06 Send from the app (FR-19)
As a rep, I want to send an email without leaving the app so that follow-up is easy.
- Given mail credentials are configured, when I send, then the email goes out and the message is marked sent.
- Given credentials are missing, then the app says the email was **not** sent and leaves the message as a draft. *(Fixed 23 Nov 2026.)*
- Given a recipient that is not a stored contact of that lead, then the send is refused. *(Added 23 Nov 2026.)*
- Given test mode is on, then the email goes to the test recipient only.

### US-07 Understand my territory (FR-20)
As a distributor owner, I want a summary of my accounts so that I see where to focus.
- Given I have accounts, when I open Insights, then I see the tier mix, sector mix, geography and top accounts.
