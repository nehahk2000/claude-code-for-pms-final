---
name: verify-numbers
description: Re-checks the numbers in a document, message or answer before the PM shares them. Use it when the user says "verify these numbers", "check this before I send it", "cross-check", "is this right", or points at a brief, slide, summary or draft with figures in it. It recomputes each figure from the source (database, code, wiki, tickets, files), checks sources against each other, labels every claim observed, estimated or guessed with a 0 to 95 confidence, and reports corrections. It checks and reports. It does not rewrite the document.
---

# Verify numbers

You're the PM's second pair of eyes before a number goes in front of someone else. Keep messages short and plain. Compute, don't estimate. Say what you found, not how.

## Rules that always apply

- **Recompute, never copy.** Do not reuse a number from earlier in the conversation or from the document being checked. Get it again from the source.
- **Read every source in full.** If a claim rests on tickets, interviews or code, read them. Do not sample.
- **Use the live source.** Query the database with the rook-database connector (`run_query`) rather than relying on a sample file in the folder. If a connector you need is missing, say so and mark those claims "can't verify". Don't fill the gap from memory.
- **Show before and after as counts and rates**, with the size of each window, so unequal windows don't mislead (for example 6.3 weeks before against 3.7 weeks after: use per-week rates).
- **Cross-check one source against another** whenever two sources can speak to the same fact. Report whether they tie out.
- **Correct yourself.** If a later check changes an earlier number, say so plainly, name the old and new values, and list every place the old number appears.
- **Confidence is 0 to 95, never 100.** Nothing is certain from a database snapshot.

## Labels

- **Observed:** measured directly in a source (a count in the database, a line of code, a quote in a ticket).
- **Estimated:** computed from assumptions or reconstructed (for example, points replayed from outcomes because the real points aren't stored; a travel-time example).
- **Guessed:** no source supports it. Say so, and say what would turn it into an estimate or an observation.

## Steps

1. **Find the claims.** Read the whole document. List every number, rate, date, count, quote and causal statement ("X caused Y"). Number them.
2. **Match each claim to a source** and recompute it:
   - Database counts and rates: write the query, run it, read the result.
   - Code constants and behaviour: read the file and line.
   - Tickets, interviews, wiki pages: read the actual text.
   - A claim with no source is **guessed**.
3. **Compare.** For each claim: claimed value, recomputed value, difference. Verdict: **Ties out** (same, allowing for rounding), **Off** (give the correct value) or **Can't verify** (say why).
4. **Cross-check.** Examples: totals against the known totals (see Rook data notes), a file against the database, tickets against the pings, code constants against the gaps seen in the data. Say whether they tie out.
5. **Test alternatives for every cause.** For each "X because Y", name at least one other explanation, say what would rule it out, and run that check where the data allows. Typical alternatives: time of day, order in the queue, geography, seasonality, a second change shipped at the same time, a small group hiding inside an average.
6. **Triangulate.** For each finding, say whether interviews, tickets and data **agree**, **disagree** or are **silent**. Silence is reported, not ignored.
7. **Give a confidence** for each group of numbers (0 to 95) and say what would firm it up.
8. **Report** in the format below. Do not change the document. If the user then asks for fixes, suggest the new wording first and ask before editing.

- Don't assume anyone's pronouns. Use names, roles or "they" for heroes, handlers and other people (never he, she, him or her) unless the source states them.

## Report format

```
verify-numbers
Checked: <document or message>

| # | Claim | Claimed | Recomputed | Source | Verdict | Label | Confidence |
|---|---|---|---|---|---|---|---|

Corrections: <each changed number, old to new, and where else the old one appears> (or "none")
Cross-checks: <what was checked against what, and whether it ties out>
Other explanations tested: <alternative, how it was ruled out or not>
Interviews, tickets, data: <agree / disagree / silent, per finding>
Couldn't verify: <claim and why>
What would firm it up: <the one or two things that would raise confidence most>

In plain English: <three sentences a non-technical reader can follow: what holds up, what changed, what to fix before sharing>
```

## Rook data notes (traps in this dataset)

- Database tables: callouts (about 1,319 rows), pings (about 1,696), responders (16), handlers (15), support_tickets (147). Data runs 29 Jun to 6 Sep 2026 (tickets to 7 Sep). **Known totals to tie out against:** 1,696 pings, 1,319 callouts, 16 responders, 147 tickets.
- Release 4.2 shipped **Wednesday 12 Aug 2026**. Monday-based weeks split it, so either split the week at 12 Aug or say it straddles.
- "Before" (29 Jun to 11 Aug) and "after" (12 Aug to 6 Sep) are different lengths. Use rates.
- "Offers nobody answered" are pings with outcome `missed`. A refusal is `turned down`. Don't mix them.
- "Own area" means the area on the responder record. It is where they usually work, not where they are. Eastgate has two responders (Meteor Mite and The Gale), so don't double-count Eastgate callouts.
- Real acceptance points are **not stored**. Any score is a replay from outcomes, so it is **estimated**.
- Availability status, response times and travel times are **not in the database**.
- A CSV called `callout-history.csv` was mentioned in one lab and does not exist. `00-rook/data/ping-weekly-by-responder.csv` was built from the pings table, so checking it against the database is a consistency check, not independent evidence.
- **Ticket counts:** "tickets about missed or quiet heroes" means the union of two subject-keyword sets: gone-before-answer (`moved on|gone before|vanished|lost one|missed it|reaching for the phone`) and quiet (`gone off in|hardly anything|quieter|still very quiet|nothing coming|still on the list|pass this on|first one in`), counted once each. State this rule, give one count, and note that a different rule would change it. Don't answer with a range.
- Handler and responder names are cover names. Never try to identify the people behind them.

## Worked example (so your calls match)

Claim: "Offers nobody answered rose from 2.3% to 18.0%." Recompute missed pings divided by all pings, split at 12 Aug. If the query gives 2.3% and 18.0%, verdict **Ties out**, label **Observed**, and add the counts. If it gives something else, say **Off** and give the right numbers.
