# Skill evals

Tests for the four skills in `.claude/skills/`. Each case is a request plus the right answer, written down **before** the skill is run. A fresh agent with no memory of this project runs the skill on the request, and each check in the case is marked pass or fail.

| Skill | Cases | Folder |
|---|---|---|
| quiet-hero-check | 3 | `quiet-hero-check/cases.md` |
| verify-numbers | 3 | `verify-numbers/cases.md` (inputs in `verify-numbers/inputs/`) |
| release-impact | 2 | `release-impact/cases.md` |
| review-checklist | 4 | `review-checklist/cases.md` (inputs in `review-checklist/inputs/`) |

Results from the latest run are in `results.md`.

## How to rerun
For each case, start a fresh session (or agent) and say: "Read `.claude/skills/<skill>/SKILL.md` and follow it for this request: <the request in the case>. Do not write, edit or delete any files. Return only the final report." Then compare the report to the checks.

## Limits
- The right answers come from this fictional dataset (Rook database, 29 Jun to 6 Sep 2026), so a pass means the skill works here, not on other data.
- The review-checklist answers for the four wiki briefs come from the course's own sample output (`06-sidekicks/scheduled-run-output.txt`).
- The planted-error inputs were written to contain known mistakes. They test whether the skill catches them.
