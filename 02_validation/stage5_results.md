# Stage 5 validation results (finished app)

Every spec validation case rerun on the finished app in the browser
(cards, order text, and timeline together). No console messages.

| Case | Spec expects | App shows | Match |
|------|--------------|-----------|-------|
| On open | case 1 already shown | 47.0 cy, 5 loads, 4 trucks, 1 h 34 min, 8:34 AM, 5 timeline rows | Yes |
| 1 | 2,400 ft^2; 44.44 / 46.67 / 47.0 cy; 5 loads (4 x 10 + 1 x 7); arrivals 7:00-8:20; 4 trucks; finish 8:34 AM; 1 h 34 min | Same; finish line "Finish 8:34 AM" | Yes |
| 2 | about 101,800 ft^2 | 101,804 ft^2 | Yes |
| 3 | 400 ft^2; 4.94 cy; 5.0 cy; 1 load; 1 truck; 10 min | Same; 1 row; finish 7:10 AM | Yes |
| 4 | 35.0 cy (not 35.5); 4 loads (3 x 10 + 1 x 5); finish 8:10 AM; 1 h 10 min | Same; 4 rows | Yes |
| 5 | 5 trucks; same finish as case 1; 30 in clamps to 24 in with a note | 5 trucks, 8:34 AM; "24 / Maximum is 24 in. Using 24." | Yes |

Stage 5 checks:

| Check | Result |
|-------|--------|
| Copy button | Copies the order text; button shows "Copied ✓". Falls back to selecting the text and copying if the browser blocks the clipboard API (it does in the Claude preview pane). Disabled when there is no slab. |
| No slab (Clear) | Cards show "—"; order box and timeline show the hint; Copy disabled |
| First truck 11:30 PM | Finish "1:04 AM (+1 day)" on the card and the timeline |
| Desktop 1280 px | Map and inputs left; cards, timeline, order right; no sideways scroll |
| Phone 375 px | Everything stacks; nothing wider than the screen; timeline fits without scrolling |

Not tested here: drawing with a finger on a real phone (Leaflet turns taps
into clicks and a double-tap into a double-click, so it should work; check it
on your phone after publishing).

Still to do by hand (Step 5): work case 1 with a calculator and sketch the
Gantt chart, and compare a building outlined on the map with Google Maps'
Measure distance.
