# Stage 4 validation results (truck timeline)

Read from the drawn SVG in the browser, plus one real mouse hover.

| Check | Expected | Result |
|-------|----------|--------|
| Case 1 rows | 5 rows; loads 1-4 on trucks 1-4, load 5 on truck 1 | Same |
| Case 1 unloading | 7:00, 7:20, 7:40, 8:00, 8:20; last 8:20-8:34 (14 min) | Same |
| Case 1 finish line | 8:34 AM | "Finish 8:34 AM" |
| Case 1 faded segments | only segments starting at or after 8:34 | Load 4 washout 8:40-8:50; load 5 trip back 8:34-8:54 and washout 8:54-9:04 |
| Case 4 | 4 rows, last load 5 cy, finish 8:10 AM | Same |
| Case 5 | trucks 1 to 5 | Load 5 on truck 5 |
| Hover (real mouse) | segment name, times, length | "Load 5, truck 1. Unloading: 8:20 AM - 8:34 AM (14 min)" |
| 1,000 x 1,000 ft x 24 in, 15%, 1 cy trucks | 60 rows and a note; cards cover every load | "Showing the first 60 of 85,186 loads."; card shows 85,186; no finish line (last load not shown); 0.1 s |
| Washout 0 min | no empty washout bars | none drawn |
| Rate 25 cy/hr | arrivals 24 min apart; last unload 16.8 min | 7:00, 7:24, 7:48, 8:12, 8:36; last 8:36-8:53 (17 min) |
| Clear | timeline shows the hint | Same |

All earlier cases rerun after this stage: 1, 3, 4, 5 cards unchanged; case 2 area 101,804 ft^2.
