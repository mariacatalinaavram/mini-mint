# Notes

## Summary

<!-- What you did, in a few sentences. What you'd like us to look at first. -->

## Bug fixes

### #1 Deleting the first step of a branch

- Root cause: In removeStep function, when reconnecting the deleted step's connections, the outgoing connection is copied, including the outcome, in the incoming connection. For the described issue, the incoming connection is condition -> "Create CRM customer", with outcome: "True", and the outgoing is "Create CRM customer" -> "Welcome email", with outcome: "Done". When deleting "Create CRM customer" step, "Welcome email" step is reconnected to the condition as done, not true. The condition already has a done connection to the merge step, and buitTree follows connections by outcome. "Welcome email" is no longer reacheable in the rendered tree, so it looks deleted even though it remains in the flow data. 
- Fix: Update removeStep function to keep the incoming connection's outcome when reconnecting the deleted step's successor.
- How I verified it: Manual check and by running the tests.

### #2 Switching flows quickly

- Root cause: Each click updates activeFlowId, then starts a getFlow request. If the earlier "KYC onboarding (Nordics)" request finishes last, it overrides editor.flow even though "Contract signing" is still the active selection. Then save function saves "KYC onboarding (Nordics)" data using the active "Contract signing" id. The api stores "KYC onboarding (Nordics)" content under that id, which explains the duplicate flows and the lost "Contract signing" flow name. The list highlight and editor content are different because they read different state: FlowList.vue highlights activeFlowId, while the canvas renders flow.
- Fix: Check if the requested flow is still selected before updating the editor. Update the check on saving.
- How I verified it: Manual check.

### #3 Unsaved changes / Discard

- Root cause: In openFlow function, the same flow object is assigned to both this.flow and this.original. When this.flow is updated, this.original is also changed. Then isDirty compares the two matching objects and returns false.  
- Fix: Create a deep copy, like in discard function.
- How I verified it: Manual check.

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
