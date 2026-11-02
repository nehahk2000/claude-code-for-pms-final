# On-leave status for heroes

**Owner:** the PM for Rook Dispatch. The product designer drafts the screen; Marcus's team builds it.

## Problem
When a hero is away on leave, the only option is to set them unavailable, which looks the same as being busy. Two handlers asked for a leave status (tickets 3035 on 5 Aug and 3143 on 6 Sep). The quiet-hero warning would also flag a hero on leave as if something were wrong.

## Proposal
Add one status, "On leave", with a return date. A hero on leave gets no offers and is not flagged as quiet until the return date.

## Scope
Only the status, the return date and the pause in offers and quiet flags. Nothing else: no calendar, no shift swapping, no reporting.

## How we'll know it works
- Fewer tickets asking for a leave status (none in the month after launch).
- No quiet-hero flags on a hero who is on leave.
