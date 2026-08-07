[![AI Assisted](https://img.shields.io/badge/AI-Claude%20Code-AAAAAA.svg?style=for-the-badge)](https://claude.ai/code)

# Hot Spring Heat Exchanger

A RimWorld 1.6 mod that adds a heat exchanger buildable directly in a hot spring. It feeds the
Dubs Bad Hygiene hot water network with no fuel and no power, so showers, baths and radiators can
run off geothermal spring heat.

## Requirements

- [Dubs Bad Hygiene](https://steamcommunity.com/sharedfiles/filedetails/?id=836308268)
- RimWorld: Odyssey (the hot spring terrain comes from this DLC)
- RimWorld 1.6

Load this mod after both.

## Installation

Copy the mod folder into your RimWorld `Mods` directory, then enable it in the mod list below
Dubs Bad Hygiene and Odyssey.

- Windows: `steamapps\common\RimWorld\Mods\`
- macOS: `steamapps/common/RimWorld/RimWorldMac.app/Mods/`
- Linux: `steamapps/common/RimWorld/Mods/`

`heat_exchanger3.png` at the repository root is the source art and is not needed by the game.

## The building

| | |
|---|---|
| Research | Geothermal Heating |
| Cost | 25 steel |
| Work to build | 150 |
| Placement | Hot spring water only |
| Output | 250 units to the Sewage pipe network |
| Fuel / power | None |

Output is deliberately modest, matching the electric boiler's base capacity. It runs on free
ambient spring heat, so a low ceiling is what keeps it from obsoleting the boilers that cost fuel
or power. A large bathhouse wants several of these strung along the spring rather than one unit.

## How it works

The mod is pure XML. There is no C# and no assembly.

Placement is handled by a custom terrain affordance rather than a PlaceWorker:

1. `Defs/TerrainAffordanceDefs/TerrainAffordances_HotSpring.xml` declares the `HotSpringTappable`
   affordance.
2. `Patches/Patch_HotSpringAffordance.xml` grants it to Odyssey's `HotSpring` terrain and nothing
   else.
3. `Defs/ThingDefs_Buildings/Buildings_HotSpringHeatExchanger.xml` requires it via
   `terrainAffordanceNeeded`.

This also removes the need for a bridge. Water blocks construction only because it lacks the
`Light` affordance that buildings normally ask for, so a building that requires a different
affordance sidesteps that check.

The building inherits from Dubs Bad Hygiene's `BasedHygieneMom` and is modelled on that mod's
`GeothermHeater`, with one substitution: it uses the base `DubsBadHygiene.CompBoiler` instead of
`CompGeoBoiler`. `CompGeoBoiler` resolves an actual steam geyser at runtime and would find nothing
under a hot spring. The base comp has no fuel or power throttle, which is why this building simply
always runs.

The terrain patch uses `PatchOperationAdd` rather than `Replace`, so the added affordance merges
with the ones `HotSpring` inherits from `WaterShallowBase` and nothing another mod has done to that
terrain gets clobbered.

## License

MIT. See [LICENSE](LICENSE).
