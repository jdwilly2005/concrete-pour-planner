# Prompt log

## 1. Project setup and spec
- **Asked:** Help with the in-class assignment (In Class - web apps.pdf), using Claude Code instead of VS Code, building the Concrete Pour Planner.
- **Changed:** Ran git init; created AGENTS.md (plus CLAUDE.md that imports it, since Claude Code reads CLAUDE.md), the numbered folders 00_planning to 03_output with a README.md each, spec.md (the polished Concrete Pour Planner spec from the PDF), and this file.
- **Worked?** Yes. The spec's validation numbers were checked by hand and agree (47.0 cy, 5 loads, 4 trucks, 8:34 AM finish; about 101,800 ft^2 for case 2; 10 min for case 3). Nothing built yet.

## 2. First commit
- **Asked:** Commit the working tree changes.
- **Changed:** Added .gitignore (ignores .DS_Store and the assignment PDF, so course material stays out of the public repo) and committed the setup and spec.
- **Worked?** Yes.

## 3. Spec review (Step 3)
- **Asked:** Start the spec review: whole-spec check, then each section in order.
- **Changed:** spec.md only. Inputs: ranges/steps/defaults table, number boxes that clamp, one slab at a time with Clear, 3+ corners, crossing-edge warning. Calculations: defined start, departure, last load, finish, duration, float tolerance, display rounding, no-slab and past-midnight cases. Outputs: timeline row labels and faded post-finish segments, card detail line, order text template. Look and feel: safety orange accent, opens on case 1, hint text, tap on touch. Validation: added cases 4 and 5. Out of scope: address search. Added a Resolved notes section.
- **Worked?** Yes. First draft of case 4 (30 x 54 x 8) turned out not to trigger float error (40 x 1.05 is exactly 42.0); replaced it with 30 x 60 x 6, which computes 35.00000000000001. Checked in Python, which uses the same doubles as JavaScript. Case 2 area computes to 101,804 ft^2.

## 4. Build stages (Step 4.1)
- **Asked:** Plan the build stages.
- **Changed:** Proposed 5 stages (layout mockup, calculations with typed slab, map drawing, truck timeline, polish and edge cases), each with what to check; added them to spec.md under ## Build stages after approval.
- **Worked?** Yes. Nothing built yet.
