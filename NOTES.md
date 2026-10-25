# Notes

## Summary

<!-- What you did, in a few sentences. What you'd like us to look at first. -->
- Fixed the three bugs: deleting the first step of a branch now preserves the branch outcome, switching flows quickly no longer lets an old response overwrite the selected flow and edits are correctly detected as unsaved.
- Also worked on the reference picker and now it shows the available outputs to the selected step.
- Safe delete was not implemented due to time constraint.

## Bug fixes

### #1 Deleting the first step of a branch

- Root cause: In removeStep function, when reconnecting the deleted step's connections, the outgoing connection is copied, including the outcome, in the incoming connection. For the described issue, the incoming connection is condition -> "Create CRM customer", with outcome: "True", and the outgoing is "Create CRM customer" -> "Welcome email", with outcome: "Done". When deleting "Create CRM customer" step, "Welcome email" step is reconnected to the condition as done, not true. The condition already has a done connection to the merge step, and buildTree follows connections by outcome. "Welcome email" is no longer reacheable in the rendered tree, so it looks deleted even though it remains in the flow data. 
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
- The picker lists outputs from steps guaranteed to have run before the selected step. It uses each step type's output definitions to display readable output names and inserts a generated `{{ steps.<uuid>.output.x }}` reference into the selected property.
- A step inside a branch can reference outputs from steps before the condition and from earlier steps in the same branch, but not from an opposite branch. A step after a merge can reference outputs from before the condition, but not outputs exclusive to a single branch.
- Verification: Manual check. I would add tests if I had more time.

### B. Safe delete

<!-- How you find references, and why you did it that way. -->

## Decisions and assumptions

<!-- Anything that was unclear and how you decided. -->
- For bug 2: in an application with a real backend maybe AbortController would be a better option.
- For feature A:
- I treated an output as available only if it's guaranteed to exist when the selected step runs. This means outputs from the other branch, or outputs specific to just one branch, are not available because only one branch executes.
- I removed closing of the reference picker after inserting a reference because when adding more references it felt like an extra step to open again the reference picker.
- I added a check to see if the reference exists before adding it.

## If I had more time
- For feature A: implement the optional point, add a better UI and tests.
- Implement feature B. The plan:
- Search step properties for references to the step being deleted.
- For each reference found, collect the id and display name of the step that contains it.
- If no references exist, preserve the current delete behavior.
- If references exist, show a confirmation dialog listing the dependent steps.
- Cancel leaves the flow unchanged, confirm proceeds with deletion.

## AI usage

<!--
Which tools you used and for what.
A few examples of prompts or tasks you gave them.
What you kept, what you changed, and what you threw away (and why).
-->

- I used Copilot for code reviews, explanations and feature A
- Prompt: "Review my changes for ... ."
- After reviewing my solution for bug 2, it suggested disabling the list item while loading. I decided that wasn't neccessary.
- Prompt: "Explain this function ... .", "Explain feature A."
- Prompt: "Create an implementation plan for feature A." I used the plan it generated
- During code review it identified two issues: "Invalid form keys can produce unusable references" and "The availability rules aren't covered by tests." I did not address these issues due to time constraints.
