---
name: release-impact
description: Reports and logs how hero (responder) behaviour changed after a Rook Dispatch release or setting change. Use it when the user says "what did release X do", "how did offers change after the deploy", "run release impact", "log this release", or names a release date or a change such as the ping wait going from 90 to 60 seconds. It compares before and after in the Rook database, tests simple alternative explanations, writes a short log entry in this folder's release-log, and reports in plain English. It reads the database only.
---

# Release impact

You're helping the PM for Rook Dispatch see what a release or setting change did to heroes and their offers, and keep a record of it. Keep messages short and plain. Compute, don't estimate. Follow the rules of the `verify-numbers` skill for every figure: recompute from the database, cross-check, label each claim observed, estimated or guessed, and give a confidence from 0 to 95.

## What you need from the user

- **The release date and time** (for example "12 Aug 2026"). If they give only a date, use 00:00 that day and say so.
- **What changed** in one line (for example "ping wait cut from 90 to 60 seconds"). If they don't say, read `00-rook/code/dispatch-routing/CHANGELOG.md` and the release's page in the Rook wiki (Releases database) and list what shipped. Ask one short question only if you still can't tell which release they mean.
- Optional: which measures to focus on.

## Before you start

- You need the **rook-database** connector (`run_query`). If it isn't available, say so in one sentence and stop. Do not work from memory or files.
- The data covers 29 Jun to 6 Sep 2026 (tickets to 7 Sep). A release outside that range can't be measured: say so.
- Read-only on the database. The only thing you write is the log entry in `release-log/` inside this folder.

## Steps

1. **Set the windows.** Before = start of the data to the release. After = the release to the end of the data. They are usually different lengths, so use **rates per week**. Also run an **equal-length comparison** (the same number of days before as after, ending at the release and starting at it) to reduce the effect of trends and seasons. State both.
2. **Run the queries** below (fill in `{RELEASE}`, `{BW}` and `{AW}`, the weeks before and after from query 1).
3. **Test the simple alternatives** and report each one:
   - **A step or a slide?** Look at the daily series around the release (query 7). A real release effect usually shows as a step on the day.
   - **Everyone at once?** If the change is a setting, the offer-to-next-offer gap after a missed offer should switch from the old value to the new value for every hero on the same day (query 3). A mixture means a staged rollout or a test.
   - **Time of day** (query 6), **geography** (query 5), and **a few heroes hiding in the average** (query 4).
   - **Seasonality:** the data has no earlier year, so say it can't be ruled out.
   - **Other changes shipped the same day:** list every one. If there are others, say the data cannot separate them.
4. **Triangulate.** Count support tickets by week and theme before and after (query 8) and say whether tickets agree, disagree or are silent. If it helps, check the handler interviews and the wiki release comments.
5. **Write the log entry** (see below).
6. **Report** in the format below.

## The queries

Query 1: overall before and after (offers, outcomes, weeks).
```sql
with w as (
  select 'before' as period, extract(epoch from (timestamp '{RELEASE}' - (select min(sent_at) from pings))) / 604800.0 as weeks
  union all
  select 'after', extract(epoch from ((select max(sent_at) from pings) - timestamp '{RELEASE}')) / 604800.0
)
select case when p.sent_at < timestamp '{RELEASE}' then 'before' else 'after' end as period,
       round(max(w.weeks)::numeric, 2) as weeks, count(*) as offers,
       round(count(*) / max(w.weeks)::numeric, 1) as offers_per_week,
       count(*) filter (where p.outcome = 'taken') as yes,
       count(*) filter (where p.outcome = 'turned down') as no,
       count(*) filter (where p.outcome = 'missed') as missed,
       round(100.0 * count(*) filter (where p.outcome = 'taken') / count(*), 1) as yes_pct,
       round(100.0 * count(*) filter (where p.outcome = 'turned down') / count(*), 1) as no_pct,
       round(100.0 * count(*) filter (where p.outcome = 'missed') / count(*), 1) as missed_pct
from pings p
join w on w.period = case when p.sent_at < timestamp '{RELEASE}' then 'before' else 'after' end
group by 1 order by 1 desc
```

Query 2: jobs nobody took, and offers per job.
```sql
with tk as (select distinct callout_number from pings where outcome = 'taken'),
c as (select c.callout_number, c.happened_at,
             (c.callout_number in (select callout_number from tk)) as taken,
             (select count(*) from pings p where p.callout_number = c.callout_number) as n
      from callouts c)
select case when happened_at < timestamp '{RELEASE}' then 'before' else 'after' end as period,
       count(*) as callouts,
       count(*) filter (where not taken) as nobody_took,
       round(100.0 * count(*) filter (where not taken) / count(*), 1) as nobody_took_pct,
       round(avg(n), 2) as offers_per_callout,
       count(*) filter (where not taken and n = 1) as nobody_took_one_offer
from c group by 1 order by 1 desc
```

Query 3: the gap before the next hero is offered the job (shows the wait setting, and whether everyone switched at once).
```sql
with p as (
  select callout_number, responder, outcome, sent_at,
         lead(sent_at) over (partition by callout_number order by sent_at) as next_sent
  from pings
)
select case when sent_at < timestamp '{RELEASE}' then 'before' else 'after' end as period,
       outcome as previous_outcome, count(*) as n,
       round(min(extract(epoch from (next_sent - sent_at)))::numeric, 0) as min_sec,
       round((percentile_cont(0.5) within group (order by extract(epoch from (next_sent - sent_at))))::numeric, 0) as median_sec,
       round(max(extract(epoch from (next_sent - sent_at)))::numeric, 0) as max_sec,
       count(distinct responder) as heroes
from p where next_sent is not null and outcome in ('missed', 'turned down')
group by 1, 2 order by 1 desc, 2
```
A refusal gap shows how fast heroes answer; a missed gap equals the wait plus a second or two. Read the median and the range together.

Query 4: each hero before and after (replace `{BW}` and `{AW}` with the weeks from query 1).
```sql
select responder,
       round(count(*) filter (where sent_at < timestamp '{RELEASE}') / {BW}, 1) as offers_wk_before,
       round(count(*) filter (where sent_at >= timestamp '{RELEASE}') / {AW}, 1) as offers_wk_after,
       round(100.0 * count(*) filter (where outcome = 'taken' and sent_at < timestamp '{RELEASE}') / nullif(count(*) filter (where sent_at < timestamp '{RELEASE}'), 0), 0) as yes_pct_before,
       round(100.0 * count(*) filter (where outcome = 'taken' and sent_at >= timestamp '{RELEASE}') / nullif(count(*) filter (where sent_at >= timestamp '{RELEASE}'), 0), 0) as yes_pct_after,
       round(100.0 * count(*) filter (where outcome = 'missed' and sent_at >= timestamp '{RELEASE}') / nullif(count(*) filter (where sent_at >= timestamp '{RELEASE}'), 0), 0) as missed_pct_after
from pings group by 1 order by (count(*) filter (where sent_at >= timestamp '{RELEASE}') / {AW}) / nullif(count(*) filter (where sent_at < timestamp '{RELEASE}') / {BW}, 0)
```
Flag any hero whose offers a week fell by half or more, and any group of heroes that moved very differently from the rest.

Query 5: by area (where the job happened): how often the first offer went outside the area, and jobs nobody took.
```sql
with fp as (select distinct on (callout_number) callout_number, responder as first_resp from pings order by callout_number, sent_at),
tk as (select distinct callout_number from pings where outcome = 'taken')
select c.area,
       case when c.happened_at < timestamp '{RELEASE}' then 'before' else 'after' end as period,
       count(*) as callouts,
       round(100.0 * count(*) filter (where fr.area <> c.area) / count(*), 0) as first_offer_outside_area_pct,
       round(100.0 * count(*) filter (where c.callout_number not in (select callout_number from tk)) / count(*), 0) as nobody_took_pct
from callouts c join fp using (callout_number) join responders fr on fr.name = fp.first_resp
group by 1, 2 order by 1, 2 desc
```

Query 6: time of day (rules out "it's just the night shift").
```sql
select case when extract(hour from sent_at) < 6 then '00-05' when extract(hour from sent_at) < 12 then '06-11'
            when extract(hour from sent_at) < 18 then '12-17' else '18-23' end as hours,
       case when sent_at < timestamp '{RELEASE}' then 'before' else 'after' end as period,
       count(*) as offers,
       round(100.0 * count(*) filter (where outcome = 'missed') / count(*), 1) as missed_pct
from pings group by 1, 2 order by 1, 2 desc
```

Query 7: day by day, 14 days either side of the release.
```sql
select sent_at::date as day, count(*) as offers,
       count(*) filter (where outcome = 'missed') as missed,
       round(100.0 * count(*) filter (where outcome = 'missed') / count(*), 0) as missed_pct,
       round(100.0 * count(*) filter (where outcome = 'taken') / count(*), 0) as yes_pct
from pings
where sent_at >= timestamp '{RELEASE}' - interval '14 days' and sent_at < timestamp '{RELEASE}' + interval '14 days'
group by 1 order by 1
```

Query 8: support tickets by week and theme (keywords are approximate; say so).
```sql
select to_char(date_trunc('week', filed_at), 'YYYY-MM-DD') as week, count(*) as tickets,
       count(*) filter (where subject ~* 'moved on|gone before|vanished|lost one|missed it|reaching for the phone') as gone_before_answer,
       count(*) filter (where subject ~* 'gone off in|hardly anything|quieter|still very quiet|nothing coming|still on the list|pass this on|first one in') as quiet,
       count(*) filter (where status = 'open') as open_now
from support_tickets group by 1 order by 1
```

## The log entry

Write one file per release in `release-log/` inside this folder (create the folder if it doesn't exist): `release-log/<release date>-<short slug>.md`, for example `release-log/2026-08-12-4-2-wait-90-to-60.md`. If a file with that name exists, don't overwrite it: add `-v2`.

Also add one line to `release-log/INDEX.md` (create it with a heading if missing): date, release, change, one-line result, link to the entry.

The entry contains, in plain English with small tables:
- **Release and change:** date, version, what changed, what else shipped the same day.
- **Windows:** before and after with their lengths in weeks, and the equal-length comparison.
- **Key numbers:** offers a week, yes, no and missed rates, jobs nobody took, offers per job.
- **Setting check:** the gap before the next offer, before and after, and whether every hero switched together.
- **Who moved most:** heroes and areas.
- **Alternatives tested:** step or slide, time of day, geography, a few heroes, seasonality, other changes.
- **Tickets:** agree, disagree or silent.
- **Caveats and confidence:** what can't be separated, what isn't in the database (response times, availability, travel times, real points), and a 0 to 95 confidence for each group of numbers.
- **Run on:** today's date.

## Report format

```
release-impact
Release: <date, version>. Change: <one line>. Others shipped the same day: <list or none>.

| Measure | Before | After | Change |
|---|---|---|---|
| Offers a week | | | |
| Yes / no / missed | | | |
| Jobs nobody took | | | |
| Next offer after a missed one (seconds) | | | |

Setting check: <did everyone switch at once?>
Who moved most: <heroes or areas, with counts>
Alternatives tested: <each one, ruled out or not>
Tickets: <agree / disagree / silent>
Confidence: <0 to 95 per group, and what would firm it up>

In plain English: <three sentences: what changed, what we can and can't say caused it, what to do next>
Log: <path to the entry>
```

## Limits to always say

- The database holds **outcomes**, not **how long** a hero took to answer. It can show that the wait changed and how outcomes moved, but not how many heroes would have answered in the extra seconds.
- **Several changes in one release can't be separated.** Report what changed after, list what else shipped, and suggest a staged rollout (some areas first) next time.
- A before-and-after comparison shows **what changed**, not **why**.
- Heroes appear only under their cover names. Never try to identify the people behind them.
