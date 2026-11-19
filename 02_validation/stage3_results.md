# Stage 3 validation results (map drawing)

Tested in the browser by clicking and dragging on the map, plus the
`setDrawnSlab(...)` helper for the lat/lon case.

| Check | Expected | Result |
|-------|----------|--------|
| Case 2: outline (40.2500, -111.6500), (40.2510, -111.6500), (40.2510, -111.6490), (40.2500, -111.6490) | about 101,800 ft^2, within 1% | 101,804 ft^2 (0.00% off) |
| Default typed slab drawn on the map | 40 x 60 ft rectangle, area 2,400 ft^2 | Polygon formula on its corners gives 2,400.0 ft^2, so it is drawn at true size; cards show case 1 |
| Draw 4 corners, double-click to finish | 4-corner outline, no extra corner, map doesn't zoom | 4 corners, zoom stayed at 18, about 48,800 ft^2 (matches its on-screen size at 0.456 m/px) |
| Drag a corner so the edges cross | warning shown | "The outline's edges cross, so the area is not reliable..." |
| Esc while drawing | previous slab comes back | Previous drawn outline restored |
| Finish with 2 corners | keeps drawing, hint about 3 corners | Same |
| Clear | no slab, cards show a dash, order box asks for a slab | Same |
| Type a width after Clear | rectangle at the map center | Same (3,200 ft^2 for 40 x 80) |
| Pan, then change a typed size | rectangle stays where it is | Center unchanged |

Still to do by hand (Step 5): outline a building on the map and compare its
area with length x width from Google Maps' Measure distance tool.
