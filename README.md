[![AI Assisted](https://img.shields.io/badge/AI-Claude%20Code-AAAAAA.svg?style=for-the-badge)](https://claude.ai/code)

# Hot Spring Heat Exchanger

A RimWorld 1.6 mod that adds a heat exchanger directly in a hot spring. It feeds the
Dubs Bad Hygiene hot water network with no fuel and no power, so showers, baths and radiators can
run off geothermal spring heat.

<img width="500" height="500" alt="hotspring" src="https://github.com/user-attachments/assets/9f430ffc-7d6d-4ae8-a9d8-eb17cb326470" />

## Requirements

- [Dubs Bad Hygiene](https://steamcommunity.com/sharedfiles/filedetails/?id=836308268)
- RimWorld: Odyssey

Load this mod after Odyssey and DBH.

## Installation

Copy the mod folder into your RimWorld `Mods` directory, then enable it in the mod list below Dubs Bad Hygiene and Odyssey.

- Windows: `steamapps\common\RimWorld\Mods\`
- macOS: `steamapps/common/RimWorld/RimWorldMac.app/Mods/`
- Linux: `steamapps/common/RimWorld/Mods/`

## The building

| | |
|---|---|
| Research | Geothermal Heating |
| Cost | 25 steel |
| Work to build | 150 |
| Placement | Hot spring water only |
| Output | 250 units |
| Fuel / power | None |

Output is deliberately modest, matching the electric boiler's base capacity. 

## How it works

The mod is pure XML. There is no C# and no assembly.

The building inherits from Dubs Bad Hygiene's `BasedHygieneMom` and is modelled on that mod's
`GeothermHeater`, with one substitution: it uses the base `DubsBadHygiene.CompBoiler` instead of
`CompGeoBoiler`. 

## AI

Yes, Claude Code was used to build this.  I have 3K hours in-game and spent quite a bit of time getting this to work properly.

## License

MIT. See [LICENSE](LICENSE).
