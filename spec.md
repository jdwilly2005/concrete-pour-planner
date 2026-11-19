# Concrete Pour Planner

## Purpose
A planning tool for a concrete pour. The user outlines a slab on
satellite imagery (or types its dimensions), and the app computes how
much concrete to order, how many truckloads it takes, how many trucks the
batch plant needs to keep the pour going without a break, and when each
truck should arrive.

## Users
A construction management student, superintendent, or project engineer
planning a slab pour and calling in the order to the batch plant.

## Platform
- A single index.html with plain HTML, CSS, and JavaScript.
- Map: Leaflet 1.9.4 from unpkg, with Esri World Imagery tiles
  (https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x})
  and the required attribution. No API keys.

## Inputs
- Slab outline. There is one slab at a time, and the last action wins:
  drawing replaces a typed slab, and typing replaces a drawn one. Either:
  - Drawn on the map: a "Draw slab" button, then click the corners and
    double-click (or click the first point) to finish. Corners can be
    dragged afterward. An outline needs at least 3 corners. If its edges
    cross, show a warning that the area is not reliable. While drawing,
    the button reads "Cancel" and Esc also cancels, which puts back the
    slab that was there before. While a drawn outline is in use, a line
    under the length and width boxes says so and gives its area. The
    map opens centered on BYU campus
    (40.2518, -111.6493) at zoom 18. Or:
  - Typed: length and width (ft) for a rectangular slab, which is drawn
    on the map as a north-aligned rectangle of that size at the map
    center (length runs east-west, width north-south). Changing a typed
    size keeps the rectangle where it is. Number boxes, 1 to 1,000 ft
    each, defaults 40 and 60.
    Dragging a corner of a typed rectangle turns it into a drawn outline.
  - A "Clear" button removes the slab.
- Numeric inputs are number boxes. A value outside its range is clamped
  to the nearest limit, with a short note next to the box.

| Input | Units | Range | Step | Default |
|-------|-------|-------|------|---------|
| Slab thickness | in | 3 to 24 | 0.5 | 6 |
| Waste allowance | % | 0 to 15 | 1 | 5 |
| Truck capacity | cy | 1 to 12 | 0.5 | 10 |
| One-way travel time from the batch plant | min | 5 to 120 | 1 | 20 |
| Placement rate the crew can sustain | cy/hr | 5 to 150 | 1 | 30 |
| Washout and reload time at the plant | min | 0 to 30 | 1 | 10 |
| Concrete price | $/cy | 50 to 500 | 1 | 165 |
| Short-load threshold (0 turns the fee off) | cy | 0 to 12 | 0.5 | 6 |
| Short-load fee, per short load | $ | 0 to 500 | 1 | 100 |

  The three cost inputs are in their own "Cost" group, below the truck
  and crew inputs.

| Input | Units | Range | Step | Default |
|-------|-------|-------|------|---------|
| Discharge limit, batching to empty | min | 30 to 180 | 1 | 90 |
| Initial set time of the concrete | min | 60 to 480 | 5 | 180 |

  These two are in a "Concrete timing" group below the cost inputs. 90
  min is the ASTM C94 default discharge limit; 3 h is a typical initial
  set for normal concrete around 70°F (shorter in hot weather).

- Time the first truck should arrive: a time picker, default 7:00 AM.

## Calculations
- Slab area (ft^2):
  - Typed slab: area = length x width, exactly.
  - Drawn outline:
    A = R^2 x |sum over edges of (lon2 - lon1) x (2 + sin lat1 + sin lat2)| / 2
    with angles in radians and R = 6,378,137 m, converted to ft^2
    (1 m^2 = 10.7639 ft^2).
- Net volume (cy) = area x thickness / 12 / 27.
- Volume with waste (cy) = net volume x (1 + waste / 100).
- Volume to order (cy) = volume with waste, rounded up to the next 0.5 cy.
- Truckloads = volume to order / capacity, rounded up. Every load is full
  except the last: last load (cy) = volume to order - (truckloads - 1) x
  capacity.
- Unload time for a full truck = capacity / placement rate (hours).
  Unload time for the last load = last load / placement rate.
- Start = the time the first truck should arrive.
- Load k (k = 1, 2, ...) arrives at start + (k - 1) x full-truck unload
  time, so the crew never waits. It leaves the plant one travel time
  before it arrives, unloads, drives back (one travel time), then washes
  out and reloads.
- Round trip for one truck = 2 x travel + full-truck unload time + washout.
- Trucks needed in rotation = round trip / full-truck unload time, rounded
  up (but no more than the number of loads). Loads are assigned to the
  trucks in turn: truck 1, truck 2, and so on, then truck 1 again.
- Cost estimate:
  - Concrete cost ($) = volume to order x concrete price.
  - A load is short if its size is less than the short-load threshold
    (a load exactly at the threshold is not short; a threshold of 0 means
    no load is short). Usually only the last load can be short, but every
    load is short if the truck capacity is under the threshold.
  - Short-load fees ($) = number of short loads x short-load fee.
  - Estimated cost ($) = concrete cost + short-load fees.
  - Dollars are shown rounded to the nearest dollar, with commas.
- Concrete timing checks:
  - Each load is batched when its truck leaves the plant (its arrival
    minus the travel time).
  - Age of a load when empty = travel time + that load's unload time.
    (Trucks arrive as the previous one finishes, so no load waits.)
  - Discharge check: a load is over the limit if its age when empty is
    more than the discharge limit (equal passes). The check passes if no
    load is over.
  - Set check: the first load starts to set at its batch time + the
    initial set time. The check passes if the last load arrives at or
    before that time; otherwise there is a cold-joint risk.
- Finish time = arrival of the last load + unload time for the last load.
- Pour duration = finish time - start.
- Rounding up (volume to order, truckloads, trucks in rotation) ignores
  floating-point error: a value within 1e-9 of a whole step is not bumped
  up to the next step.
- Calculations use full precision. On screen: area to the whole ft^2,
  net volume and volume with waste to 0.01 cy, volume to order with one
  decimal (47.0), load sizes without a decimal when whole (10 cy, 7 cy)
  and with one otherwise (7.5 cy), clock times to the nearest minute, and
  durations as "1 h 34 min" (or "10 min" under an hour).
- If there is no slab, or its area is zero, show a hint to draw or type a
  slab instead of results: the cards show a dash and the order box says
  to draw or type a slab. A time past midnight is shown with "(+1 day)".

## Outputs
- The slab drawn on the map with a label showing its area (ft^2) and
  volume to order (cy).
- Summary cards, in two rows of three: concrete to order (cy),
  truckloads, and estimated cost; then trucks in rotation, pour
  duration, and finish time. The "Concrete to order" card has a small
  detail line, for example "44.44 cy net · 46.67 cy with 5% waste". The
  "Estimated cost" card's detail line gives the concrete cost and the
  short-load fees, for example "47.0 cy × $165 · no short loads" or
  "5.0 cy × $165 + $100 short-load fee (1 load)". The cost is not part of
  the order summary text.
- A full-width "Timing checks" bar below the summary cards, one line per
  check, each with a green ✓ or an amber ⚠, for example:
  - "✓ Every load is empty within 90 min of batching (longest 40 min)."
  - "⚠ 4 of 5 loads take longer than 90 min from batching to empty (up
    to 95 min)."
  - "✓ Last truck arrives 8:20 AM, 80 min before the first load starts
    to set (9:40 AM)."
  - "⚠ Last truck arrives 8:20 AM, 40 min after the first load starts to
    set (7:40 AM). Risk of a cold joint."
  The timing checks are not part of the order summary text.
- On the timeline, a load over the discharge limit gets a red outline on
  its unloading bar and a ⚠ before its row label. A dashed red line
  labeled "First load sets" marks that time when it falls inside the
  chart.
- A truck schedule as a timeline (like a Gantt chart), drawn as inline
  SVG with no chart library:
  - One row per load, labeled like "Load 3 · Truck 3 · 10 cy".
  - Each row shows travel out, unloading, the trip back, and washout in
    four different colors, with a legend, on a clock-time axis.
  - A vertical line marks the finish time. Segments after the finish
    (the last trucks' trips back and washouts) are drawn faded.
  - Hovering a segment shows its name, start and end times, and length.
  - For very large pours, the timeline shows the first 60 loads and a
    note such as "Showing the first 60 of 412 loads." The summary cards
    and order text always cover every load.
  - With no slab, the timeline area shows the same hint as the cards.
- An "Order summary" box with a plain-text order for the batch plant and
  a "Copy" button. Template, filled in for validation case 1:
  "Order: 47.0 cy, 6 in slab (5% waste incl.). 5 loads: 4 x 10 cy +
  1 x 7 cy. First truck on site 7:00 AM, then one every 20 min. 4 trucks
  in rotation. Last truck done 8:34 AM."
  If every load is full, or there is only one load, the loads part is
  just "N x C cy" (for example "1 load: 1 x 5 cy"). With only one load,
  the spacing phrase is dropped and the trucks part reads "1 truck."
  (for validation case 3: "Order: 5.0 cy, 4 in slab (0% waste incl.).
  1 load: 1 x 5 cy. First truck on site 7:00 AM. 1 truck. Last truck
  done 7:10 AM.").
- Everything updates immediately whenever an input changes.

## Look and feel
- Opens with an example slab already drawn at the center of the map:
  the default typed slab (40 ft x 60 ft), so the app shows validation
  case 1 the moment it opens.
- Clean and professional, like a commercial construction app: white
  cards with soft shadows, one accent color (safety orange, #E8590C)
  with neutral grays for everything else, a modern system font, large
  readable numbers on the summary cards.
- Map and inputs on the left; summary, timeline, and order on the right.
  On a phone, everything stacks.
- A short hint tells the user what to do next, for example "Click Draw
  slab and click the corners, or type a length and width." While
  drawing: "Click each corner. Double-click or click the first point to
  finish."
- On touch screens, tapping a timeline segment shows the same details as
  hovering.

## Validation
1. Typed slab 40 ft x 60 ft x 6 in, 5% waste, 10 cy trucks, 20 min
   travel, 30 cy/hr, 10 min washout, first truck 7:00 AM:
   - Area 2,400 ft^2; 44.44 cy; with waste 46.67 cy; order 47.0 cy.
   - 5 truckloads (four of 10 cy and one of 7 cy).
   - Unload time 20 min, so trucks arrive at 7:00, 7:20, 7:40, 8:00,
     and 8:20.
   - Round trip 2 x 20 + 20 + 10 = 70 min, so 4 trucks in rotation.
   - Last truck holds 7 cy and unloads in 14 min, so the pour finishes
     at 8:34 AM. Pour duration 1 h 34 min.
2. Area: an outline with corners (40.2500, -111.6500),
   (40.2510, -111.6500), (40.2510, -111.6490), (40.2500, -111.6490) has
   an area of about 101,800 ft^2 (within 1%).
3. Typed slab 20 ft x 20 ft x 4 in, 0% waste, 10 cy trucks: 400 ft^2,
   4.94 cy, order 5.0 cy, 1 truckload, 1 truck, pour duration 10 min.
4. Rounding tolerance: typed slab 30 ft x 60 ft x 6 in, 5% waste, other
   inputs as in case 1: 1,800 ft^2; 33.33 cy net; 35.00 cy with waste
   (JavaScript computes 35.00000000000001); order 35.0 cy (not 35.5).
   4 truckloads (three of 10 cy and one of 5 cy). Arrivals 7:00, 7:20,
   7:40, 8:00. Last load unloads in 10 min, so the pour finishes at
   8:10 AM. Pour duration 1 h 10 min. 4 trucks in rotation.
5. Rotation cap and clamping: case 1 with 60 min travel: round trip
   2 x 60 + 20 + 10 = 150 min, 150 / 20 = 7.5, rounded up to 8, capped
   at 5 loads, so 5 trucks in rotation. Arrivals and finish are the same
   as case 1. Then type 30 in for thickness: it is clamped to 24 in and
   a note appears next to the box.
6. Cost estimate, default price $165/cy, threshold 6 cy, fee $100:
   - 6a. Case 1: loads 10, 10, 10, 10, 7 (7 cy is not short). Concrete
     47.0 x $165 = $7,755; no short loads; estimated cost $7,755.
   - 6b. Case 3: one 5 cy load, which is short. Concrete 5.0 x $165 =
     $825; fee $100; estimated cost $925.
   - 6c. Case 1 with 4 cy trucks: 12 loads (eleven of 4 cy and one of
     3 cy), all short. Concrete $7,755; fees 12 x $100 = $1,200;
     estimated cost $8,955.
   - 6d. Case 3 with threshold 0: no short loads; estimated cost $825.
7. Concrete timing, default limit 90 min and set time 180 min:
   - 7a. Case 1: full loads empty at 20 + 20 = 40 min, last load at
     20 + 14 = 34 min, so the discharge check passes (longest 40 min).
     First load batched 6:40 AM, sets 9:40 AM; last truck arrives
     8:20 AM, 80 min before. Both checks pass.
   - 7b. Case 1 with 75 min travel: loads 1 to 4 reach 75 + 20 = 95 min
     (over); load 5 reaches 75 + 14 = 89 min (not over). "4 of 5 loads",
     up to 95 min. First load batched 5:45 AM, sets 8:45 AM; last truck
     8:20 AM, 25 min before, so the set check passes.
   - 7c. Case 1 with set time 60 min: first load sets 6:40 + 60 =
     7:40 AM; last truck arrives 8:20 AM, 40 min after, so the set check
     fails. The discharge check passes.
   - 7d. Boundaries: case 1 with 70 min travel reaches exactly 90 min,
     which passes. Case 1 with set time 100 min sets at exactly 8:20 AM,
     the last arrival, which passes.

## Out of scope
- Reinforcement, finishing crews, pump trucks, and weather.
- Costs other than the concrete price and short-load fees (delivery,
  fuel surcharges, taxes, labor).
- Saving plans.
- Address search (the user pans and zooms the map by hand).

## Build stages
1. Layout mockup. index.html with the Leaflet satellite map centered on
   BYU, every input, the five summary cards, a placeholder timeline, and
   the order summary box, filled with validation case 1 values. No real
   calculations.
   Check: the layout matches Look and feel (map and inputs left, results
   right, orange accent, large numbers); narrowing the window stacks
   everything; satellite tiles load with attribution.
2. Calculations with a typed slab. Pure calculation functions (area,
   volumes, loads, schedule, trucks in rotation), separate from the
   drawing code. Typed length and width plus every input drive the
   summary cards and order text; out-of-range values clamp with a note.
   Check: typing validation cases 1, 3, 4, and 5 gives the expected cards
   and order text; 30 in thickness clamps to 24 in.
3. Map drawing. The typed rectangle drawn at the map center; Draw slab,
   draggable corners, and Clear; the area and volume label; the polygon
   area formula; the 3-corner rule, crossing-edge warning, and hint text.
   Check: case 2 run through the area function gives about 101,800 ft^2;
   an outlined building matches Google Maps' Measure distance; dragging a
   corner updates every output.
4. Truck timeline. The SVG Gantt chart: one row per load with its truck
   number, four colors and a legend, the finish line, faded segments after
   the finish, and hover/tap details.
   Check: case 1 shows 5 rows arriving 7:00 to 8:20 with the finish line
   at 8:34; case 5 shows trucks 1 to 5; hovering shows correct times.
5. Polish and edge cases. The Copy button, "(+1 day)" past midnight, the
   no-slab state, a phone layout pass, and no console errors.
   Check: every validation case still matches; the phone-width layout
   works; clearing the slab shows the hint; a late start time shows
   "(+1 day)".

## Resolved notes
Decisions from the spec review (Step 3), already written into the
sections above:
- Numeric inputs are number boxes with ranges; out-of-range values clamp.
- One slab at a time, last action wins; Clear button; dragging a typed
  rectangle's corner makes it a drawn outline.
- Truck capacity is a number box (1 to 12 cy, 0.5 cy steps).
- Typed slab area is exactly length x width.
- Standard display rounding; a tolerance stops floating-point error from
  bumping values up to the next rounding step.
- Timeline has one row per load, labeled with its truck number; segments
  after the finish are faded.
- The "Concrete to order" card shows net volume and volume with waste.
- Accent color is safety orange (#E8590C).
- Order summary follows the template in Outputs.
- Drawn outlines need 3+ corners; crossing edges show a warning.
- Added validation cases 4 (floating-point rounding) and 5 (rotation cap
  and clamping).
- Feature 1 (Step 6): cost estimate. Flat fee per short load (not per
  missing yard); defaults $165/cy, 6 cy threshold, $100 fee; shown on a
  sixth summary card only, not in the order text. Validation case 6.
- Feature 2 (Step 6): concrete timing checks. Both a per-load discharge
  limit (default 90 min) and a last-truck-before-first-load-sets check
  (default set time 180 min); concrete is batched when the truck leaves
  the plant; warnings on the page and timeline only, not in the order
  text. Validation case 7.
