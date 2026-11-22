# Assumptions and constraints

| | |
|---|---|
| **Belongs in** | Planning (before build) |
| **Written** | Nov 2026 (reconstructed after build) |
| **Evidence** | Product behaviour and PM judgement |
| **Confidence** | Hypothesis (assumptions), Observed (constraints) |

## Assumptions (each is a risk until tested)

| ID | Assumption | How to test |
|---|---|---|
| A-01 | Healthcare sales teams struggle to find reachable decision-makers. | 5 to 8 interviews |
| A-02 | Public website data is enough to find useful contacts for small clinics and pharmacies. | Pilot: share of accounts with a valid email or named person |
| A-03 | Users will accept AI-written drafts if they can edit them. | Edit rate and send rate in the pilot |
| A-04 | A single reachability score helps users choose whom to contact first. | Do users sort and act on it? |
| A-05 | Small teams will pay for this instead of using free maps and manual research. | Pilot pricing conversation |
| A-06 | Messages sent from a normal mail account will reach inboxes. | Bounce and spam rates |
| A-07 | The target markets allow this kind of outreach under local rules. | Legal review per country |

## Constraints

| ID | Constraint |
|---|---|
| C-01 | Long operations must finish inside the hosting platform's time limits. |
| C-02 | The prototype uses free tiers; capacity and reliability follow. |
| C-03 | Many business sites block automated readers, so enrichment is partial by nature. |
| C-04 | Personal contact data is regulated; the product must support opt-out and deletion before real use. |
| C-05 | Messages must stay as drafts for human review. |
