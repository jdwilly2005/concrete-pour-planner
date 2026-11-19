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
- Slab outline, either:
  - Drawn on the map: a "Draw slab" button, then click the corners and
    double-click (or click the first point) to finish. Corners can be
    dragged afterward. The map opens centered on BYU campus
    (40.2518, -111.6493) at zoom 18. Or:
  - Typed: length and width (ft) for a rectangular slab, which is drawn
    on the map as a rectangle of that size at the map center.
- Slab thickness (in), default 6.
- Waste allowance (%), default 5.
- Truck capacity (cubic yards), default 10.
- One-way travel time from the batch plant (min), default 20.
- Placement rate the crew can sustain (cubic yards per hour), default 30.
- Washout and reload time at the plant (min), default 10.
- Time the first truck should arrive, default 7:00 AM.

## Calculations
- Slab area (ft^2). For a drawn outline, use
  A = R^2 x |sum over edges of (lon2 - lon1) x (2 + sin lat1 + sin lat2)| / 2
  with angles in radians and R = 6,378,137 m, converted to ft^2
  (1 m^2 = 10.7639 ft^2).
- Volume to order (cubic yards) =
  area x thickness / 12 / 27 x (1 + waste / 100), rounded up to the next
  0.5 cy.
- Truckloads = volume / capacity, rounded up. Every load is full except
  the last.
- Unload time for a full truck = capacity / placement rate (hours).
- Trucks arrive one unload time apart, so the crew never waits:
  truck k (k = 1, 2, ...) arrives at start + (k - 1) x unload time.
- Round trip for one truck = 2 x travel + unload time + washout.
- Trucks needed in rotation = round trip / unload time, rounded up (but no
  more than the number of loads). Loads are assigned to the trucks in
  turn: truck 1, truck 2, and so on, then truck 1 again.
- Pour duration runs from the first truck's arrival until the last truck
  is empty.

## Outputs
- The slab drawn on the map with its area and volume on a label.
- Summary cards: concrete to order (cy), truckloads, trucks in rotation,
  pour duration, and finish time.
- A truck schedule as a timeline (like a Gantt chart): one row per
  truckload showing travel out, unloading, the trip back, and washout,
  in different colors, on a clock-time axis. Hovering shows the times.
- An "Order summary" box with a plain-text order for the batch plant
  (volume, number of loads, first arrival, spacing between trucks) and a
  "Copy" button.
- Everything updates immediately whenever an input changes.

## Look and feel
- Opens with an example slab already drawn near the center of the map,
  so it is useful the moment it opens.
- Clean and professional, like a commercial construction app: white
  cards with soft shadows, one accent color, a modern system font, large
  readable numbers on the summary cards.
- Map and inputs on the left; summary, timeline, and order on the right.
  On a phone, everything stacks.
- A short hint tells the user what to do next.

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

## Out of scope
- Reinforcement, finishing crews, pump trucks, weather, and costs.
- Saving plans.
