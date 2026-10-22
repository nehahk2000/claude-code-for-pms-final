# Keeping heroes in the game
*Brief for Helen. (P) = proposal, not agreed. Dates are proposed.*

**Owner:** the PM for Rook Dispatch (me). Helen decides; Marcus and the staff engineer build.
**In scope:** spotting quiet heroes, telling them they're still in, points that can recover, testing the wait (90s vs 60s). **Out:** closeness and skills weights, Supply, a handler phone app.

## 1. Executive overview
After 4.2, some heroes stopped getting jobs and no one told them or us. Nothing goes live until a replay on old data.

## 2. Decisions we need from Helen
| When | Decision |
|---|---|
| Today | Agree the goal: no hero goes quiet unseen |
| Today | Approve the replay (no live change); name owners for it and for ticket replies |
| 13 Nov | Approve the live tests below; set targets and a date |

## 3. Decisions we need from other teams
- **Engineering:** run the "check first" items, build the replay, say if the wait can be set by area.
- **Support:** answer the 43 open tickets by 29 Oct, Farlight's handler first. I draft, Support sends.
- **Design:** draw Kip's view (draft below).

## 4. The problem and why it matters
A missed offer costs a hero 0.12 points, a yes earns 0.08, and points never come back. Fewer points meant fewer offers, so heroes could not climb back. No test ran on 4.2, so the cause is unproven, though this fits the data. (Estimate: real points aren't stored.)

**Check first** (if bad, a points fix may not help): do offers reach the phone? do points reset on restart? can we send a text? Owner: staff engineer, 30 Oct.

## 5. The ideal state and the plan
**Ideal state:** a quiet hero is flagged on day 3 and told they're still in. Quiet = no offer in their own area for 3 days, or last 3 offers missed.

**Kip's banner (example, Meteor Mite's real history):** "Meteor Mite has gone quiet. Offers this week: 1 (usual: about 11). Last accepted: 19 Aug. Last 3: no, missed, missed. Check phone / Mark away / Tell Product."

**Farlight's text** (arrives even if offers don't): "You haven't had an offer in Uptown for a few days. It isn't about anything you did. You're still in rotation, and we're fixing how offers reach you. If your phone has been quiet, tell your handler."

**The 2019 question** (Wen: "should this ease back toward NEUTRAL_SCORE on its own?"):
| Option | Effect |
|---|---|
| A. Leave it | Heroes keep fading out |
| B. A miss costs less than a no (the phone may never have rung) | Points still never come back |
| **C. B, plus points drift back, a lowest score, next own-area job** | Recommended; the replay sets the numbers (P) |

| Test | Where | Pass | Stop if |
|---|---|---|---|
| 1. Banner, text, option C | Uptown, Old Town, Harborside | No hero reaches day 7 without an offer (P) | Jobs nobody took top 12% there for a week (P) |
| 2. Wait: 90s vs 60s | Two non-quiet areas, picked by Marcus by 6 Nov | Fewer missed offers, no rise in jobs nobody took (P) | Jobs nobody took rise two weeks running (P) |

Different areas, so we can tell which test worked.

## 6. Supporting data
| | Before 4.2 | After 4.2 |
|---|---|---|
| Offers nobody answered | 2.3% | 18.0% |
| Jobs nobody took | 5.5% | 11.1% |
| Farlight, offers a week | about 12 | 0 (week of 31 Aug) |
| Offers missed, 4 hardest-hit heroes | 2.9% | 59% |

Farlight's handler filed 11 tickets; all still open.

## 7. Open questions and owners
| Question | Owner | By |
|---|---|---|
| Why do 82% of unfilled jobs stop after one offer? | Staff engineer | 6 Nov |
| Do the new weights apply to heroes who turn jobs down? (Marcus asked 14 Aug) | Staff engineer | 6 Nov |
| Can neighbours keep covering, and are their areas getting worse? | Marcus | 6 Nov |
| Clickable version of Kip's view | Me, with Design | Before live tests |
