# Feature 1 validation results (cost estimate)

Run in the browser by setting the inputs and reading the Estimated cost card.
Defaults: price $165/cy, short-load threshold 6 cy, fee $100.

| Case | Spec expects | App shows | Match |
|------|--------------|-----------|-------|
| 6a: case 1 | $7,755, no short loads | $7,755 · "47.0 cy × $165 · no short loads" | Yes |
| 6b: case 3 | $925 (one 5 cy short load) | $925 · "5.0 cy × $165 + $100 short-load fee (1 load)" | Yes |
| 6c: case 1, 4 cy trucks | 12 loads (11 x 4 + 1 x 3), all short; $8,955 | $8,955 · "... + $1,200 short-load fees (12 loads)"; loads card "11 × 4 cy + 1 × 3 cy" | Yes |
| 6d: case 3, threshold 0 | $825 | $825 · no short loads | Yes |

Extra checks:
- Threshold exactly equal to the load (case 3, threshold 5): not short, $825.
- Price $165.50 on case 1: $7,779 (47.0 x 165.50 = 7,778.50, shown to the nearest dollar); detail shows "$165.50".
- Price 900: clamped to 500 with "Maximum is $500/cy. Using 500."; $23,500.
- Clear: the cost card shows "—".

Regression: cases 1-5 rerun after the feature. Cards, timeline rows, and
order text are unchanged (the cost is not in the order text). Case 2 area
101,804 ft^2. No console messages.

Layout: six cards in two rows of three at 1280 px; three rows of two at
375 px; no sideways scrolling.

Bug found while checking the layout (not caused by this feature): at
1280 px the map could open about 145 m south of the slab, with the slab
off-screen. Leaflet's animated re-centering drifted when several resizes
happened during page load. Fixed by remembering the intended map center
and restoring it exactly after each resize. Verified: a fresh load at
1280 px is centered on the slab; resizing to 375 px keeps it centered; a
view the user has panned to survives a resize. A fresh load at 375 px
could not be re-run in the preview pane after the fix.
