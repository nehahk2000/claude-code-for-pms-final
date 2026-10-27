---
name: review-checklist
description: Checks a product brief against four basics. Use it when the user points at a brief (a file, a folder of briefs, or a wiki page) and asks to review it, check it, or run the review checklist. It reports whether the brief names who owns it, says how we will know it worked, keeps the same scope from start to finish, and explains the problem before the fix. It checks and reports. It does not rewrite the brief.
---

# Review checklist

You're checking briefs for a product manager. Keep every message short and plain. Say what you found, not how you found it.

## What to check

Check every brief against these four things. Read each brief **in full**. Never judge from the title, the first section or a sample.

### 1. Owner named
The brief names who owns it: a person, a team, or a specific role that is accountable now.
- **Yes:** "Owner: the engineering manager's team", "The product designer, exploring for Q4", "the PM for Rook Dispatch (me)". If a name is easy to find in the Team directory of the Rook wiki, give it. Otherwise give the role.
- **NO:** no owner at all, "TBD", "whoever picks it up", "nobody's picked it up yet", or an owner only for a future phase. Flagging a gap for a future owner is not the same as naming one.

### 2. Success measure
The brief says how anyone would know it worked: a number, a rate, a count, or a clear observable signal.
- **Yes:** "fewer support tickets about X", "time to full coverage should drop", "we'll know it's working when that number is zero". A missing baseline is fine; mention it but don't flag it.
- **NO:** it only says what will be built, what gets logged, or where it shows up. A success measure is about the outcome, not the feature.

### 3. Scope stays the same
The scope the brief states or implies at the start is the scope it ends with.
- Compare what the brief opens with ("that's the whole ask", an "In scope" list, a one-sentence proposal) against everything proposed later: later paragraphs, decisions, plan steps, tests and open questions.
- **NO:** things are added mid-document ("while we're building that we should also..."), or something listed as out of scope is asked for later (for example, in a decision, a plan step or a test). Name each addition and say where it shows up.
- **Yes:** the later sections only develop or sequence what was stated at the start.

### 4. Problem before the fix
The brief explains the problem (who is affected, what goes wrong, some evidence) before it describes the solution.
- **Yes:** the problem appears first. A one-sentence problem in the overview that comes before any solution detail counts.
- **NO:** it opens with the solution, the feature, the request or the plan, and the problem only appears later. Say where the problem first appears.

If you cannot tell, say **UNCLEAR** and explain in one line. Don't guess.

## How to run it

1. **Find the briefs.** The user gives a file, a folder, or a wiki page.
   - A folder: check every brief in it (text, markdown, Word). Count them.
   - A `.docx`: read its text, for example `unzip -p file.docx word/document.xml`, and strip the tags.
   - A Rook wiki page: read it with the rook-wiki connector. For a wiki page of briefs, check each child page.
   - If the user gave nothing to point at, ask one short question: "Which brief or folder should I check?"
2. **Read each one in full.** Also read anything it clearly depends on if a check can't be decided without it (for example a "see the Team directory" reference for an owner).
3. **Judge the four checks** using the rules above.
4. **Report** in the format below.

Do not edit, rewrite, rename or save over the brief. If the user asks for fixes afterwards, suggest them in plain words and ask before changing anything.

## Report format

Plain text, like this (dots line up the answers):

```
review-checklist
Source: <path or page> (<n> brief<s>)

<brief name>
  owner named ................. yes (<who>)  or  NO — flagged
  success measure ............. yes  or  NO — flagged
  scope stays bounded ......... yes  or  NO — flagged
  problem stated before fix ... yes  or  NO — flagged
  -> <n> flag(s): <one sentence per flag: what is wrong and where, with the
     words from the brief that show it>
  (or)  -> clean

<n> briefs checked, <n> flagged.
```

Rules for the report:
- One block per brief, in the order found.
- For every flag, point at the evidence: quote a few words or say which section. Keep it to two or three lines.
- Say "yes" with a short note only when the note helps, such as who the owner is.
- End with one tally line. If every brief is clean, say so in one line.
- Mark an unclear check as `UNCLEAR — flagged` and say what would settle it.

## Worked example (so your calls match)

- A brief whose owner section says "nobody's picked it up yet": owner **NO**.
- A brief that says what gets logged and where it shows up, but never how anyone would know it's working: success measure **NO**.
- A brief that opens "that's the whole ask" for one approval step, then adds a routing-around rule, a dashboard, handler visibility and a spreadsheet replacement: scope **NO**.
- A brief that opens with the app and what it would do, and only reaches the real problem in its second section: problem before fix **NO**.
