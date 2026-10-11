# CPC Remaster Compatibility Fix v1.0.1 — The Witcher 3 patch 5.01

Unofficial compatibility patch for Custom Player Characters 3.1.2, tested on The Witcher 3 version 5.01.

### Fixed
- Updated map pin API signatures (`insideBounds`), resolving the 5.01 script compilation failure.
- Integrated the current quest reward/chest handling.
- Silently restored the missing Chameleon Potion without recurring HUD notifications.

### Preserved
- Character switching, inventory previews, female voice support, remastered cloth resources and collision cache.
- Original Ciri restricted menu behavior.
- Sorceress teleport, with the original CPC `NRDodge` binding configured in `input.settings`.

### Installation
Requires original CPC 3.1.2. Disable/remove `modSharedImports`. Extract the release ZIP into the Witcher 3 installation directory so that both `mods/mod0000_CPC_RemasterFix` and `dlc/dlc_cpc_remaster_fix` are present. Remove previous compatibility overlay folders first; do not delete the original CPC.

### Known issue
Outfits 35–40 may remain non-functional as in the prior Next-Gen baseline.

### Verified archive
`CPC_Remaster_Compatibility_Fix_v1.0.1_5.01.zip`  
SHA256: `51E3FC0B23BA3560380A0D9962ADC176547114979B46806403E5FC8A5E08EC6E`

Original CPC by nikich340. Unofficial compatibility project distributed with the original author's permission; no Donation Points.
