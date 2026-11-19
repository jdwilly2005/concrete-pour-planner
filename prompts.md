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

## 5. Build stage 1: layout mockup
- **Asked:** Build stage 1 (layout mockup).
- **Changed:** Created index.html: header, Leaflet 1.9.4 map with Esri World Imagery centered on BYU and a placeholder 40 x 60 ft slab with label, Draw slab / Clear buttons and hint, all inputs as number boxes with ranges, five summary cards, an SVG truck timeline drawn from hard-coded case 1 data (legend, finish line, faded segments after finish, hover/tap tooltips), and the order summary with a Copy button (not wired yet). A banner marks it as a mockup.
- **Worked?** Yes, after fixes found in the browser: the slab outline was drawn off-screen because Leaflet measured the map before layout finished (added invalidateSize on load/resize); the Finish label and last axis time were clipped; "1 h 34 min" wrapped; on a phone the timeline scrolled sideways (now fits, with shorter row labels and 30-min ticks). No console errors on desktop or phone width.

## 6. Build stage 2: calculations with a typed slab
- **Asked:** Build stage 2.
- **Changed:** spec.md first: load sizes shown without a decimal when whole (the old rounding rule contradicted the order template), and wording for a one-load order. index.html: a separate `PourCalc` script of pure functions (roundUpTo with 1e-9 tolerance, polygonAreaFt2, rectAreaFt2, volumes, truckloads, unloadMinutes, planPour); a wiring script that reads and clamps the inputs, runs planPour, and fills the five cards and the order text on every keystroke. Banner updated. Map slab and timeline are still placeholders (stages 3 and 4). Results saved to 02_validation/stage2_results.md.
- **Worked?** Yes. Cases 1 to 5 all match. One bug found and fixed: a clamp note disappeared as soon as any other box was edited; notes now stay until that box is edited again.

## 7. Build stage 3: map drawing
- **Asked:** Build stage 3.
- **Changed:** spec.md first: length runs east-west and width north-south; resizing keeps the rectangle in place; Cancel button and Esc while drawing; a line under the slab boxes while a drawn outline is in use; what the cards show with no slab. index.html: `PourCalc.rectCorners` and `PourCalc.edgesCross` (pure geometry); removed the placeholder slab; one slab state (typed, drawn, or none); Draw slab with click-to-add corners, double-click or first-point to finish, 3-corner minimum; draggable corner handles (dragging a typed rectangle makes it a drawn outline); Clear; live area/volume label above the slab; crossing-edge warning; empty state for cards and order text; `setDrawnSlab()` helper for checking case 2. Results in 02_validation/stage3_results.md.
- **Worked?** Yes. Case 2 gives 101,804 ft^2. Drawing, dragging, Esc, Clear, and typing all behave as specified; no console errors. Moved the map label above the outline because it covered the top corners of small slabs. The building-vs-Google-Maps check is left for the hand checks.

## 8. Build stage 4: truck timeline
- **Asked:** Build stage 4.
- **Changed:** spec.md first: show at most 60 timeline rows with a "Showing the first 60 of N loads" note (the input ranges allow about 85,000 loads); timeline shows the hint when there is no slab. index.html: replaced the placeholder data and the mockup's `fmtClock` (it could show "7:60" and had no "+1 day") with `drawTimeline(plan)`, which draws from `planPour`'s schedule using the tested clock and duration formatting; wider choice of axis tick spacing for long pours; no zero-length bars; finish line only when the last load is shown; tooltips name the load and truck, stay on screen, and hide when tapping elsewhere; removed the "in progress" banner. Results in 02_validation/stage4_results.md.
- **Worked?** Yes. Case 1 timeline matches the spec, case 5 shows trucks 1-5, hover checked with the real mouse. Fixed during testing: crowded first/last axis labels, and the Truckloads card showing "85186" without a comma. All earlier validation cases still match.

## 9. Build stage 5: polish and edge cases
- **Asked:** Build stage 5.
- **Changed:** index.html: wired the Copy button (clipboard API, with a select-and-copy fallback and "Copied ✓" / "Press Ctrl+C" feedback; disabled when there is no slab); disabled-button and keyboard-focus styles. The "+1 day" display and no-slab state were already built in stages 2-4. No spec changes needed. Results in 02_validation/stage5_results.md.
- **Worked?** Yes. All validation cases 1-5 match on the finished app, Copy works (the preview pane blocks the clipboard API, so the fallback path was the one exercised), late start shows "+1 day", Clear shows the hints, and the layout is correct at 1280 px and 375 px with no sideways scrolling and no console messages. Finger drawing on a real phone is untested.

## 10. Plan feature 1 (Step 6): cost estimate
- **Asked:** Plan a feature.
- **Changed:** Proposed four features (cost estimate, working-time check, pump truck, plant truck limit); chose the cost estimate. Decided: flat fee per short load, defaults $165/cy / 6 cy threshold / $100 fee, cost on a sixth card only (not in the order text). spec.md only: three cost inputs, cost equations (fee on every load under the threshold, 0 turns it off), two rows of three cards, validation case 6 (6a-6d, worked by hand), "costs" narrowed in Out of scope, Resolved notes entry. Nothing built yet.
- **Worked?** Yes (planning only).

## 11. Build feature 1: cost estimate
- **Asked:** Build it now.
- **Changed:** index.html only (spec was already updated): `PourCalc.costEstimate` (pure, separate from planPour so the schedule math is untouched); a "Cost" input group (price, short-load threshold, fee) with clamping and dollar-formatted notes; an "Estimated cost" card, with the cards now in two rows of three; cost card cleared in the empty state. Also fixed a map bug found while checking the layout: the map could open off-center at desktop width because Leaflet's animated re-centering drifted during load-time resizes; it now restores the intended center exactly after each resize. Results in 02_validation/feature1_cost_results.md.
- **Worked?** Yes. Case 6a-6d match ($7,755, $925, $8,955, $825); cases 1-5 unchanged; layout fine at 1280 px and 375 px. A fresh page load at phone size could not be re-run in the preview pane after the map fix.

## 12. Plan feature 2 (Step 6): concrete timing checks
- **Asked:** Plan feature 2.
- **Changed:** Chose the working-time check over the pump truck and plant truck limit. Decided: both checks (per-load discharge limit, and last truck before the first load sets), defaults 90 min and 180 min, warnings on the page and timeline only. spec.md only: two inputs, the timing equations (batched when the truck leaves the plant; equal passes), a "Timing checks" bar and timeline markings, validation case 7 (7a-7d, worked by hand), Resolved notes entry. Nothing built yet.
- **Worked?** Yes (planning only).

## 13. Build feature 2: concrete timing checks
- **Asked:** Build it now.
- **Changed:** index.html: `PourCalc.timingChecks` (pure; per-load batching-to-empty age vs the discharge limit, and last arrival vs the first load's set time; equal passes); a "Concrete timing" input group; a "Timing checks" bar with green ✓ / amber ⚠ lines, hidden with no slab; timeline marks over-limit loads with a ⚠ label and red outline, adds the batching-to-empty age to the unloading tooltip, and draws a dashed red "First load sets" line when it falls inside the chart. spec.md: added the "just as the first load starts to set" wording for a 0 min margin (found at the 7d boundary). Also made the setDrawnSlab() console helper zoom instantly. Results in 02_validation/feature2_timing_results.md.
- **Worked?** Yes. Case 7a-7d match; cases 1-6 unchanged; layout checked at 1280 px and 375 px; no console messages.

## 14. Publish (Step 7)
- **Asked:** Help me publish.
- **Changed:** `gh` isn't installed, so the repo was created on github.com (public, `concrete-pour-planner`, empty). Before the first push, rewrote the author/committer email on all 12 local commits from a personal address to the GitHub noreply address and set it as this repo's `user.email`, so no personal email is public. Added the `origin` remote and pushed `main`. GitHub Pages turned on in the repo settings (Deploy from a branch, main, / root).
- **Worked?** Yes. Repo is public at https://github.com/jdwilly2005/concrete-pour-planner; site at https://jdwilly2005.github.io/concrete-pour-planner/.
