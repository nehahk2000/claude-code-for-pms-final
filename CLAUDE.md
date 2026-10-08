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
- Names for these roles are in the wiki's Team directory, not yet matched here.

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
- Cause unproven. By my reading of the code, a floor acceptance score alone should not drop a home responder below distant ones at proximity weight 0.60, so ask the staff engineer about availability status, travel-time estimates and capability tags for these four. Confidence in the problem-statement link is about 70 out of 95.
- Still: 11 responders pinged within 5 minutes of accepting (6 accepted again); whether engaged status blocks pings is unanswered. Halloran's Supply issues (11-day requisition, failure reports, catalog search) belong to Supply.

**Interviews, filters and metrics (second wrap-up)**
- Interview themes across the four handlers: offer gone before answering 3 of 4; alerts hard to notice or tell apart 3 of 4; text too small 2 of 4; uneven workload 2 of 4; filters resetting without warning, no dark mode, and Supply issues 1 of 4 each.
- Console filters have no spec in the wiki. Ambrose filters the coverage view by capability tags. They change what a handler sees, not who gets pinged.
- The Dispatch one-pager defines three metrics: acceptance rate, time-to-accept (median seconds from ping sent to taken) and coverage gap. Time-to-accept is not in the database; ask Engineering for it. The one-pager says a taken ping marks the responder engaged; no cooldown was found in the config.
- The user wants to understand how 4.2 came about. The interviews on file are from September (after 4.2) and are for the console redesign. The research that led to 4.2 has not been found.
