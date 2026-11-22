# Launch checklist and go/no-go

| | |
|---|---|
| **Belongs in** | Launch (before launch) |
| **Written** | Nov 2026 |
| **Evidence** | Known limitations, risk register |
| **Confidence** | Derived. **The product has not launched.** |

## Gate 1: Safe to show (before any outside user)

- [x] Every API route requires a session (L-01), tested 23 Nov 2026
- [x] Database closed to the public key; sign-ups closed (L-16, L-17), tested 23 Nov 2026
- [x] Safe redirects, security headers, locked email endpoint (L-18 to L-20)
- [ ] Per-user data ownership and policies (L-02)
- [x] Setup scripts create every table (L-06), migration added
- [ ] Fallback messages flagged; no fake score (L-04)
- [x] Nothing is marked sent unless it was sent (L-05)
- [ ] Legacy names removed from outgoing text (L-12; sender name done, fallback signature open)
- [ ] One score formula (L-07)
- [ ] Rate limits (L-22)
- [ ] Tests for scoring and API access passing

## Gate 2: Allowed to use (before real contact data)

- [ ] Privacy notice and terms of use
- [ ] Unsubscribe line and suppression list
- [ ] Deletion on request
- [ ] Retention rule agreed
- [ ] Target countries confirmed and reviewed by an adviser
- [ ] Provider terms checked

## Gate 3: Ready for a pilot

- [ ] 3 pilot users and a real territory each
- [ ] Analytics events in place (search started, draft made, sent, replied)
- [ ] Cost per search and per draft logged
- [ ] Support contact and a feedback form
- [ ] UAT signed off ([`uat-checklist.md`](uat-checklist.md))

## Go / no-go rule

**Go** only when every item in Gates 1 and 2 is ticked. Gate 3 starts the pilot.

## Rollback

Hosting keeps earlier deployments; roll back to the last good deployment if a release breaks sign-in, search or send. Database changes ship as numbered migrations so they can be reversed.

## Communication

Tell pilot users plainly: what the product does, that messages are drafts they send, what data is collected, and how to ask for deletion.
