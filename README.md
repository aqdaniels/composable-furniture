# Composable Furniture — a Claude skill

Design furniture the way an architect designs a building: start from a structure you don't redesign (IKEA frames), add a skin that gives it character (IKEA-compatible fronts, panels, legs and tops from other makers), then fixtures, hardware, finishes, appliances and services from wherever they're sold.

The skill's job isn't suggesting products. It produces a build that **fits together, is still for sale, and matches its render**:

- Eight layers: structure, skin, surface, fixtures, hardware, finish, appliances, services
- Every part is graded on fit: native, certified, adaptable, independent (unverified parts are never used)
- Fit checks: dimension stack-ups, wall tolerance, load, anchoring, appliance ventilation and power
- One `build.json` drives the 3D render, the shopping list, the cut list and the landed cost, so they can't disagree
- A built-in library of known fit rules (IKEA BESTÅ and SEKTION, Semihandmade, Fronteriors, Pretty Pegs, built-in coolers) that grows with every build

![Example build: a 55-inch coffee and wine bar, with cost, vendor count, counter height and a front view of every part](examples/coffee-wine-bar/img/hero.png)

Every build comes with fit checks that show what passes and what needs attention before you buy:

![Compatibility checks for the coffee and wine bar: width, counter height, cooler fit, ventilation, power, shelf load and lead time](examples/coffee-wine-bar/img/checks.png)

## Install

Copy the `composable-furniture` folder into your skills directory:

- **Claude Code:** `~/.claude/skills/composable-furniture/SKILL.md`
- **Claude apps:** upload the folder as a skill in Settings

Then ask for something like *"Design a 48-inch built-in coffee bar with a beverage fridge"* or *"Two-drawer nightstands with brass handles and a hidden phone charger."*

## Example

`examples/coffee-wine-bar/` holds a complete build: a 55" built-in coffee and wine bar (IKEA SEKTION drawers, Semihandmade olive panels, a dual-zone cooler, walnut shelves for two rows of bottles, and wallpaper). Open `index.html` in a browser for the 3D model, bill of materials, cost summary, fit checks, cut list and build sequence.

Prices and availability in the example were checked on 2026-11-12 and will drift; the skill re-checks them live on every build.

## Limits

- Prices and stock change; the skill re-verifies every part each time it runs.
- Amazon listings can't be verified through web search, so the skill uses links you paste or a browser.
- Electrical, plumbing and structural work beyond stud anchoring is flagged for a licensed pro.

## License

MIT. See [LICENSE](LICENSE).
