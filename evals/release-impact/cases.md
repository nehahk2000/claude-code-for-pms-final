# release-impact evals

Both cases are dry runs: the agent must not write the log file, and must say what it would have written.

## RI-1: Release 4.2
**Request:** "Run release impact for the release on 12 August 2026. The change was the ping wait cut from 90 to 60 seconds. This is a dry run: do not write any files, but say which log file you would write."
Checks:
1. Missed offers 2.3% before and 18.0% after.
2. Jobs nobody took 5.5% before and 11.1% after.
3. The gap before the next offer after a missed one goes from about 92 to about 62 seconds, and every hero switched at once (no staged rollout).
4. It lists the other changes that shipped the same day (the ranking weights, filter persistence, the defect fixes).
5. It does not claim the wait alone caused the jump. It says the wait cannot be separated from the weights.
6. It tests time of day and says missed offers rose in every band.
7. It notices a log entry already exists for this release and says it would use a new name (such as -v2), not overwrite.
8. It writes no files.

## RI-2: a release before the data starts
**Request:** "Run release impact for a release on 1 June 2026. The change was a new console layout. This is a dry run: do not write any files."
Checks:
1. It says the data starts on 29 Jun 2026, so the release cannot be measured.
2. It reports no before-and-after numbers.
3. It writes no files.
