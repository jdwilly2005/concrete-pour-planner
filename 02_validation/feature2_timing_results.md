# Feature 2 validation results (concrete timing checks)

Run in the browser by setting the inputs and reading the Timing checks bar
and the drawn timeline. Defaults: discharge limit 90 min, set time 180 min.

| Case | Spec expects | App shows | Match |
|------|--------------|-----------|-------|
| 7a: case 1 | both pass; longest 40 min; first load sets 9:40 AM, 80 min after the last arrival | ✓ "Every load is empty within 90 min of batching (longest 40 min)." ✓ "Last truck arrives 8:20 AM, 80 min before the first load starts to set (9:40 AM)." No timeline warnings; set line not drawn (9:40 is past the chart) | Yes |
| 7b: case 1, 75 min travel | loads 1-4 over (95 min), load 5 not (89 min); "4 of 5 loads"; sets 8:45 AM, 25 min after the last arrival | ⚠ "4 of 5 loads take longer than 90 min from batching to empty (up to 95 min)." ✓ set check, 25 min, 8:45 AM. Rows 1-4 marked ⚠ with red outlines (4 outlined, load 5 not); "First load sets 8:45 AM" line drawn | Yes |
| 7c: case 1, set time 60 | sets 7:40 AM; last arrival 8:20, 40 min after; set check fails | ⚠ "Last truck arrives 8:20 AM, 40 min after the first load starts to set (7:40 AM). Risk of a cold joint." Discharge ✓ | Yes |
| 7d: 70 min travel | exactly 90 min passes | ✓ "(longest 90 min)" | Yes |
| 7d: set time 100 | sets exactly at the last arrival, passes | ✓ "Last truck arrives 8:20 AM, just as the first load starts to set (8:20 AM)." | Yes |

Extra checks:
- Hover on an over-limit unloading bar: "Load 1, truck 1. Unloading: 7:00 AM – 7:20 AM (20 min). Batching to empty: 95 min (over the limit)."
- Set time 30 clamps to 60 with "Minimum is 60 min. Using 60."
- Clear hides the Timing checks bar.

Regression: cases 1-6 rerun after the feature. Cards, order text, costs,
and timeline row counts unchanged (case 4 cost $5,875 = 35.0 x $165 +
$100 for its 5 cy short last load). Case 2 area 101,804 ft^2. No console
messages.

Layout: checked case 7b at 1280 px and 375 px; no sideways scrolling; the
"First load sets" and "Finish" labels stack without overlapping.

Fixed during testing: the setDrawnSlab() console helper zoomed with an
animation, so a size typed right after it placed the rectangle off-screen.
It now zooms instantly.
