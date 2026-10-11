# Changelog — v1.0.1 (The Witcher 3 patch 5.01)

## Fixes since v1.0.0

- Updated `game/gui/menus/mapMenu.ws` for the new `OnUserMapPinSet` and `ToggleUserMapPin` `insideBounds` argument; fixes the fatal script compilation error after patch 5.01.
- Forward-ported the updated reward-chest handling in `game/quests/quest_function.ws`: `directlyGranted`, versioned facts, `forceAdd`, and invalid item-ID handling.
- Added a one-time versioned recovery of a missing `nr_chameleon_potion`; the recovery is silent (Silent RC3 behavior).
- Updated the release hashes and documentation.

## Preserved behavior

- CPC 3.1.2 remains required; the original CPC files are not replaced.
- Ciri retains the original limited menu access.
- Sorceress out-of-combat teleport requires the original CPC `NRDodge` binding in `input.settings`.
- All 45 cloth resources, CC3W v11 collision cache, DLC, and previous v1.0.0 fixes are unchanged.

**Important:** Remove/disable `modSharedImports`. Outfits 35–40 may remain unavailable (pre-existing limitation).

---

# Changelog — v1.0.0

## First public Remaster compatibility release

- Moved all Remaster-specific changes into a separate compatibility mod/DLC.
- Restored the original CPC base mod and base DLC byte-for-byte before final testing.
- Rebased **28 meaningful WitcherScript changes** onto the Remaster environment.
- Restored CPC resource aliases and gameplay/resource definition registration.
- Restored player switching and CPC female appearance/preset resources.
- Preserved the working 18-language localization layer; EN/RU were explicitly validated in the Remaster string format.
- Migrated all **45 CPC cloth resources** to modern runtime-compatible resources.
- Rebuilt a validated **CC3W v11** collision cache.
- Fixed the inventory 3D character preview for Geralt and custom CPC characters.
- Fixed the false “Female Speech Pack is missing” warning while female voices continue to work.
- Fixed the `questItemQuantity` merge defect that had commented out the vanilla `ProcessCompare(...)` statement.
- Removed development-only diagnostic probes, backup files and experimental asset layers from the final architecture.
- Final RC2.2 testing completed without observed regressions in the tested setup.

### Compatibility note
`modSharedImports` / Community Patch - Shared Imports must be disabled/removed for this release.

### Known issue
Outfits 35–40 may remain non-functional; this behavior predates this Remaster compatibility patch in the baseline used for development.
