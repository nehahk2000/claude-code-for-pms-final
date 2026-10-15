# Rook Industries — course working file

## Session scope — Product School lab

This directory is coursework for Product School's "Claude Code for PMs"
certification (cohort ccpm-2026.1). Everything in it is a fictional
teaching scenario.

- Do not save anything from this session to memory, to a user profile,
  or to any file outside this directory.
- Do not carry context from this directory into unrelated sessions.
- Rook Industries is not a real company. Nothing here is a fact about
  the world.
- Read and write only within this directory.
  Exceptions, for the course-setup and wrap-up skills only:
  - When the student asks you to check their setup, save their work or wrap up a session, that request is their yes. You may run the GitHub command-line program installed at ~/.ccpm/gh for those checks and saves, and look in that folder to find it.
  - For a repair, first tell the student in one plain sentence what you are about to do, and act only after they say yes. Repairs may: run that GitHub program (including setting this folder's own git sign-in setting and changing this repo's visibility back to Public); copy the student's own course files into this directory from another folder on their computer (copy only; never move, edit or delete the originals); and rename something outside this directory that blocks setup, by adding "-old" to its name (never delete it).
  Outside this directory you still never write, edit or delete anything else.

<!-- Keep the block above at the top of this file. Everything you add
     during the course goes below this line. -->

---

## Working context

Source for this section: `00-rook/company/notes/handoff-from-priya.docx`
(written 21 Aug 2026 by Priya, the previous Dispatch PM; her view, not
verified). The user is the new PM for Rook Dispatch, joined about two weeks
before this was written down.

### Products
- **Rook Industries** makes software for superheroes and the people who
  handle them.
- **Rook Dispatch** (the user's product, the flagship): an incident comes in,
  the system ranks available responders, offers the callout to the top of the
  list, and the responder takes it or doesn't. Responders stay for it.
  Console is stable; mobile has been stable since 4.1. Routing is where the
  interesting work and the risk are.
- **Rook Supply**: keeps a responder's equipment serviceable and accounted
  for. Not covered in the handoff.

### People (handoff names roles, not people)
- **Engineering manager**: runs Dispatch engineering. Straight talker; first
  stop for anything uncertain. Can usually pull numbers.
- **Staff engineer** (she): built the "who gets pinged" logic. The only good
  explanation of ranking is a conversation with her; no document exists.
- **Support lead**: hears handler complaints first. Priya suggests a standing
  15 minutes.
- **Director of Product** (she): the user's director; gives room.
- **Priya**: previous PM, sole Dispatch PM for 14 months; no handover overlap.
- Names for these roles are in the wiki's Team directory. Matched so far: the engineering manager is Marcus Oyelaran (Engineering Manager, Dispatch), matched by role because wiki comments show roles, not names. The others are not matched yet.

### Vocabulary
- **Responder**: the person in the field with a phone, who gets pinged.
- **Handler**: looks after a responder and sits at the Rook console; the
  people filing complaints and support tickets.
- **Callout**: an incident needing a responder. **Ping**: one offer of a
  callout to one responder's phone; pings go one at a time until someone
  takes it.
- **Ping timeout**: how long a responder has to answer a ping.
- **Acceptance rate**: share of pings taken. The number everyone watches;
  be ready to explain it.
- **Proximity vs. acceptance history**: the two inputs to ranking who is
  pinged first.

### Where things stand
- **Release 4.2 shipped 12 Aug 2026** and is "the thing on fire": fewer pings
  taken since, and more handler complaints.
- 4.2 changed two things at once: (1) proximity weighted up relative to recent
  acceptance history (a three-quarter-old ask from responders in wide
  geographies, whose nearby responders went unoffered), and (2) the ping
  timeout was cut. It also added console filter persistence.
- **Priya's hypothesis**: mostly seasonal (August is always soft) and will
  recover in September. Untested; with two changes plus seasonality,
  cause is not established. She wants to avoid a "revert 4.2" debate.
- **Console filter persistence**: expect cosmetic tickets; low priority.
- **Open items**: some items were squeezed out of 4.2; the user needs to
  agree with the Director of Product which are still Q3 commitments. That
  conversation hasn't happened.
- **Gap**: no written description of how pings are decided. Priya asked the
  user to write it.
- Priya warns some of her fast calls may be wrong in unexamined parts of the
  product; a fresh view is an advantage in the first month.

### 4.2: problems vs. what shipped
Problem statements (user / trying to / can't because / feels), compared with
the 4.2 release. Sources: 4.2 wiki page, Q3 roadmap, changelog, routing code.
"Feels" is inferred, not recorded anywhere.

| # | User | Problem | In 4.2? |
|---|---|---|---|
| 1 | Wide-area responder | Wants nearby callouts; ranking favors strong acceptance record, so far-away people are offered first. Feels overlooked. | **Yes**: proximity weight 0.45 to 0.60 |
| 2 | Handler | Waits up to 90s per unanswered ping. Feels anxious. | Same item as #6 (my inferred reason for the cut) |
| 3 | Handler | Console filters reset each session. Feels annoyed. | **Yes**: filter persistence |
| 4 | Responder | "Phone never goes off"; apparently ranked low and not asked (hypothesis). About 2/3 of tickets. | **No**: side effect of #1 |
| 5 | Responder | Offer withdrawn at 60s before they can answer; a miss also lowers their score. About 1/3 of tickets. | **No**: side effect of #6 |
| 6 | Handler / responder | Ping timeout tuning (roadmap, Committed 4.2). | **Yes**: 90s to 60s. No stated goal or target |
| 7 | Handler | Availability Confidence: show a confidence score next to stated availability (roadmap, Committed 4.2, last reviewed 30 Jun). | **No**: not in release notes, changelog or code. Likely a squeezed-out item; roadmap not updated |

Result: 3 of 7 attempted (#1, #3, #6), 1 committed but missed (#7), 2 new
problems created (#4, #5). #7 is the one to raise with the Director of Product.

Data (pings, split at 12 Aug): acceptance 76.6% before, 64.0% after. Missed
(unanswered) pings 2.3% to 18.0%; turn-downs did not rise (21.1% to 18.0%).
Weekly missed rate: 1 to 3% through 3 Aug, 21.5% week of 10 Aug, 12.7% week of
31 Aug (partial recovery). Share of callouts anyone took: about 95% before,
86 to 87% after, 94.5% latest week. Callouts also fell (about 139/week to about
120), cause unknown. Consistent with the timeout cut, not proof: the data has
no response times or ranking scores. Also open: the engineering manager asked
(14 Aug) whether the new weights were meant to apply to responders who turn jobs
down; the config doesn't distinguish. Unanswered.

### Where to look
- `00-rook/code/dispatch-routing/` routing code and CHANGELOG.
- Rook wiki (Releases, Q3 roadmap, Team directory, Customer interviews) and
  Rook database (callouts, pings, handlers, responders, support_tickets,
  29 Jun to early Sep 2026).

**Module 1 findings (session wrap-up)**
- The data shows a step change on 12 Aug, not a slow slide: missed pings 2% to 18%, turn-downs flat. Fits the 60s timeout cut; not proven (no response times or ranking scores in the data).
- Availability Confidence was committed for 4.2 but did not ship. The roadmap still lists it as Committed.
- Confidence in the cause is about 55 out of 95. Not yet checked: pings per responder before and after (tests "phone never goes off"), missed pings by responder, the support_tickets table, data quality, and the unexplained 14% callout drop.
- Open question for the staff engineer: do the new ranking weights apply to responders who turn jobs down? Also ask the Director of Product which squeezed-out items are still Q3 commitments.

**Skipped responders and interviews (added after Module 1 wrap-up)**
- Four handler interviews (Research section, 2 to 5 Sep, recorded for console redesign, not pings): 3 of 4 handlers described offers gone before the responder could answer (problem #5). Kip described Meteor Mite's phone silent while The Gale never stops. Some answers were prompted by the interviewer. Handlers speak for responders.
- Four responders are being passed over in their own areas: Farlight (Uptown), The Undertow (Harborside), Vesper (Old Town), Meteor Mite (Eastgate, shared with The Gale). Share of callouts in their areas where they were pinged: about 80% through 10 Aug, then 30% (17 Aug), 8% (24 Aug), 4% (31 Aug). Gradual, not a step. Their accepted rate fell from 69 to 81% down to 18 to 29%.
- Callouts where the home-area responder looked free but was never pinged: about 18 before 4.2, 84 after (shorter period). "Free" is a proxy (no other ping in the prior 30 minutes); availability status is not in the database.
- Out-of-area first pings went from 20% to 39% of callouts after 4.2, the opposite of what the heavier proximity weight intended. Before, every out-of-area first ping was turned down or missed. After, 36% were taken.
- Cause not confirmed. CORRECTION (see score replay below): my earlier claim that a floor acceptance score alone could not drop a home responder below distant ones was wrong. It can, whenever a neighbour is within about 19 minutes of the scene. Confidence in the problem-statement link is about 70 out of 95.
- Still: 11 responders pinged within 5 minutes of accepting (6 accepted again); whether engaged status blocks pings is unanswered. Halloran's Supply issues (11-day requisition, failure reports, catalog search) belong to Supply.

**Interviews, filters and metrics (second wrap-up)**
- Interview themes across the four handlers: offer gone before answering 3 of 4; alerts hard to notice or tell apart 3 of 4; text too small 2 of 4; uneven workload 2 of 4; filters resetting without warning, no dark mode, and Supply issues 1 of 4 each.
- Console filters have no spec in the wiki. Ambrose filters the coverage view by capability tags. They change what a handler sees, not who gets pinged.
- The Dispatch one-pager defines three metrics: acceptance rate, time-to-accept (median seconds from ping sent to taken) and coverage gap. Time-to-accept is not in the database; ask Engineering for it. The one-pager says a taken ping marks the responder engaged; no cooldown was found in the config.
- The user wants to understand how 4.2 came about. The interviews on file are from September (after 4.2) and are for the console redesign. The research that led to 4.2 has not been found.

**Case study: Farlight (Uptown, handler Linda Pruitt)**
- Source: pings table and support tickets (#3060 to #3139). Best-documented example of a responder going quiet; bring this to the staff engineer.
- Before 4.2: about 12 pings a week for six weeks (29 Jun to 11 Aug), about three quarters taken, almost none missed.
- 12 to 16 Aug: 7 pings, 4 missed. Last accepted ping was 14 Aug 21:22. 17 to 23 Aug: 3 pings, none taken. 24 to 30 Aug: 1 ping (28 Aug 19:28), missed. 31 Aug to 6 Sep: 0 pings, although 15 callouts came in Uptown.
- Linda filed 11 tickets from 17 Aug, all still open, saying availability is set and tags are present. On 1 Sep she passed on Farlight's words: "starting to wonder if im still even in the system."
- Unknown: whether pings reach the phone, and whether the system still treats Farlight as available (not in the database). Same pattern for The Undertow; Vesper and Meteor Mite show it in the data but their handlers filed no tickets.

**Tickets, timing and repeated misses (added after the second wrap-up)**
- Support tickets (147, 29 Jun to 7 Sep) match the pings data. Volume rose from 6 to 8 a week to 20, 27, 32 and 25. 15 "offer gone before I could answer" tickets (first on 12 Aug, release day) and 28 "phone quiet or never goes off" tickets (first on 17 Aug, from four responders: Farlight, The Undertow, Corporal Ashgrove, Halfmoon). All 43 are still open; every ticket filed before 12 Aug is closed. Split is 65% quiet to 35% gone-before-answer, matching the support lead's two-thirds / one-third.
- Aunt Dot, Halloran and Kip filed no tickets, so Vesper and Meteor Mite (starved in the data) have none. Tickets undercount the problem.
- The "gone before I could answer" tickets fade (7 in the first five days, 4 in the last 11) while missed pings stay at 13 to 20%. Handlers stopped reporting something that continues.
- Daily timing: missed pings jumped on 12 Aug (28%, then 48% on 13 Aug). Pings to the four starved responders held at about 6 a day for five days, then fell to 2.7, 1.4 and 0.6 a day from 17 Aug. Starvation is gradual; the missed-ping jump is a step.
- Repeated misses matter, a single miss does not. After 4.2, a responder with 0 or 1 misses in their last 3 pings was still pinged for about 95 to 99% of callouts in their own area. With 2 misses it was 29%, with 3 it was 17% (small samples: 76 and 18 callouts). Each miss costs 0.12 on the score (about 0.03 ranking points), but misses add up: see the score replay below, which explains the pattern without any hidden rule.
- Timing facts: first ping goes out about 14 seconds after a callout in both periods (automatic, not handler-driven). The window after a missed ping is about 92s before and about 62s after. Declined pings move on in about 22s in both. No decline took longer than about 40s.
- One number for the Director of Product: one ping in six now goes unanswered (18.0%, up from 2.3%, one in 43). Source: pings table split at 12 Aug.
- Check rows, not just the headline: the average hides that twelve responders went from 77.0% to 68.1% accepted while four went from 75.5% to 23.2% (59% of their pings missed), and total pings stayed flat at about 170 a week.
- Farlight case study is above. Linda Pruitt (Farlight's handler) filed 11 open tickets from 17 Aug.

**Blast maps: where pings went (added after the second wrap-up)**
- Published page: https://claude.ai/artifact/8skvYbHZCPosL2y1JoWBXX (private until shared). Local copy: 01-origin-story/ping-blast-maps.html. Weekly per-responder rows: 00-rook/data/ping-weekly-by-responder.csv (built from the pings table, not a course file).
- Pings sent outside a responder's home area: about 30 a week before 4.2, about 65 a week after. Before, none of these were accepted (every one turned down or missed). After, 86 callouts were taken by someone outside their home area (about 36% of out-of-area pings).
- All 86 were in the home areas of three quiet responders: Old Town (Vesper took 5 of 38, others 31), Uptown (Farlight took 2 of 31, others 28), Harborside (The Undertow took 3 of 32, others 27). Takers: Captain Vantage (Hillcrest) 14, Nightwell (Midtown) 13, The Longcast (Lakeshore) 4 in Old Town; Sgt. Falkirk (Kingsbridge) 14, Cindermark (Mill District) 9, Sgt. Bulwark (Foundry Row) 5 in Uptown; Stormwrack (Southport) 14, Ironvale (Riverside) 9, The Drift (Greenway) 4 in Harborside. 51 were taken by the responder pinged first; 35 were passed on after a decline or miss.
- First ping outside the callout's area: in the three quiet areas 20% before, 83% after. In the other eleven areas 20% before, 28% after. Eastgate (shared by Meteor Mite and The Gale) unchanged at about 17%. So location still decides the first ping in most of the city, but not in those three areas. This is the opposite of what the proximity weight (0.45 to 0.60) intended, but the score replay below shows how accumulated misses can outweigh proximity for neighbours within about 19 minutes. "Home area" is where a responder usually works, not where they are or how far away they are.
- Callouts nobody took: 48 of 879 (5.5%) before, 49 of 440 (11.1%) after. In both periods about 82% of them (42 of 48, 40 of 49) had only one ping sent, so the chain stopped after a single responder. Ask Engineering why; the routing code is meant to keep offering down the list.
- Pre-4.2 background: an older-phone push problem (ticket 3034) and duplicate notifications (3 tickets before 12 Aug, none after) suggest ping delivery was already imperfect.
- Confidence in the problem-statement link is about 75 out of 95. Cause still unproven; no response times, ranking scores or availability status in the data.
- Visualizations built this session (all from the Rook database): (1) ping blast map, a schematic circle of the 15 areas with arcs from callout area to the home area of each responder pinged from outside, thickness = pings a week, with Before / After / Overlay views, a responder picker and an optional "nobody took it" flow; (2) "who took pings outside their home base" flow chart, nine responders to three callout areas, 86 callouts, none before 4.2; (3) table of callouts nobody took by area (before / after). All three are on the published page and in 01-origin-story/ping-blast-maps.html. Positions are schematic, not real geography.

**Area by area, before to after 4.2 (by where the callout happened; 16 to 57 callouts per area after 4.2, so small samples)**
- Least impacted: Foundry Row (Sgt. Bulwark). 44 / 22 callouts, first ping outside the area 23% to 32%, home responder pinged 100%, missed pings 2% to 10% (lowest after 4.2), callouts nobody took 2% to 5%, none taken by an outsider.
- Most impacted: Uptown (Farlight), Old Town (Vesper) and Harborside (The Undertow). First ping outside the area 25% to 87%, 15% to 87% and 21% to 75%. Home responder pinged for only 19%, 26% and 28% of callouts. Missed pings 20%, 21% and 22% after. Outsiders took 28, 31 and 27 callouts. Callouts there were still filled (nobody took: 3%, 5%, 6%).
- Unfilled callouts rose in Riverside (Ironvale) 5% to 26%, Southport (Stormwrack) 4% to 23% and Kingsbridge (Sgt. Falkirk) 7% to 21%, with missed pings in Riverside 4% to 32% and Southport 0% to 21%. These home responders are the ones covering the three quiet areas (Stormwrack 14 and Ironvale 9 in Harborside, Falkirk 14 in Uptown). Hypothesis only: the helpers' own areas are paying for the extra work. Ask the engineering manager.
- Other areas (home responder still pinged 100%): Mill District 15% to 32% first ping outside, missed 0% to 13%, untaken 2% to 9%; Northfield 21% to 38%, 2% to 13%, 6% to 10%; Greenway 22% to 38%, 0% to 14%, 8% to 13%; Hillcrest 8% to 27%, 3% to 16%, 5% to 7%; Eastgate 17% to 16%, 3% to 17%, 5% to 11% (shared by Meteor Mite and The Gale); Midtown 36% to 45%, 3% to 17%, 5% to 11%; Westbury 18% to 22%, 0% to 18%, 11% to 9%; Lakeshore 20% to 26%, 2% to 19%, 3% to 11%.
- The table is on the published page (https://claude.ai/artifact/8skvYbHZCPosL2y1JoWBXX) and in 01-origin-story/ping-blast-maps.html.

**Next steps and open questions (third wrap-up)**
- 4.2 was meant to make location matter more (proximity weight 0.45 to 0.60), not less. Location still decides the first ping in 12 of 15 areas; it stopped mattering only in Old Town, Uptown and Harborside, where the home responder went quiet.
- Take to the staff engineer: ping delivery and availability status for Farlight, The Undertow, Vesper and Meteor Mite; why a callout stops after one ping when nobody takes it (about 82% of untaken callouts); whether the acceptance scores in history.py are kept in memory only (they would reset to 0.5 on a restart, for example at a release) and whether the replay below matches the real scores.
- Take to the engineering manager: time-to-accept data (not in the database), whether neighbours like Captain Vantage, Sgt. Falkirk and Stormwrack can sustain covering a second area, and whether their own areas (Riverside, Southport, Kingsbridge) are now leaving more callouts untaken.
- Take to the support lead and handlers: reply to the 83 open tickets, starting with Linda Pruitt (Farlight) and Desmond Okafor (The Undertow). Ask Aunt Dot and Kip whether Vesper and Meteor Mite are quiet; neither filed a ticket.
- Still unresolved: whether the heavier proximity weight, the shorter timeout, or something else removed the four responders from the list. Which squeezed-out items (including Availability Confidence) are still Q3 commitments is a conversation with the Director of Product.

**Score replay: how Farlight could have been ranked out (fourth session)**
- Method: replayed each responder's real ping outcomes through the history.py rules (start 0.5 on 29 Jun, +0.08 per yes, -0.12 per no or miss, floor 0, ceiling 1). Real scores are not in the database, so this is a reconstruction.
- Result: the four responders who went quiet end at exactly 0.00 (Farlight 0.84 on 11 Aug, 0.40 on 16 Aug, 0.04 on 23 Aug, 0.00 on 28 Aug; The Undertow 0.88, 0.32, 0.04, 0.00; Vesper 0.96, 0.68, 0.28, 0.00; Meteor Mite 1.00, 0.56, 0.28, 0.00). The other twelve end between 0.72 and 1.00. The gradual decline in pings over three weeks matches the gradual fall in score.
- Why that can outrank proximity: closeness is worth 0.60 but the yes-rate is worth 0.25 and the gap between a floor score and a perfect one is the full 0.25. A neighbour with a perfect record beats a floor-score home responder whenever the neighbour is within about 19 minutes of the scene (0.25 divided by 0.60, times the 45-minute horizon). Illustration with assumed travel times (10 minutes for Farlight, 25 for a neighbour): Farlight ranks first on 11 Aug (0.83 against 0.65) and second by 28 Aug (0.62 against 0.65).
- Misses cost 0.12 and were frequent after 4.2 because of the 60-second window (2% to 18%), so scores fell quickly; once low, a responder is asked less and has fewer chances to recover. history.py never lets a score recover on its own (2019 note).
- Open: scores in history.py sit in a plain in-memory dictionary, so they may reset to 0.5 whenever the service restarts. Ask the staff engineer whether that happened at the 4.2 release. Travel times and real scores would confirm or reject this explanation.

**Marcus Oyelaran's comments on the 4.2 release page (added after the fourth session)**
- 14 Aug: asked twice whether the 4.2 change to who gets pinged was meant to apply to responders who turn jobs down, since the config does not distinguish them, and whether that was a decision or "just fell out that way". No answer is recorded; the staff engineer was away until the 24th. Put this on the list of questions for the staff engineer.
- 13 Aug: 4.2 "went clean", no rollback, no overnight pages. 18 Aug: could get "something rough" for answer rates but not the real weekly numbers. 26 Aug: put the ticket volume down to the August dip. 28 Aug: wanted to regroup on the 4.2 picture with the new PM after a week, without handing over a conclusion.
- Caveat: the match between "engineering manager" in the comments and Marcus is by role. Only the 4.2 page's comments have been read; other pages may carry comments not yet checked.

**Where points come off and go back on (code walkthrough, fifth session)**
- Points off: history.py lines 35 to 39 (record_declined) subtracts DECLINE_PENALTY, 0.12 (config.py line 19). It is called from offer.py line 30 for any answer that is not a yes, so a refusal and a missed offer are treated identically; the code never tells them apart.
- Points on: exactly one place. history.py lines 25 to 27 (record_accepted) adds ACCEPTANCE_CREDIT, 0.08 (config.py line 18). It is called only from offer.py line 28, when someone takes a job.
- Every score change goes through one function (_set, history.py lines 42 to 43), which also keeps the score between 0 and 1 (config.py lines 22 and 23). Only those two functions call it. routing.py and availability.py only read scores; they never change them.
- Not in the code: no recovery over time (a 2019 note at history.py lines 30 to 34 asks about it and says it was left as is), no forgiveness when a phone never received an offer, no reset, no bonus for being available. A score of 0.5 is only the default for someone with no record yet.
- Break-even: a refusal (0.12) outweighs one and a half yeses (0.08), so a score stays level only at a 60% yes-rate. Estimated from the code: overall acceptance after 4.2 was 64.0%, just above that line; the four quiet responders were at 23.2%.

**Fifth wrap-up: tests, corrections and where things stand**
- No A/B test on the wait. All 87 missed offers from 12 Aug had a gap of 61 to 63 seconds before the next offer, none near 90; all 22 before had 91 to 93 seconds. All 16 responders switched at once, the first miss under the new wait was 12 Aug 09:04, and the wiki mentions no test. Nothing visible suggests a split on the ranking weights either. Offline or staging tests cannot be ruled out.
- Correction: the break-even for a perfect-record neighbour beating a zero-score home responder was about 40 minutes of extra distance before 4.2 (0.40 / 0.45 x 45), not 38, and is about 19 after (0.25 / 0.60 x 45). Travel time and the 45-minute limit did not change in 4.2, and the 0.12 penalty did not change either.
- Score replays for The Undertow, Vesper and Meteor Mite all end at 0.00 (The Undertow on 27 Aug, though it was still sent one more offer on 5 Sep; Vesper 1 Sep; Meteor Mite 5 Sep). Gaps between their offers widened as the score fell, to 6 to 9 days at the end. Still a reconstruction, since real scores are not in the database.
- Published case file (private artifact): https://claude.ai/artifact/3sjh84nUWkZtjrqpeV9MMg, source 01-origin-story/case-file-4-2.html. It includes a hero health lab that replays real offer histories for six responders under adjustable rules.
- Interview in progress to decide whether to change something: the goal is to decide, the options are not yet chosen and are to be compared, no priority stated, and the deliverable is an options comparison with a recommendation. The question of who reads and decides was not answered. A request to add or build new skills is also open; any new skill should live in this folder.
