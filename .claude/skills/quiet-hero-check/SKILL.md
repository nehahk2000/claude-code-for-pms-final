---
name: quiet-hero-check
description: Finds heroes (responders) who have gone quiet in Rook Dispatch, meaning they have stopped getting offers or keep missing them. Use it when the user asks "who has gone quiet", "any quiet heroes", "run the quiet hero check", "which responders are being skipped", or wants an early warning on responders. It reads the Rook database only, flags each quiet hero with the reason, and never changes data or messages anyone.
---

# Quiet hero check

You're helping the PM for Rook Dispatch spot responders (heroes) who have gone quiet before anyone has to file a ticket. Keep every message short and plain. Say what you found, not how you found it.

## What "quiet" means

A hero is **QUIET** if either is true as of the check date:
1. **No own-area offer for 3 days.** The hero's last offer for a callout in their own area (the area on their responder record) was 3 or more days before the check date.
2. **Last 3 offers all missed.** Their three most recent offers were all missed (nobody answered in time).

A hero is on the **WATCH** list if they are not quiet but got fewer than half their usual offers in the last 7 days.

These are the thresholds from the brief for Helen. The user may change them ("use 4 days", "last 2 missed"). Use what they say and state it in the report.

## Before you start

- You need the **rook-database** connector (the `run_query` tool). If it isn't available, say so in one sentence and stop. Do not guess from memory or from files.
- The database only runs to early September 2026. Use that, not today's date, as the check date unless the user gives one.
- Everything is read-only. Never write, message, or change anything.

## Steps

1. **Pick the check date and the baseline.**
   - Check date: the user's date, or the day after the latest offer (`select max(sent_at) from pings`).
   - Baseline ("usual" offers a week): the first 6 weeks of data, 29 Jun to 10 Aug 2026, because release 4.2 shipped on 12 Aug. The user may override this.
2. **Run the query** below, filling in `{ASOF}` (check date and time), `{BASELINE_END}` (end of the baseline) and `{BASELINE_WEEKS}` (weeks in the baseline, 6 for the default).
3. **Apply the rules** to each row:
   - QUIET if `days_since_own_area_offer` is 3 or more, or `last3_all_missed` is true.
   - WATCH if not quiet and `offers_last_7d` is under half of `usual_per_week`.
   - A hero with no own-area offer at all in the data counts as QUIET, with the reason "no own-area offer on record".
4. **Optional cross-check with tickets.** If the user wants it, count open support tickets per quiet hero:
   `select about_responder, count(*) filter (where status = 'open') as open_tickets, to_char(min(filed_at), 'MM-DD') as first_ticket from support_tickets group by 1`
   Say whether tickets agree, disagree or are silent for each quiet hero. Silence is information: a handler who filed nothing may not know.
5. **Report** in the format below.

## The query

```sql
with p as (
  select p.responder, p.sent_at, p.outcome, (c.area = r.area) as own_area
  from pings p
  join callouts c using (callout_number)
  join responders r on r.name = p.responder
  where p.sent_at <= timestamp '{ASOF}'
), ranked as (
  select responder, outcome,
         row_number() over (partition by responder order by sent_at desc) as rn
  from p
), last3 as (
  select responder,
         string_agg(case outcome when 'taken' then 'yes' when 'turned down' then 'no' else 'missed' end, ', ' order by rn desc) as last3,
         count(*) = 3 and bool_and(outcome = 'missed') as all_missed
  from ranked where rn <= 3 group by responder
)
select r.name as responder, r.area, r.handler,
       to_char(max(p.sent_at), 'MM-DD HH24:MI') as last_offer,
       to_char(max(p.sent_at) filter (where p.outcome = 'taken'), 'MM-DD HH24:MI') as last_accepted,
       round(extract(epoch from (timestamp '{ASOF}' - max(p.sent_at) filter (where p.own_area))) / 86400.0, 1) as days_since_own_area_offer,
       count(p.responder) filter (where p.sent_at > timestamp '{ASOF}' - interval '7 days') as offers_last_7d,
       round(count(p.responder) filter (where p.sent_at < timestamp '{BASELINE_END}') / {BASELINE_WEEKS}.0, 1) as usual_per_week,
       l.last3, coalesce(l.all_missed, false) as last3_all_missed
from responders r
left join p on p.responder = r.name
left join last3 l on l.responder = r.name
group by r.name, r.area, r.handler, l.last3, l.all_missed
order by r.name
```

## Report format

```
quiet-hero-check
As of <date>. Quiet = no own-area offer for <3> days, or last <3> offers all missed. Usual = <baseline>.

QUIET (<n>)
  <Hero> (<area>, handler <name>): <reason>
     offers this week <n> (usual <n>) · last accepted <date> · last 3: <no, missed, missed>
     tickets: <n open, first on date> / none filed
WATCH (<n>)
  <Hero>: <offers this week> against <usual>
FINE: <n> heroes.

<one plain sentence: who to look at first and why>
```

Rules for the report:
- Order QUIET by days without an own-area offer, longest first.
- Always show the handler's name so the PM knows who to ask.
- Give counts as numbers, not adjectives.
- Close with one line on what you cannot see: **availability status and whether offers reach phones are not in the database**, so "quiet" means "not being offered or not answering", not "unavailable".
- Heroes who share an area (Meteor Mite and The Gale share Eastgate) are each checked against that area. Say so if it matters.
- If nobody is quiet, say that in one line.

## After the report

Offer, but don't do unasked: drafting a note to the handler, a reply to an open ticket, or an entry for the Product inbox. Don't send or save anything outside this folder.

## Worked example (so your calls match)

Check date 7 Sep 2026, baseline 29 Jun to 10 Aug (6 weeks). Result: **QUIET (4)**: Farlight (15.5 days without an Uptown offer; 0 offers this week against about 12), Meteor Mite (8.6 days; 1 against about 11), Vesper (5.5 days; 1 against about 14) and The Undertow (last 3 offers all missed; 1 against about 12). No hero on WATCH. The other 12 are fine.
