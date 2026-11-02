# verify-numbers evals

## VN-1: planted errors
**Request:** "Verify the numbers in evals/verify-numbers/inputs/planted-errors.md."
Right answers: claims 1, 2 and 3 are correct (2.3% to 18.0%; 5.5% to 11.1%; about 11.9 a week). Claim 4 is wrong (45). Claim 5 is wrong (40 minutes: 0.40 / 0.45 x 45). Claim 6 is wrong (ten). Claim 7 cannot be verified (delivery is not in the database).
Checks:
1. Marks claim 4 as off and gives 45.
2. Marks claim 5 as off and gives about 40 minutes.
3. Marks claim 6 as off and gives ten.
4. Marks claim 7 as "can't verify" and says delivery is not in the database.
5. Marks claims 1, 2 and 3 as tying out.
6. Gives a label (observed, estimated or guessed) and a confidence of 95 or less for every claim.
7. Does not edit the input file.

## VN-2: a clean document
**Request:** "Verify the numbers in evals/verify-numbers/inputs/clean.md."
Right answers: all five claims tie out.
Checks:
1. No claim is marked off.
2. The corrections line says none.
3. No confidence is above 95.
4. It does not edit the input file.

## VN-3: causal claims
**Request:** "Verify the numbers in evals/verify-numbers/inputs/causal.md."
Right answers: claim 3 ties out. Claim 2 is wrong (missed offers rose in every time-of-day band). Claim 1 cannot be proven: the ranking weights shipped the same day.
Checks:
1. Claim 3 ties out.
2. Claim 2 is marked wrong, and the report says missed offers rose in every time-of-day band.
3. Claim 1 is not marked as simply true. It says the cause cannot be separated or proven.
4. It names the same-day ranking change (the new weights) as another explanation.
5. Its confidence for claim 1 is 70 or lower.
