# Notes

## Summary

<!-- What you did, in a few sentences. What you'd like us to look at first. -->

## Bug fixes

### #1 Deleting the first step of a branch

- Root cause: In removeStep function, when reconnecting the deleted step's connections, the outgoing connection is copied, including the outcome, in the incoming connection. For the described issue, the incoming connection is condition -> "Create CRM customer", with outcome: "True", and the outgoing is "Create CRM customer" -> "Welcome email", with outcome: "Done". When deleting "Create CRM customer" step, "Welcome email" step is reconnected to the condition as done, not true. The condition already has a done connection to the merge step, and buitTree follows connections by outcome. "Welcome email" is no longer reacheable in the rendered tree, so it looks deleted even though it remains in the flow data. 
- Fix: Update removeStep function to keep the incoming connection's outcome when reconnecting the deleted step's successor.
- How I verified it: Manual check and by running the tests.

### #2 Switching flows quickly

- Root cause:
- Fix:
- How I verified it:

### #3 Unsaved changes / Discard

- Root cause:
- Fix:
- How I verified it:

### Other things I noticed

<!-- Problems you found that weren't reported. Which ones you fixed, and which ones you left (and why). -->

## Features

### A. Reference picker

<!-- Which outputs you offer, and why. Edge cases you thought about. -->

### B. Safe delete

<!-- How you find references, and why you did it that way. -->

## Decisions and assumptions

<!-- Anything that was unclear and how you decided. -->

## If I had more time

## AI usage

<!--
Which tools you used and for what.
A few examples of prompts or tasks you gave them.
What you kept, what you changed, and what you threw away (and why).
-->
