# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A pure-XML RimWorld 1.6 mod (no C#, no assemblies, no build step). It adds one building,
`HotSpringHeatExchanger`, that can be placed on Odyssey hot spring water and feeds the Dubs Bad
Hygiene (DBH) hot water network with no fuel and no power.

Hard dependencies, both of which must load first: Dubs Bad Hygiene and RimWorld: Odyssey.

## Deploy and test

There is nothing to compile. The mod runs by being copied into the game's Mods folder:

```sh
rsync -a --delete \
  --exclude 'heat_exchanger3.png' --exclude 'CLAUDE.md' \
  "/Users/dean/Desktop/Projects/Rimworld_Mods/HotSpringHeatExchanger/" \
  "$HOME/Library/Application Support/Steam/steamapps/common/RimWorld/RimWorldMac.app/Mods/HotSpringHeatExchanger/"
```

`heat_exchanger3.png` is the 1024x1024 source art at the project root and is not shipped; the
in-game texture is the 128x128 export at `Textures/sxdjt/HotSpringHeatExchanger.png`.

Verification is manual, in game: enable the mod after DBH and Odyssey, then check the dev-mode log
for def resolution errors on startup and try placing the building on and off hot spring water.
Every change here is a def or patch change, so a bad edit surfaces as a red error at load time
rather than a crash.

## Reference sources on this machine

Read these before changing anything structural. They are the authority for parent def names, comp
class names and field names, all of which are silently version-sensitive.

- DBH abstract parent `BasedHygieneMom`:
  `~/Library/Application Support/Steam/steamapps/workshop/content/294100/836308268/1.6/Defs/ThingDefs_Buildings/BuildingsB_Hygiene.xml`
- DBH `GeothermHeater`, the building this one is modelled on:
  `.../836308268/1.6/Defs/ThingDefs_Buildings/BuildingsE_Heating.xml`
- Odyssey `HotSpring` terrain, the patch target:
  `~/Library/Application Support/Steam/steamapps/common/RimWorld/RimWorldMac.app/Data/Odyssey/Defs/TerrainDefs/Terrain_Water.xml`

## Architecture

Three files carry the whole mod, and the design hinges on how they interact.

1. `Defs/TerrainAffordanceDefs/TerrainAffordances_HotSpring.xml` declares a custom affordance,
   `HotSpringTappable`.
2. `Patches/Patch_HotSpringAffordance.xml` grants that affordance to Odyssey's `HotSpring` terrain
   and nothing else.
3. `Defs/ThingDefs_Buildings/Buildings_HotSpringHeatExchanger.xml` sets
   `terrainAffordanceNeeded` to `HotSpringTappable`.

That triangle is the entire placement rule. It replaces what would otherwise need a C# PlaceWorker,
and it also removes the need for a bridge: water blocks construction only because it lacks the
`Light` affordance that buildings normally require, so a building that asks for a different
affordance sidesteps that check. Changing any one of the three names breaks placement silently, with
the building becoming unplaceable everywhere rather than erroring.

Two consequences worth knowing before editing:

- The patch uses `PatchOperationAdd` on a terrain that has no `<affordances>` node of its own and
  inherits one from `WaterShallowBase`. Patches run against raw XML before def inheritance resolves,
  so the added one-entry list merges with the inherited affordances instead of replacing them. Do not
  switch this to `PatchOperationReplace`.
- The building uses the base `DubsBadHygiene.CompBoiler` (no `compClass` override), not
  `CompGeoBoiler`. `CompGeoBoiler` resolves a real `Building_SteamGeyser` at runtime and would find
  nothing under a hot spring. The base comp has no fuel or power throttle, which is why this building
  simply always runs.

Balance intent, in case a change looks arbitrary: `BaseCapacity` is deliberately held at 250 (the
electric boiler's base output) and build cost at 25 steel, so the answer to a large bathhouse is
several units strung along the spring, not one unit that obsoletes the boilers that cost fuel or
power.

## Conventions

- Texture paths live in one global namespace shared by every loaded mod, so all texture folders are
  prefixed `sxdjt/`. An unprefixed path can be silently overwritten by whichever mod loads last.
- The existing XML is heavily commented and the comments explain *why* a choice was made (why base
  `CompBoiler`, why `PatchOperationAdd`, why not rotatable). Match that density; these notes are the
  only documentation the mod has.
- The building is `rotatable=false` because `Graphic_Single` has no per-direction variants. Making it
  rotatable means adding `_north`/`_east`/`_south` textures and switching to `Graphic_Multi`.

## Repo state

This directory is not a git repository and has no README or CHANGELOG. If a release is requested,
confirm what the target is (Steam Workshop upload, GitHub repo, plain zip) rather than assuming.
