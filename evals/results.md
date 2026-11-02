# Eval results

Run 1 November 2026. Each case was run by a fresh Sonnet agent that was told to use the skill, then graded against the checks written in advance in each `cases.md`.

## First run: 63 of 67 checks passed

| Skill | Cases | Checks passed | What failed |
|---|---|---|---|
| quiet-hero-check | QHC-1, QHC-2, QHC-3 | 20 of 22 | QHC-3: used "she/her" for people; left a stray "correction" note in the report |
| verify-numbers | VN-1, VN-2, VN-3 | 15 of 16 | VN-1: gave "43 to 45" tickets instead of one count |
| release-impact | RI-1, RI-2 | 10 of 11 | RI-1: did not check whether a log file already existed |
| review-checklist | RC-1 to RC-4 | 18 of 18 | none |

## Fixes made to the skills
- All four: never assume pronouns; use names, roles or "they".
- quiet-hero-check: no self-corrections in the report; use the same "usual" figure everywhere.
- verify-numbers: ticket-count rule (union of two keyword sets, state the rule, give one number).
- release-impact: list `release-log/` first, say if a file exists, name the exact file in a dry run.

## Rerun of the three failed cases: 22 of 22 checks passed
- QHC-3: 7 of 7. RI-1: 8 of 8. VN-1: 7 of 7.
- After fixes: 67 of 67.

## Limits
- The right answers come from this fictional dataset, which I know well; a new dataset needs new cases.
- RC-1 answers come from `06-sidekicks/scheduled-run-output.txt`.
- Each case was run once. A skill that passes once can still fail on another run.
- Reruns were only for cases that failed, so the fixes were not re-tested on the cases that passed.
