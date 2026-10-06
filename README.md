# Custom Player Characters - Remaster Compatibility Fix

> **Unofficial community fix.** This project is not an official CPC update and is maintained separately from the original mod.

[Русская версия README](README_RU.md) · [Changelog](CHANGELOG.md) · [Credits](CREDITS.md) · [Technical handoff](docs/TECHNICAL_UPSTREAM_EN.md)

> **IMPORTANT: disable/remove `modSharedImports` (Community Patch - Shared Imports) before using this patch.**  
> The original CPC page lists Shared Imports as a legacy requirement, but this Remaster compatibility release was built and tested with `modSharedImports` absent. Running both is unsupported.

## About

This is a standalone compatibility layer for **Custom Player Characters (CPC) 3.1.2** on the current **The Witcher 3 Next-Gen / Remaster environment**.

It keeps the original CPC installation intact and moves Remaster-specific changes into a separate mod + DLC overlay.

The patch was created to preserve the original CPC behavior while fixing the engine-facing parts that no longer work correctly in the Remaster environment.

## What it fixes

- CPC resource aliases / resource definitions not registering correctly.
- Player switching between Geralt, Witcheress and Sorceress.
- Female faces, heads and CPC preset resources.
- Legacy CPC localization compatibility.
- Missing/invisible lower-body cloth and broken cloth physics.
- All **45 CPC cloth resources** migrated to the modern runtime format.
- Modern **CC3W v11** collision cache for CPC cloth.
- Missing character model in the inventory 3D preview.
- False “Female Speech Pack is missing” warning while female voices actually work.
- Remaster API/script compatibility changes across the CPC script set.
- A discovered `questItemQuantity` merge defect where the vanilla `ProcessCompare(...)` statement had been accidentally swallowed by an `NR_MOD` comment.

## Requirements

- The original **Custom Player Characters** mod, version **3.1.2** (Nexus Mod ID **5940**).
- The same base game / DLC requirements as the original CPC installation.
- Any optional CPC add-ons you normally use may remain installed unless stated otherwise below.

### Mandatory compatibility requirement

**Disable or remove `modSharedImports` / Community Patch - Shared Imports.**

This is intentionally different from the legacy CPC installation instructions. The Remaster fix has its own compatibility layer and the final validated setup used `modSharedImports: ABSENT_FROM_CURRENT_GAME`.

Do not report a patch issue until Shared Imports has been removed/disabled and the game has been fully restarted.

## Installation

1. Install the original CPC 3.1.2 normally.
2. Make sure `modSharedImports` / Community Patch - Shared Imports is disabled or removed.
3. Extract this compatibility archive into the Witcher 3 game directory.
4. Keep the included folder names unchanged:
   - `mods\mod0000_CPC_RemasterFix`
   - `dlc\dlc_cpc_remaster_fix`
5. Fully restart the game.

### Upgrading from development/test builds

Remove old development-only folders if you previously used them:

- `mods\mod0000_CPC_AllClothFix`
- `mods\modcpc_remaster_fix`
- any older `mods\mod0000_CPC_RemasterFix`
- any older `dlc\dlc_cpc_remaster_fix`

Then install the current release.

## Tested CPC-related setup

### Mods
- `modCustomPlayerCharacters`
- `mod_sharedutils_oneliners`
- `moddhbcreaturs`
- `modiwptextures`
- `mod0000_CPC_RemasterFix`

### DLC
- `dlcCustomPlayerCharacters`
- `dlcCustomPlayerCharacters_FemaleVoice`
- `dlcCustomPlayerCharacters_FemaleVoiceMods`
- `dlcFemaleArmorModels`
- `dlcdhbcreaturs`
- `dlc_cpc_cosmetic_pack`
- `dlc_cpc_remaster_fix`

### Disabled
- **`modSharedImports`**

This list documents the tested environment; optional CPC packs are not automatically required just because they were present during testing.

## Verification checklist

After installing, check:

- load an existing save;
- Geralt → Witcheress → Sorceress → Geralt switching;
- CPC / Chameleon menu;
- presets, faces and hair;
- Geralt inventory 3D preview;
- custom female inventory 3D preview;
- female voices;
- no false Female Speech Pack warning;
- `outfit_05`;
- `outfit_18`;
- several additional cloth outfits while walking/running;
- fast travel.

## Known issue

**Outfits / dresses 35–40 may still fail to display.**

They were already non-functional in the Next-Gen baseline used for the port, so they are treated as a pre-existing CPC/content issue rather than a Remaster regression introduced by this patch.

## Compatibility / other mods

The release contains **28 Remaster-specific WitcherScript overrides**. The archived 4.04 setup used during development had no conflicting versions of these paths among its installed mods.

Other users may still have unrelated mods that edit the same vanilla scripts. If the game reports script conflicts, resolve those conflicts for your own mod list rather than overwriting files blindly.

## Credits and permission

Original **Custom Player Characters** by **nikich340**.

This compatibility work is intended for release with permission from the original CPC author. It is not a standalone redistribution of CPC: the original mod remains required.

All original CPC assets remain credited to their original author(s). Do not enable Donation Points or redistribute original assets outside the permission you have received.

## Version

**1.0.0 — first public Remaster compatibility release**
