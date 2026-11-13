---
name: "composable-furniture"
description: "Design a piece of furniture (coffee bar, media wall, bar, desk, banquette, entry bench) by composing parts across IKEA, IKEA-compatible brands and Amazon into a verified build with 3D render, mood image and costed shopping list."
---

# Composable Furniture

Composable furniture treats a piece of furniture the way an architect treats a building: a **structure** you don't redesign, a **skin** that gives it character, and **fixtures** that make it work. Pieces come from any manufacturer. What makes them composable is a shared interface: they are made to fit a known frame system, or they can be checked against one by dimension and load.

The job of this skill is not to suggest products. It is to produce a build that **actually fits together, is still for sale, and looks like the render**.

## The composition model

Every build is described in eight layers. Fill them bottom-up.

| Layer | What it is | Typical sources |
|---|---|---|
| 1. Structure | Carcass / frame that carries load | IKEA systems: BESTÅ, METOD (SEKTION in US), KALLAX, EKET, IVAR, PAX, ENHET, HAVSTA |
| 2. Skin | Fronts, sides, tops, cover panels, legs | Semihandmade, Superfront, Reform, Norse Interiors, Pretty Pegs, Bemz/Comfort Works (soft goods) |
| 3. Surface | Work surface / countertop | IKEA countertops, butcher block, stone remnant, Semihandmade/Superfront tops |
| 4. Fixtures | Open shelving, rails, hooks, racks, organizers, lighting | Amazon, IKEA (KUNGSFORS, HULTARP), hardware stores |
| 5. Hardware | Pulls, hinges, anchors, levelers | Superfront, Pretty Pegs, Amazon |
| 6. Finish | Paint, trim, moulding, edge banding, backsplash | Hardware store / Amazon |
| 7. Appliances | Anything built in or placed on it that sets dimensions, load or power: beverage fridge, espresso machine, TV | Appliance dealers |
| 8. Services & consumables | Labor the build needs (electrician, tiler, countertop fabricator) and small supplies (wall rail, screws, thinset, grout, edge strip, sealant) | Local trades, hardware store |

Layer 8 is part of the build, not a footnote. Price what can be priced; list the rest under "Not priced" with a rough allowance.

### Compatibility tiers
Every part gets one tier. The tier is shown in the shopping list.

- **Native**: same system as the structure (IKEA BESTÅ door on BESTÅ frame).
- **Certified**: the third-party maker explicitly lists that exact IKEA system and size on its product page.
- **Adaptable**: fits by dimension but needs modification (cutting, drilling, spacers). State the tools and skill needed.
- **Independent**: does not join the frame (appliance, wall shelf, light, tile). Checked by dimension, clearance, load and power only.
- **Unverified**: no evidence it fits. Never include in the build; list as an alternate only if the user asks.

### Interfaces to check for every joint
- **Dimensions**: width/height/depth stack-up, overhangs, reveals, and clearance for anything that goes on or in it.
- **Mounting**: how it attaches (frame hinge holes, leg plate, wall rail, screw-on), and whether that is the same on the user's frame.
- **Load**: weight of top + contents + appliances vs. what the frame and shelves are rated for. If a rating isn't published, say so and size conservatively.
- **Anchoring**: tall or top-heavy pieces get anti-tip anchoring into studs. Always.
- **Power**: plug-in, UL-listed only (power strips, plug-in LED, cord grommets). Anything hardwired goes to a licensed electrician; say so. Add up the draw of everything on the circuit (an espresso machine plus a fridge can trip an ordinary circuit) and flag it.
- **Tolerance**: a wall-to-wall build never sums to exactly the wall width. Walls are not square or plumb. Leave at least 1/2 in (12 mm) total, taken up by a filler strip scribed on site, and say which part absorbs it. Check baseboards, outlets and switches in the footprint.
- **Appliance fit**: bay width, height and depth vs. the appliance's real measurements (a "15-inch" unit is not 15 in wide), door swing direction, and ventilation. A built-in fridge must vent from the front.

## Workflow

Keep a single build file (`build.json`, schema below) as the source of truth. The render, the mood brief, and the shopping list are all generated from it, so they can never disagree.

### 1. Brief
Do not start a build on assumptions. If any of the first three items below is missing, ask before researching. A wrong guess here means a full rebuild.
- **Reference photo first.** If the user has one, start from it: decompose it into the eight layers, note anything that looks AI-generated or not purchasable, and confirm the reading with the user before sourcing.
- **Form**: freestanding or built-in wall to wall, and the finished height (sideboard ~30 in, counter 36 in, bar 42 in).
- **Exact wall width** for built-ins, measured at floor, counter height and top.

Then get, asking only for what's missing:
- What it's for and what lives on/in it (e.g., espresso machine + grinder + 30 mugs).
- Space: wall width, ceiling height, outlet location, any obstructions. Floor vs. wall-hung.
- Style words, if there is no reference photo.
- Sources the user wants used: Amazon storefront or product links, favorite shops.
- Whether a photoreal mood image is wanted, if generating one costs the user credits.
- Budget ceiling, country (availability differs by region), owner vs. renter (affects wall mounting), and tools/skill level.

### 2. Pattern
Height and form pick the structure system, so settle them before anything else: BESTÅ on legs lands near 30 in (sideboard); SEKTION/METOD base cabinets land at 36 in (counter) and are the route for built-ins and built-in appliances.

Pick an archetype and decompose it into **zones** before choosing products. Pattern library:

- **Coffee bar**: closed base storage, work surface at counter height, vertical display/open shelving, appliance power, task lighting, optional water/trash.
- **Media console**: low closed storage, cable management, ventilation for electronics, optional wall shelving.
- **Home bar**: base storage with bottle/glass organization, durable surface, upper display, lighting.
- **Desk / workstation**: storage pedestals, long top, cable tray, task light.
- **Banquette / window seat**: base cabinets as bench, cushion, top, legs or plinth.
- **Entry / mudroom**: bench storage, hooks, upper cubbies.
- **Pantry or storage wall**: tall frames, fronts, interior organizers, built-in trim.

State the zone dimensions you're designing to (e.g., counter top at ~36 in / 91 cm for standing prep, work-surface depth, clearance above the machine for a top-loading water tank).

### 3. Structure
Search the retailer live for the frame system that fits the zones. For each candidate capture: product name, article number, exact dimensions, price, region, and that it is **currently sold** (discontinued bases are a common failure; check). Prefer systems with a large third-party ecosystem (BESTÅ, METOD/SEKTION, PAX) when the user wants a custom look.

### 4. Skin, surface, fixtures, hardware, finish
For each layer, search for parts against the chosen frame:
- Third-party fronts/legs/tops: confirm on the maker's page that the exact frame and size are supported, and check shipping to the user's country (some brands ship legs and handles worldwide but fronts only regionally).
- Amazon: web search cannot verify Amazon listings (specs, price, availability). Use one of these routes, in order: (1) links or a storefront URL the user pastes, read with a browser tool; (2) a browser tool searching Amazon directly; (3) if neither is available, source the part from a retailer whose page can be read, and tell the user Amazon was skipped and why. Never add an Amazon part from a search snippet.
- Other generic parts: use listed dimensions and weight ratings; choose listings with clear specs. Record the listing URL, not a search URL.
- For every third-party part also capture: ships to the user's region (yes/no), lead time, minimum order or small-order fee.
Never invent an article number, price, or URL. If a value can't be found, leave it blank and flag it.

### 5. Compatibility check
Run the checks from "Interfaces" as an explicit table: joint, check, result (pass/adapt/fail), note. Compute the stack-up with real numbers (frame height + legs/plinth + top thickness = finished height). Any fail goes back to step 3 or 4. Report the final pass table to the user.

Also produce a **cut list**: every part that must be cut, ripped or drilled, with from-size, to-size, tool, and who does it (user or trade). Check the width sum against the wall with the tolerance rule.

### 6. 3D render (source of truth)
Build a self-contained HTML page with Three.js that reads the parts from `build.json`:
- Each part is a box (or simple primitive) at its real dimensions and position, in centimetres internally, with inch labels if the user is US-based.
- Color/material approximates the chosen finish; a toggle colors parts by **vendor** instead.
- Controls: orbit, **exploded view** (parts separate along their assembly axis), dimension callouts for overall W/H/D and key heights, click a part to see name, vendor, tier, price.
- If a 3D library can't load, fall back to an isometric SVG of the same parts (use the isometric-design skill if available).
The render must show only parts that are in the shopping list.

Render rules learned from real builds:
- Load Three.js from cdnjs (r128 works in artifacts). Write orbit, zoom and pinch by hand; do not depend on add-on control scripts.
- Draw the wall and floor as context, a dashed box for reserved appliance clearance, and glass fronts on glass-door appliances.
- Always include the flat front-elevation fallback, drawn from the same parts.
- **Verify before delivering**: open the page in a headless browser, click every control, check desktop and phone widths, and look at a screenshot. If the 3D library cannot load in the test environment, say plainly that the 3D view is unverified. Never describe a render you have not seen as working.

### 7. Mood shot (default, labeled)
The user expects to see the finished piece, so make this by default. Use whatever image-generation tool the session has; if it spends the user's credits, use the answer from the brief. If no tool is available, say so at delivery and give the prompt so the user can run it elsewhere. Generate one styled image from a prompt written **from build.json** (materials, colors, proportions, what's displayed on it). Label it on the page: *"Mood image: approximate. The 3D model and shopping list are what you'd actually build."* Never use the mood image to justify a part.

### 8. Deliverable
One HTML page, published as an artifact, with sections in this order:
1. **Hero**: 3D render (with exploded toggle) and the mood shot beside it.
2. **Bill of materials**: layer, part, vendor, article/SKU, dimensions, qty, unit price, line total, link, compatibility tier.
3. **Cost summary**: by vendor and by layer, then landed cost: parts + fees and minimum-order charges + shipping estimate + services + a "Not priced" allowance. Show the date prices were checked. Where one layer swings the total (usually surface or skin), show good / better / best for that layer only.
4. **Compatibility checks**: the pass table from step 5.
5. **Build sequence**: ordered steps (assemble frames, level and anchor, mount skin, set top, fixtures, finish), tools needed, estimated time.
6. **Cut list and schedule**: the cut list from step 5, and an order-by sequence driven by the longest lead time.
7. **Risks and alternates**: discontinuation risk, lead times, warranty note (modifying IKEA parts can affect warranty), and one cheaper and one upgraded swap where it matters.

## build.json schema

```json
{
  "name": "Coffee bar",
  "units": "cm",
  "region": "US",
  "checked_on": "YYYY-MM-DD",
  "envelope": { "w": 0, "h": 0, "d": 0 },
  "zones": [ { "id": "base", "purpose": "closed storage", "target": { "w": 0, "h": 0, "d": 0 } } ],
  "parts": [
    {
      "id": "frame-1",
      "layer": "structure",
      "name": "",
      "vendor": "",
      "sku": "",
      "url": "",
      "price": 0,
      "currency": "USD",
      "qty": 1,
      "dims": { "w": 0, "h": 0, "d": 0 },
      "position": { "x": 0, "y": 0, "z": 0 },
      "attaches_to": null,
      "tier": "native",
      "ships_to_region": true,
      "lead_time_weeks": 0,
      "fees": 0,
      "finish": "",
      "notes": ""
    }
  ],
  "wall": { "w": 0, "tolerance": 1.2, "absorbed_by": "" },
  "clearance": [ { "for": "espresso machine", "w": 0, "h": 0, "d": 0 } ],
  "cuts": [ { "part": "", "from": "", "to": "", "tool": "", "by": "user" } ],
  "services": [ { "trade": "", "task": "", "estimate": 0 } ],
  "not_priced": [ "" ],
  "checks": [ { "joint": "", "check": "", "result": "pass", "note": "" } ]
}
```

## Worked example: coffee bar (structure only, verify every product live)

- **Zones**: base storage (~150 cm wide), work surface at counter height, open shelving above, plug-in power strip + under-shelf LED.
- **Structure**: two or three BESTÅ frames on legs, or SEKTION/METOD base cabinets for true counter height. Pick based on target height after the stack-up: BESTÅ on legs typically lands well below counter height, so it suits a lower "sideboard" bar. Base cabinets suit a standing prep height.
- **Skin**: Certified fronts from Semihandmade or Superfront for that frame; legs from Pretty Pegs or Superfront if on legs.
- **Surface**: a top deep enough to cover the frame plus a small overhang; check that its weight and the espresso machine's are within what the frame can carry.
- **Fixtures**: wall shelving with brackets into studs, mug hooks or a rail, UL-listed plug-in LED strip, cord grommet through the top.
- **Checks that usually fail first**: finished height vs. the user's intent, machine clearance under the shelf, outlet reach, and anti-tip anchoring.

## Known fit rules

Verified on US sites, 2026-11-12. Use these to skip re-research of fit; always re-check price and availability live.

**IKEA BESTÅ**
- 47 1/4 in frame is 47 1/4 x 15 3/4 x 25 1/4 in and needs 4 legs plus 1 BESTÅ supporting leg. IKEA legs are 3 7/8 in, adjustable to 4 3/8 in.
- Legs + frame + 3/4 in top panel is about 30 in finished. Sideboard height, not counter height.
- Doors are 23 5/8 x 25 1/4 in, two per 47 1/4 in frame; one hinge pack per door. Push-open hinges mean no handles.
- Top panel 47 1/4 x 16 1/2 in covers frame plus doors; max load 110 lb.
- IKEA flags it as a tipping hazard: always anchor.

**IKEA SEKTION (US)**
- 30 in base frame + 4 1/2 in legs (adjustable 3 1/2 to 5 1/8 in) + 1 1/2 in top = 36 in counter.
- Two 30 x 15 in drawer fronts on MAXIMERA high drawers fill a 30 in frame. Legs, plinth and suspension rail are sold separately.

**Third-party makers**
- Semihandmade: fronts, panels, trim and toekick for SEKTION and BESTÅ. Parts are 3/4 in thick (IKEA is about 5/8 in). Painted parts take 6 to 8 weeks; walnut 8 to 12 weeks and needs a PDF plan to check out. Its pages disagree on small painted orders ($100 fee under $700 vs. $700 minimum): confirm.
- Fronteriors: doors, sides, tops and legs for BESTÅ, made to order in 20 to 25 working days.
- Pretty Pegs: guarantees fit on BESTÅ; check the specific leg is not discontinued.
- Bo Fronts: real-wood SEKTION fronts, but US orders were paused at this check.
- Superfront: verify what ships to the US before using fronts.

**Appliances**
- A "15 in" undercounter beverage fridge measured 14 3/4 W x 33 7/8 H x 22 3/4 D in. Make the bay at least 1 in wider than the unit. It must be front-venting, and its door should hinge away from any side panel.

## After every build

End with a short "What this build taught" list: new fit rules, makers that failed a check, sourcing dead ends. Offer to add them to Known fit rules by updating this skill, so the next build starts from them.

## Refreshing an old build

When asked to re-check a build, reload its build.json, re-verify every part's price, availability and regional shipping, mark what changed, replace anything discontinued with a part of the same tier, and regenerate the page.

## Test prompts

Use these to check the skill after changing it:
1. "Two-door coffee bar" with nothing else. Expected: asks form, height and width before sourcing.
2. A reference photo plus "4 ft wall". Expected: layer-by-layer reading of the photo, built-in at 36 in, tolerance and cut list present.
3. "Media console under a 65 in TV, renter, $400". Expected: no wall-hung structure, ventilation and cable checks, landed cost under budget.

## Guardrails
- Only real, current products with working links; record the date checked.
- Never mix up regional variants (IKEA METOD vs. SEKTION; EU vs. US sizes).
- Don't claim a third-party part fits unless its maker says so (Certified) or you've shown the dimensional proof (Adaptable).
- Electrical, plumbing, and structural wall work beyond stud-anchoring: recommend a licensed pro.
- The render and the shopping list come from the same build.json. If you change a part, regenerate both.
- State at delivery anything the brief asked for that the build does not have (an unused source, a missing mood image, an unverified render).