# RACI and roles

| | |
|---|---|
| **Belongs in** | Planning (before build) |
| **Written** | Nov 2026 (reconstructed after build) |
| **Evidence** | Project history; roles as they were played |
| **Confidence** | Derived |

**R** = responsible (does the work), **A** = accountable (final say), **C** = consulted, **I** = informed.

The team was small: one product and project manager and an engineering team. Roles that a larger team would have (design, QA, data protection, marketing) were not staffed, and the gaps are marked.

| Activity | PM | Engineering | Not staffed |
|---|---|---|---|
| Problem framing and domain choice | A, R | C | |
| Requirements and acceptance criteria | A, R | C | |
| Prioritisation and scope | A, R | C | |
| UX and visual design | A (direction) | R (implementation) | Dedicated designer |
| Architecture and technology choices | C | A, R | |
| Implementation | I | A, R | |
| Testing and QA | A | R (manual) | Dedicated QA; no automated tests |
| Privacy and legal review | A (raise) | C | **Data-protection or legal reviewer** |
| Deployment and hosting | A | R | |
| Risk tracking | A, R | C | |
| Documentation | A, R | C | |
| User research and pilot | A, R | I | Researcher |

## What I would add before a pilot

A part-time **data-protection reviewer** (compliance for contact data), a **QA owner** for the scoring and enrichment logic, and a **designer** for the first-run experience.
