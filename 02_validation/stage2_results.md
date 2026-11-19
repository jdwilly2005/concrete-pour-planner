# Stage 2 validation results

Run in the browser on index.html by setting each input box and firing the
same events typing does, then reading the cards and order text. Case 2 was
run through `PourCalc.polygonAreaFt2` directly (map drawing is stage 3).

| Case | Spec expects | App showed | Match |
|------|--------------|------------|-------|
| 1: 40 x 60 x 6 in, 5% | 47.0 cy; 44.44 net, 46.67 with waste; 5 loads (4 x 10 + 1 x 7); 4 trucks; arrivals 7:00, 7:20, 7:40, 8:00, 8:20; finish 8:34 AM; 1 h 34 min | Same; load 5 goes to truck 1 | Yes |
| 2: lat/lon outline | about 101,800 ft^2 (within 1%) | 101,804 ft^2 | Yes |
| 3: 20 x 20 x 4 in, 0% | 400 ft^2; 4.94 cy; 5.0 cy; 1 load; 1 truck; 10 min | 4.94 cy, 5.0 cy, 1 load, 1 truck, 10 min, finish 7:10 AM | Yes |
| 4: 30 x 60 x 6 in, 5% | 35.0 cy (not 35.5); 4 loads (3 x 10 + 1 x 5); finish 8:10 AM; 1 h 10 min; 4 trucks | Same (raw volume with waste is 35.00000000000001) | Yes |
| 5: case 1, 60 min travel | 150 min round trip; 5 trucks (capped); finish 8:34 AM | Same | Yes |
| 5: thickness 30 in | clamps to 24 in, note shown | Box shows 24, note "Maximum is 24 in. Using 24." stays after editing another box; order 192.0 cy at 8% waste | Yes |

Extra: first truck at 11:30 PM finishes at "1:04 AM (+1 day)".

Order text for case 1:
"Order: 47.0 cy, 6 in slab (5% waste incl.). 5 loads: 4 x 10 cy + 1 x 7 cy. First truck on site 7:00 AM, then one every 20 min. 4 trucks in rotation. Last truck done 8:34 AM."

Order text for case 3:
"Order: 5.0 cy, 4 in slab (0% waste incl.). 1 load: 1 x 5 cy. First truck on site 7:00 AM. 1 truck. Last truck done 7:10 AM."
