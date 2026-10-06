# Custom Player Characters — Remaster Compatibility Fix
## Final technical handoff for the original CPC developer

### Release status

The compatibility work reached a clean release architecture and the final **RC2.2** was functionally tested without observed regressions in the maintainer's environment.

The final architecture keeps the upstream CPC package intact:

```text
Original CPC 3.1.2
  mods\modCustomPlayerCharacters
  dlc\dlcCustomPlayerCharacters

Remaster compatibility layer
  mods\mod0000_CPC_RemasterFix
  dlc\dlc_cpc_remaster_fix
```

The release is therefore suitable both as a standalone compatibility patch and as a reference implementation for a future upstream CPC update.

---

## 1. Baseline validation / clean-room consolidation

Before the final release, three independent states were compared:

1. standalone original CPC 3.1.2 extraction;
2. the archived pre-port 4.04 mod/DLC installation;
3. the known-working Remaster development installation.

The two independent original/4.04 sources matched exactly for the primary CPC roots:

```text
mods\modCustomPlayerCharacters       81 / 81 files identical
dlc\dlcCustomPlayerCharacters       31 / 31 files identical
mods\mod_sharedutils_oneliners      12 / 12 files identical
```

The external CPC-related components in the old 4.04 stack were also verified against the current tested installation and left untouched.

The current development CPC roots were then backed up, the original CPC roots were restored byte-for-byte, and the Remaster changes were reinstalled only through the separate compatibility layer.

This prevents accidental upstream drift from being shipped as part of the compatibility patch.

---

## 2. Final tested CPC-related environment

### Mods present

```text
modCustomPlayerCharacters
mod_sharedutils_oneliners
moddhbcreaturs
modiwptextures
mod0000_CPC_RemasterFix
```

### DLC present

```text
dlcCustomPlayerCharacters
dlcCustomPlayerCharacters_FemaleVoice
dlcCustomPlayerCharacters_FemaleVoiceMods
dlcFemaleArmorModels
dlcdhbcreaturs
dlc_cpc_cosmetic_pack
dlc_cpc_remaster_fix
```

### Explicitly absent / disabled

```text
modSharedImports
```

Important upstream point: the original CPC page historically requires Community Patch - Shared Imports, but the final Remaster compatibility configuration was validated with `modSharedImports` absent. The release should therefore explicitly require users to disable/remove it when using this compatibility layer.

The exact root cause of every possible interaction with Shared Imports was not re-investigated for the release; the supported configuration is defined by the validated state: **Shared Imports disabled**.

---

## 3. Script compatibility layer

The final compatibility mod contains **28 meaningful Remaster-specific WitcherScript overrides**.

### Rebased vanilla/game scripts

```text
scripts\game\gameplay\effects\effects\regen\regenEffect.ws
scripts\game\gui\menus\mapMenu.ws
scripts\game\gui\r4guiSceneController.ws
scripts\game\quests\conditions\questItemQuantity.ws
scripts\game\quests\quest_function.ws
scripts\game\scenes\scene_functions.ws
```

### CPC / NewReplacers scripts

```text
scripts\local\newreplacers\NR_MagicManager.ws
scripts\local\newreplacers\NR_PlayerManager.ws
scripts\local\newreplacers\NR_QuestApprenticeProgress.ws
scripts\local\newreplacers\NR_QuestFunctions.ws
scripts\local\newreplacers\NR_ReplacerSorceress.ws

scripts\local\newreplacers\magic_actions\NR_MagicAction.ws
scripts\local\newreplacers\magic_actions\NR_MagicBomb.ws
scripts\local\newreplacers\magic_actions\NR_MagicCounterPush.ws
scripts\local\newreplacers\magic_actions\NR_MagicFastTravelTeleport.ws
scripts\local\newreplacers\magic_actions\NR_MagicLightning.ws
scripts\local\newreplacers\magic_actions\NR_MagicProjectileWithPrepare.ws
scripts\local\newreplacers\magic_actions\NR_MagicRock.ws
scripts\local\newreplacers\magic_actions\NR_MagicSlash.ws
scripts\local\newreplacers\magic_actions\NR_MagicSpecialAction.ws
scripts\local\newreplacers\magic_actions\NR_MagicSpecialLightningFall.ws
scripts\local\newreplacers\magic_actions\NR_MagicSpecialMeteor.ws
scripts\local\newreplacers\magic_actions\NR_MagicSpecialMeteorFall.ws
scripts\local\newreplacers\magic_actions\NR_MagicSpecialPolymorphism.ws
scripts\local\newreplacers\magic_actions\NR_MagicSpecialServant.ws
scripts\local\newreplacers\magic_actions\NR_MagicSpecialTornado.ws
scripts\local\newreplacers\magic_actions\NR_MagicTeleport.ws

scripts\local\newreplacers\magic_entities\NR_FastTravelTeleport.ws
```

Two files that differed only by blank lines were intentionally excluded from the compatibility delta:

```text
NR_FormattedLocChoiceAction.ws
NR_LocalizedPreviewChoiceAction.ws
```

A scan of the archived 4.04 mod stack found no different-content conflicts on the 28 release paths.

---

## 4. Resource aliases and gameplay definitions

### Symptom

The Remaster alias registry itself worked, but CPC aliases were missing:

```text
GetResourceAliases() -> 1901 aliases

Geralt                     present
Ciri                       present
nr_replacer_witcher        missing
nr_replacer_witcheress     missing
nr_replacer_sorceress      missing
```

### Working solution

A dedicated compatibility DLC mounts the CPC definitions in a Remaster-compatible form. The working layer uses resource/gameplay definition mounters and includes the CPC entity/head/item definitions needed by the current engine.

Relevant compatibility content includes:

```text
gameplay.xml
def_item_quest.xml
jo_def_head_items.xml
nr_def_head_items.xml
nr_def_items.xml
_female_variants_extension.xml
```

for the appropriate `items` / `items_plus` trees.

The compatibility DLC also mounts the required CPC entity-template directories and resource aliases.

This restored:

- player aliases;
- Chameleon/player switching;
- female resources;
- head/face definitions;
- preset resource resolution.

### Upstream recommendation

For a native upstream Remaster release, move these definitions into a first-class Remaster-compatible DLC mounter instead of depending on the legacy loading assumptions.

---

## 5. Localization

Legacy CPC localization used the older string serialization.

The validated Russian conversion produced:

```text
source version: 162
output version: 164

strings: 528
TEXT MISMATCHES: 0
BAD TERMINATORS: 0
```

Known runtime checks included CPC menu labels and quick-switch strings.

The final working compatibility layer preserves the full set of **18 `.w3strings` files** from the tested Remaster configuration. EN and RU were explicitly converted/validated during development.

### Upstream recommendation

Rebuild all shipped localization resources in the current format while preserving all IDs and decoded text. Localization IDs should not be reused as binary/content-presence sentinels.

---

## 6. Cloth / APEX migration

This was the largest compatibility issue.

### Legacy resource layout

The old CPC `.redcloth` files are small legacy cooked `CApexClothResource` wrappers. Their actual APEX payload is stored through the old physics cache rather than embedded like current Remaster resources.

All **45 CPC cloth cache records** followed the same layout:

```text
97-byte old cache record wrapper
+
NvParameterized / NVIDIA APEX payload
```

The pure APEX data begins at offset 97 with:

```text
5A 5B 5C 5D
```

### Working migration

For each CPC cloth resource:

```text
legacy CPC redcloth metadata
+
its own original APEX payload
↓
WolvenKit CR2W -> JSON
add apexBinaryAsset
JSON -> CR2W
↓
modern runtime-compatible CR2W v162 redcloth
```

Validation:

```text
rebuilt CR2W -> JSON
metadata excluding apexBinaryAsset == legacy metadata
APEX byte count == original
APEX SHA256 == original
```

The batch covers all **45 CPC cloth resources**.

Three already runtime-proven resources were kept as known-good modern resources; the remaining 42 were rebuilt and round-trip validated.

### outfit_18 finding

One outfit may require multiple cloth resources. `outfit_18` required:

```text
sleeves_long.redcloth
skirt_long.redcloth
skirt_short.redcloth
```

Migrating only two of them left a visible section absent.

### Upstream recommendation

Maintain an explicit appearance/entity → cloth dependency map. Do not infer one cloth resource per outfit.

---

## 7. Modern collision.cache

The working Remaster cache format is:

```text
CC3W v11
```

Reverse engineering established the required hash fields:

```text
record metadata + 0x1C
uint64 LE
= FNV-1a 64 of the COMPRESSED zlib record

header + 0x30
uint64 LE
= FNV-1a 64 of the filename table + metadata table index
```

A first hybrid cache with correct offsets/sizes but stale hashes failed with:

```text
Collision cache index file is corrupted: CRC error
```

After recalculating the per-record and global index hashes, REDkit `optimizecollisioncache` accepted the generated cache.

The release contains a modern cache with all 45 CPC cloth records.

---

## 8. Packing strategy

The successful cloth release path avoids re-cooking already-valid migrated resources:

```text
validated modern CR2W v162 resources
        |
        | direct WCC pack
        v
blob0.bundle
metadata.store
validated CC3W v11 collision.cache
```

Do **not** use REDkit “Install Project” against the original CPC Cosmetic Pack. During development that route could overwrite/create a partial `dlc_cpc_cosmetic_pack`.

The original cosmetic DLC is preserved unchanged; the Remaster layer overrides it separately.

---

## 9. Inventory 3D preview

### Symptom

The inventory interface itself worked, but the central 3D player model was absent for both Geralt and CPC characters.

### Root cause

Two executable lines had been collapsed onto `// ^ NR_MOD` comment lines:

```witcherscript
// ^ NR_MOD                _isEntitySpawning = true;

// ^ NR_MOD                theGame.GetGuiManager().UpdateSceneEntityItems( items, enhancements );
```

Everything after `//` was commented out.

### Fix

Restore the newline:

```witcherscript
// ^ NR_MOD
_isEntitySpawning = true;

// ^ NR_MOD
theGame.GetGuiManager().UpdateSceneEntityItems( items, enhancements );
```

### Runtime result

Confirmed:

- Geralt inventory preview works;
- custom female CPC preview works;
- appearance/equipment are applied.

---

## 10. Female Speech false warning

Legacy CPC used:

```witcherscript
!NR_IsIdStrExists(2115940999)
```

as a Female Speech Pack installation sentinel and warning string `2115940540`.

In Remaster:

```text
female voices work
localization sentinel absent
=> false missing-pack warning
```

### Fix

Remove only this legacy localization-based Female Speech presence test and stop appending warning `2115940540`.

Keep the independent optional-content checks for the other CPC DLCs.

### Runtime result

Confirmed:

- female voices work;
- no false warning on save load;
- no false warning when entering CPC/Chameleon menu.

### Upstream recommendation

Detect optional audio/content by a real installed DLC/resource/audio marker, not by a localization string.

---

## 11. questItemQuantity merge defect

A second collapsed-comment issue was discovered during the final audit:

```witcherscript
// ^ NR_MOD ^            isFulfilled = ProcessCompare( comparator, itemQuantity, count );
```

`ProcessCompare(...)` is normal vanilla executable logic and must not be commented out.

The final release restores:

```witcherscript
// ^ NR_MOD ^
isFulfilled = ProcessCompare( comparator, itemQuantity, count );
```

This finding is important for future rebases: automated `NR_MOD` block transfer must be followed by a scan for executable code accidentally placed after `//`.

---

## 12. Shared Imports requirement for this release

The upstream CPC Nexus page historically lists **Community Patch - Shared Imports** as required.

The final Remaster compatibility release was instead tested with:

```text
modSharedImports = absent
```

and the release documentation explicitly requires it to be disabled/removed.

This should be treated as a release compatibility boundary. If upstream later integrates the patch natively, Shared Imports behavior can be revisited separately.

---

## 13. Final release composition

```text
mods\
  mod0000_CPC_RemasterFix\
    content\
      28 Remaster script overrides
      18 localization files
      packed 45-cloth resource layer
      CC3W v11 collision.cache
      metadata.store

dlc\
  dlc_cpc_remaster_fix\
    content\
      compatibility DLC bundle
      metadata.store
```

Not shipped:

```text
NR_Remaster* diagnostic probes
*.bak*
old rollback trees
REDkit temporary output
modcpc_remaster_fix experimental bundle
mod0000_CPC_AllClothFix as a separate development mod
test-only backup directories
```

---

## 14. Regression test matrix

Final functional testing covered the main release paths:

- game start / script compilation;
- existing save load;
- Geralt → Witcheress → Sorceress → Geralt;
- CPC/Chameleon menu;
- presets / faces / hair;
- Geralt inventory preview;
- female CPC inventory preview;
- female voice playback;
- no false Female Speech warning;
- outfit_05;
- outfit_18;
- representative additional cloth outfits;
- fast travel / normal gameplay smoke test.

The maintainer reported the final RC2.2 configuration as working.

---

## 15. Known pre-existing content issue

Outfits / dresses **35–40** may still fail to display.

They already failed in the pre-port Next-Gen baseline and are therefore treated as a separate upstream/content issue, not a Remaster regression introduced by this compatibility work.

---

## 16. Engineering takeaway

The safe port is not “convert everything” or “resave everything.”

The working strategy is:

```text
preserve original CPC semantics/assets
+
modernize only engine-facing serialization, registration and API deltas
+
keep the Remaster compatibility delta isolated
+
validate against an independently preserved original baseline
```

This gives upstream a clean path either to adopt the patch as-is or integrate the same changes into a future native CPC release.
