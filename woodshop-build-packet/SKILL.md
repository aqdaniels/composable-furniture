---
name: "woodshop-build-packet"
description: "Turn a furniture design (Fusion model, description, sketch or photo) into a build packet: price sheet, dimensioned blueprints, cut list with cutting diagrams, pocket-hole schedule and step-by-step instructions, for a miter saw and Kreg jig shop. Use for \"build packet\", \"price out this bench\", \"plans/blueprints for\", \"cut list\", \"what lumber do I need\", \"how do I build this\"."
---

# Woodshop build packet

Take one furniture design and produce one packet the user can carry into the lumber aisle and then into the shop: what to buy and what it costs, drawings with every dimension, which cuts come from which board, and the build steps in order.

The user builds with a **miter saw and a Kreg pocket-hole jig**. Design and plan for those tools. A plan that needs a tool the user doesn't own is a wrong plan, however good it looks.

## The three rules that prevent ruined lumber

1. **Lumber names lie.** A 2x4 is 1-1/2" x 3-1/2". 3/4" plywood is 23/32". Every dimension in the packet uses the real size. The script's tables are the source of truth; don't do this from memory.
2. **The space is not the part.** A 45" frame with a leg at each end has a 38" apron, not a 45" one. Derive each part length by subtracting what it sits between, then prove it with a dimension check (below).
3. **The blade eats 1/8".** Two 48" parts do not come out of one 96" board. The script accounts for this; trust its board count over intuition.

## Workflow

### 1. Get the design

Work from whichever the user gives you:

- **A Fusion model.** If Autodesk Fusion connector tools are available, read the open design: list the components/bodies and take each one's name and bounding-box size. Fusion may report centimetres internally - convert to inches. Match each body's cross-section to a stock size (1.5 x 3.5 is a 2x4). A body that matches no stock size is a finding: tell the user and propose the nearest stock, because they cannot make a 2-1/4" wide board without a rip saw.
- **A description, sketch or photo.** Settle overall width, depth and height and what the piece must hold, then design the parts yourself. If an overall dimension is missing, ask for it; don't guess the size of someone's furniture.

If there are no Fusion tools in the session, say so in one line and carry on from the description. The packet does not depend on Fusion.

### 2. Confirm the tool profile

Assume this unless the user has said otherwise, and state the assumption on the packet's cover:

| Tool | Default | Why it matters |
|---|---|---|
| Miter saw crosscut | 5-1/2" (cuts a 2x6 in one pass) | Wider boards need cut-flip-cut; the script warns |
| Rip saw (table, circular or track saw) | none | No ripping boards narrower; plywood gets cut at the store |
| Kreg jig, drill/driver, tape, square, clamps, sander | yes | Needed for every build |

Ask about tools only when the answer changes the design (for example, a piece that is mostly plywood). If the user tells you a tool they own, use it and note it.

### 3. Design for these tools

- Prefer dimensional lumber (1x and 2x boards) crosscut to length. Every part should be a stock width.
- Join with pocket holes plus glue. Drill pockets into the **end of the rail** going into the leg or panel, on the face nobody sees (inside, underside, back).
- Use plywood only for shelves, backs and panels, sized so the store can make the cuts (straight, full-width, a few of them).
- Angles: stick to 90 and 45 unless the design demands more; a miter saw does other angles but each one is a chance to get it wrong.
- Sanity-check strength the way a builder would: a shelf of 3/4" stock spanning more than about 32" sags and wants a support or a front rail; a bench or table wants aprons under the top; seating should use 2x stock for legs and aprons.

### 4. Write design.json and run the script

Save the script at the end of this file as `shopmath.py`, write the design as `design.json`, and run:

```
python3 shopmath.py design.json > result.json
```

`design.json` format (all inches):

```json
{
  "name": "Entry bench 48 x 16-1/2 x 18",
  "species": "softwood",
  "tools": {"miter_max_crosscut": 5.5, "can_rip": false},
  "sales_tax_pct": 9.45,
  "prices": {
    "2x4": {"96": 3.98, "120": 5.48},
    "2x6": {"96": 6.48},
    "ply-3/4": {"sheet": 54.98},
    "screws": {"2-1/2\"": 6.98}
  },
  "parts": [
    {"name": "Leg", "stock": "2x4", "length": 16.5, "qty": 4},
    {"name": "Long apron", "stock": "2x4", "length": 38, "qty": 2},
    {"name": "Seat slat", "stock": "2x6", "length": 48, "qty": 3},
    {"name": "Shelf", "stock": "ply-3/4", "width": 10, "length": 38}
  ],
  "joints": [
    {"name": "Apron to leg", "pocket_in": "2x4", "holes": 2, "qty": 8}
  ],
  "hardware": [{"item": "Wood glue, 8 oz", "qty": 1, "unit_price": 4.48}],
  "dimension_checks": [
    {"name": "Height", "adds": [16.5, 1.5], "should_equal": 18},
    {"name": "Frame length", "adds": [3.5, 38, 3.5], "should_equal": 45}
  ]
}
```

- `stock` is a lumber name (`1x2` to `1x12`, `2x2` to `2x12`, `4x4`) or a sheet (`ply-1/4`, `ply-1/2`, `ply-3/4`, `mdf-3/4`). Sheet parts need `width` and `length`.
- `prices` keys for lumber are board lengths in inches (96 = 8 ft). List every length the store sells so the script can pick the cheapest mix.
- `pocket_in` is the board the pockets are drilled into; the jig setting follows that board's thickness.
- `species`: `softwood` (pine, fir, plywood - coarse-thread screws) or `hardwood` (oak, maple, poplar - fine-thread).
- **Write a dimension check for every overall dimension** - height, width, depth - listing the real part sizes that stack up to it. This is the step that catches rule 2 errors, so don't skip it or fill it in to match; work out the stack from the parts.

If `result.json` has `"ok": false`, fix the design and rerun. Don't ship a packet with errors. Warnings go into the packet as shop notes. Look at the leftovers too: three boards each leaving 48" usually means a part is a hair too long for two-per-board, and it is worth asking whether 47-7/8" would do.

### 5. Price it

Pricing is part of the packet, not an optional extra: the user asked for a price sheet, so a packet without costs and material links is unfinished. Do this step every time, before building the page.

Ask which store and ZIP if you don't know (Home Depot and Lowe's price by location); if the user isn't there to answer, use Home Depot and say so. Then, for every line the script will put on the price sheet - each board size and length, each sheet, each screw box, glue and finishing supplies - search the web for that exact product at that store and capture two things:

- **The product page link.** The specific product (for example "2 in. x 4 in. x 8 ft. prime kiln-dried whitewood stud"), not the store's home page. Every row on the price sheet carries its link so the user can click through, check stock at their store and add it to a cart.
- **Today's price**, with the date you saw it. Lumber prices move weekly.

Put the prices into `design.json` and rerun so the totals come from the script. Keep the links in a simple item-to-URL list for step 6.

Store sites often block page fetches or hide the price from search results. When that happens for an item:

- Still give a link: the product page if you found it, otherwise a store search link for the item (for example `https://www.homedepot.com/s/2x4%208%20ft%20stud`). A row never goes out with no link.
- Try the other big-box store for a price before giving up, and label which store the price came from.
- If no price turns up, leave it out. The script lists it under `unpriced_items`; show it on the sheet as "price not found - check link" and never fill in a remembered or guessed number - a price sheet with invented prices is worse than one with a gap.
- If web search is not available in the session at all, say that plainly at the top of your reply and on the price sheet, deliver the packet with links and quantities, and ask the user to paste the prices so you can finish the totals. Don't present an unpriced packet as complete.

Also:

- Add finishing supplies the build needs (sandpaper, stain or paint, wood filler) as hardware lines.
- Use the user's local sales tax rate if known; otherwise show the subtotal and say tax is not included.

### 6. Build the packet

Deliver one self-contained HTML page (inline CSS and SVG, no external files), printable in black and white, letter size, with a page break before each section. Sections in this order:

1. **Cover** - piece name, overall size, a three-quarter view drawing (or a Fusion screenshot if one is available), tool profile assumed, total cost, estimated build time.
2. **Price sheet** - one row per thing bought: item (as a clickable link to the store product page, with the store's product name), quantity, unit price, total, store and date priced. Subtotal, tax, total. State how many rows are priced out of how many ("9 of 9 items priced"), and if any are missing, label the total "partial". Then a "you'll have left over" line (offcuts worth keeping).
3. **Blueprints** - front, side and top views as SVG, drawn to scale from the parts list, with dimension lines for every overall size and every part length, in tape-measure fractions (14-1/2", never 14.5). Label each part with a letter (A, B, C) that matches the cut list. Add one exploded view showing how the parts meet.
4. **Cut list** - letter, part, quantity, stock, real thickness x width x length, cut angle.
5. **Cutting diagrams** - one bar per store board showing the cuts in order with the leftover shaded; one rectangle per plywood sheet with the panels positioned from the script's x/y values. This is the page that goes to the store and the saw.
6. **Pocket-hole schedule** - per joint: which board gets the pockets, which face, how many, jig setting, drill-collar setting, screw length and thread.
7. **Step-by-step instructions** - numbered, in build order, one action per step, each naming the parts by letter and the tool used, with a small drawing for any step where orientation matters.
8. **Shop notes** - the script's warnings, plus anything to watch for.

Order the steps the way the work actually goes, because that is where first builds go wrong:

1. Cut all parts, labelling each with its letter in pencil as it comes off the saw. Cut the longest parts from each board first.
2. Drill all pocket holes before any assembly - reaching into an assembled frame with the jig is miserable.
3. Sand faces that will be hard to reach later.
4. Build flat sub-assemblies first (two end frames), then join them, then add the top, then shelves and backs.
5. Check for square after each sub-assembly by measuring both diagonals; equal means square.
6. Fill, final sand, finish.

Put safety where it applies, in the step itself (eye and ear protection at the saw, clamp the work before drilling), not in a generic block.

### 7. Check before delivering

- `result.json` says `"ok": true`.
- Every number on the blueprints matches the cut list, and every cut-list row appears in a cutting diagram.
- The price sheet total equals the script's total.
- Every price sheet row has a working-looking link to a specific product or a store search, and every priced row names its store and date. If the sheet has no prices at all, stop and go back to step 5 - that is a failed packet, not a finished one.
- Open the HTML and look at it: dimension text readable, no overlapping labels, diagrams to scale.

Then give the user the packet with a short summary: total cost (or "partial" with what is missing), board count, and anything that needs their decision. Repeat the price sheet as a small table in the reply itself, with the links, so the cost and materials are visible without opening the packet.

## Reference: shopmath.py

```python
#!/usr/bin/env python3
"""shopmath.py - checks a furniture parts list and works out boards, cuts and cost.

Usage: python3 shopmath.py design.json > result.json
All dimensions are inches. See SKILL.md for the design.json format.
"""
import json, sys
from fractions import Fraction

KERF = 0.125  # saw blade width lost on every cut

# nominal name -> (actual thickness, actual width)
LUMBER = {
    "1x2": (0.75, 1.5), "1x3": (0.75, 2.5), "1x4": (0.75, 3.5), "1x6": (0.75, 5.5),
    "1x8": (0.75, 7.25), "1x10": (0.75, 9.25), "1x12": (0.75, 11.25),
    "2x2": (1.5, 1.5), "2x3": (1.5, 2.5), "2x4": (1.5, 3.5), "2x6": (1.5, 5.5),
    "2x8": (1.5, 7.25), "2x10": (1.5, 9.25), "2x12": (1.5, 11.25),
    "4x4": (3.5, 3.5),
}
# sheet name -> actual thickness (sold labelled 1/4, 1/2, 3/4; real panels run thin)
SHEETS = {"ply-1/4": 0.21875, "ply-1/2": 0.46875, "ply-3/4": 0.71875, "mdf-3/4": 0.75}
SHEET_W, SHEET_L = 48.0, 96.0

# Kreg chart: (max material thickness, jig/collar setting, screw length)
KREG = [(0.5, '1/2"', 1.0), (0.625, '5/8"', 1.0), (0.75, '3/4"', 1.25),
        (0.875, '7/8"', 1.5), (1.0, '1"', 1.5), (1.125, '1-1/8"', 1.5),
        (1.25, '1-1/4"', 2.0), (1.375, '1-3/8"', 2.0), (1.5, '1-1/2"', 2.5)]


def frac(x, den=16):
    """Decimal inches -> tape-measure text to the nearest 1/16 (or 1/den), e.g. 14.5 -> 14-1/2\""""
    f = Fraction(round(x * den), den)
    whole, rem = divmod(f.numerator, f.denominator)
    if rem == 0:
        return f'{whole}"'
    part = f"{rem}/{f.denominator}"
    return f'{whole}-{part}"' if whole else f'{part}"'


def thickness(stock):
    if stock in LUMBER:
        return LUMBER[stock][0]
    if stock in SHEETS:
        return SHEETS[stock]
    raise ValueError(f"unknown stock '{stock}'")


def kreg(stock, species="softwood"):
    """Pocket-hole setting for the board the pockets are drilled INTO."""
    t = thickness(stock)
    # plywood sold as 1/2 or 3/4 is set by its label, not its thin real size
    if stock in SHEETS:
        t = {0.21875: 0.25, 0.46875: 0.5, 0.71875: 0.75}.get(t, t)
    if t < 0.5:
        return {"ok": False, "reason": f"{stock} is too thin for pocket holes; use glue and brads"}
    for max_t, setting, screw in KREG:
        if t <= max_t + 1e-9:
            thread = "fine" if species == "hardwood" else "coarse"
            return {"ok": True, "jig_setting": setting, "screw_length": frac(screw), "thread": thread}
    return {"ok": False, "reason": f"{stock} is thicker than the jig handles; use lag screws or structural screws"}


def pack_boards(lengths, stock_lengths, prices):
    """Fit cut lengths onto store boards. Tries each store length for each new
    board and keeps the cheapest overall plan (first-fit decreasing per length mix)."""
    lengths = sorted(lengths, reverse=True)
    longest = max(stock_lengths)
    if lengths and lengths[0] > longest:
        raise ValueError(f"a {frac(lengths[0])} part is longer than the longest board ({frac(longest)})")
    best = None
    # try: all boards of one length, for every available length; keep cheapest
    for L in stock_lengths:
        if lengths and lengths[0] > L:
            continue
        boards = []
        for cut in lengths:
            for b in boards:
                if b["used"] + KERF + cut <= L + 1e-9:
                    b["cuts"].append(cut); b["used"] += KERF + cut
                    break
            else:
                boards.append({"length": L, "cuts": [cut], "used": cut})
        cost = len(boards) * prices.get(str(int(L)), 0)
        if best is None or cost < best[0] or (cost == best[0] and len(boards) < len(best[1])):
            best = (cost, boards)
    cost, boards = best
    # shrink any board that would fit on a shorter, cheaper one
    for b in boards:
        for L in sorted(stock_lengths):
            if b["used"] <= L + 1e-9 and prices.get(str(int(L)), 0) <= prices.get(str(int(b["length"])), 0):
                b["length"] = L
                break
        b["waste"] = round(b["length"] - b["used"], 4)
    return boards


def pack_sheets(panels):
    """Shelf-pack rectangular panels (w, l, name) onto 4x8 sheets. Returns sheets
    with x/y positions so a cutting diagram can be drawn."""
    items = []
    for w, l, name in panels:
        w, l = sorted((w, l))
        if w > SHEET_W or l > SHEET_L:
            raise ValueError(f"panel '{name}' ({frac(w)} x {frac(l)}) does not fit on a 4x8 sheet")
        items.append((w, l, name))
    items.sort(key=lambda p: (-p[0], -p[1]))
    sheets = []
    for w, l, name in items:
        placed = False
        for s in sheets:
            for shelf in s["shelves"]:
                if w <= shelf["h"] + 1e-9 and shelf["x"] + l <= SHEET_L + 1e-9:
                    s["panels"].append({"name": name, "x": shelf["x"], "y": shelf["y"], "w": w, "l": l})
                    shelf["x"] += l + KERF; placed = True; break
            if placed:
                break
            if s["next_y"] + w <= SHEET_W + 1e-9:
                shelf = {"y": s["next_y"], "h": w, "x": l + KERF}
                s["shelves"].append(shelf)
                s["panels"].append({"name": name, "x": 0.0, "y": shelf["y"], "w": w, "l": l})
                s["next_y"] += w + KERF; placed = True; break
        if not placed:
            sheets.append({"shelves": [{"y": 0.0, "h": w, "x": l + KERF}], "next_y": w + KERF,
                           "panels": [{"name": name, "x": 0.0, "y": 0.0, "w": w, "l": l}]})
    for s in sheets:
        used = sum(p["w"] * p["l"] for p in s["panels"])
        s["used_pct"] = round(100 * used / (SHEET_W * SHEET_L))
        del s["shelves"], s["next_y"]
    return sheets


def main(path):
    d = json.load(open(path))
    tools = d.get("tools", {})
    max_cross = tools.get("miter_max_crosscut", 5.5)   # widest board the miter saw cuts in one pass
    can_rip = tools.get("can_rip", False)              # table saw / circular saw / track saw
    prices = d.get("prices", {})
    out = {"errors": [], "warnings": [], "cut_list": [], "boards": {}, "sheets": {},
           "pocket_holes": [], "price_sheet": [], "checks": []}

    by_stock, panels = {}, {}
    for p in d["parts"]:
        stock, qty = p["stock"], int(p.get("qty", 1))
        try:
            t = thickness(stock)
        except ValueError as e:
            out["errors"].append(str(e)); continue
        if stock in LUMBER:
            w = LUMBER[stock][1]
            if p.get("width") and abs(p["width"] - w) > 0.01:
                msg = f"'{p['name']}' needs {stock} ripped to {frac(p['width'])} wide"
                (out["warnings"] if can_rip else out["errors"]).append(
                    msg + ("" if can_rip else " - no rip saw in the tool list; pick a stock width instead"))
                w = p["width"]
            if LUMBER[stock][1] > max_cross + 1e-9:
                out["warnings"].append(f"'{p['name']}': {stock} is {frac(LUMBER[stock][1])} wide, more than the "
                                       f"miter saw's {frac(max_cross)} cut - cut, flip the board, and cut again")
            by_stock.setdefault(stock, []).extend([p["length"]] * qty)
            out["cut_list"].append({"part": p["name"], "qty": qty, "stock": stock,
                                    "thickness": frac(t, 32), "width": frac(w), "length": frac(p["length"]),
                                    "angle": p.get("angle", "90")})
        else:
            panels.setdefault(stock, []).extend([(p["width"], p["length"], p["name"])] * qty)
            out["cut_list"].append({"part": p["name"], "qty": qty, "stock": stock,
                                    "thickness": frac(t, 32), "width": frac(p["width"]), "length": frac(p["length"]),
                                    "angle": "90"})
            if not can_rip:
                out["warnings"].append(f"'{p['name']}' is a sheet-goods panel and there is no rip saw in the "
                                       "tool list - have the store cut it; bring the sheet diagram")

    for stock, lengths in by_stock.items():
        sp = prices.get(stock, {})
        stock_lengths = sorted(float(k) for k in sp) or [96.0]
        try:
            boards = pack_boards(lengths, stock_lengths, sp)
        except ValueError as e:
            out["errors"].append(f"{stock}: {e}"); continue
        out["boards"][stock] = [{"board_length": frac(b["length"]), "cuts": [frac(c) for c in b["cuts"]],
                                 "leftover": frac(b["waste"])} for b in boards]
        counts = {}
        for b in boards:
            counts[b["length"]] = counts.get(b["length"], 0) + 1
        for L, n in sorted(counts.items()):
            unit = sp.get(str(int(L)))
            out["price_sheet"].append({"item": f"{stock} x {int(L // 12)} ft", "qty": n, "unit_price": unit,
                                       "total": round(n * unit, 2) if unit is not None else None})
        # check: every inch of every board is accounted for
        need = sum(lengths)
        got = sum(sum(b["cuts"]) for b in boards)
        out["checks"].append({"stock": stock, "parts_total_in": round(need, 3),
                              "placed_total_in": round(got, 3), "ok": abs(need - got) < 1e-6})

    for stock, plist in panels.items():
        try:
            sheets = pack_sheets(plist)
        except ValueError as e:
            out["errors"].append(str(e)); continue
        out["sheets"][stock] = sheets
        unit = prices.get(stock, {}).get("sheet")
        out["price_sheet"].append({"item": f"{stock} 4x8 sheet", "qty": len(sheets), "unit_price": unit,
                                   "total": round(len(sheets) * unit, 2) if unit is not None else None})
        out["checks"].append({"stock": stock, "panels": len(plist),
                              "placed": sum(len(s["panels"]) for s in sheets),
                              "ok": len(plist) == sum(len(s["panels"]) for s in sheets)})

    screws = {}
    for j in d.get("joints", []):
        k = kreg(j["pocket_in"], d.get("species", "softwood"))
        row = {"joint": j["name"], "pockets_drilled_in": j["pocket_in"], "holes": int(j.get("holes", 2)),
               "qty": int(j.get("qty", 1)), **k}
        out["pocket_holes"].append(row)
        if k["ok"]:
            key = f'{k["screw_length"]} {k["thread"]}-thread pocket screws'
            screws[key] = screws.get(key, 0) + row["holes"] * row["qty"]
        else:
            out["errors"].append(f"joint '{j['name']}': {k['reason']}")
    for name, n in screws.items():
        box = prices.get("screws", {}).get(name.split(" ")[0])
        out["price_sheet"].append({"item": name, "qty": n, "unit_price": None,
                                   "total": box, "note": "price is per box; count is screws needed"})

    for extra in d.get("hardware", []):
        tot = round(extra["qty"] * extra["unit_price"], 2) if extra.get("unit_price") is not None else None
        out["price_sheet"].append({"item": extra["item"], "qty": extra["qty"],
                                   "unit_price": extra.get("unit_price"), "total": tot})

    # overall dimension checks supplied by the designer: each is a list of parts that must add up
    for c in d.get("dimension_checks", []):
        total = sum(c["adds"])
        ok = abs(total - c["should_equal"]) < 1/32
        out["checks"].append({"check": c["name"], "adds_to": frac(total), "target": frac(c["should_equal"]), "ok": ok})
        if not ok:
            out["errors"].append(f"dimension check '{c['name']}': parts add to {frac(total)}, target is {frac(c['should_equal'])}")

    known = [r["total"] for r in out["price_sheet"] if r["total"] is not None]
    out["materials_subtotal"] = round(sum(known), 2)
    out["unpriced_items"] = [r["item"] for r in out["price_sheet"] if r["total"] is None]
    tax = d.get("sales_tax_pct")
    if tax is not None:
        out["sales_tax"] = round(out["materials_subtotal"] * tax / 100, 2)
        out["total"] = round(out["materials_subtotal"] + out["sales_tax"], 2)
    out["ok"] = not out["errors"] and all(c["ok"] for c in out["checks"])
    json.dump(out, sys.stdout, indent=1)


if __name__ == "__main__":
    main(sys.argv[1])
```