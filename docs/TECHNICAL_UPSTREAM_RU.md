# Custom Player Characters — Remaster Compatibility Fix
## Финальная техническая документация для автора оригинального CPC

### Статус релиза

Работа по совместимости доведена до чистой релизной архитектуры. Финальная сборка **RC2.2** была функционально протестирована без выявленных регрессий в тестовой среде.

Итоговая архитектура сохраняет upstream CPC нетронутым:

```text
Original CPC 3.1.2
  mods\modCustomPlayerCharacters
  dlc\dlcCustomPlayerCharacters

Remaster compatibility layer
  mods\mod0000_CPC_RemasterFix
  dlc\dlc_cpc_remaster_fix
```

Такой результат подходит как для отдельного compatibility patch, так и в качестве reference implementation для возможного будущего upstream-обновления CPC.

---

## 1. Проверка baseline и финальная clean-room консолидация

Перед релизом были сопоставлены три независимых состояния:

1. отдельно распакованный оригинальный CPC 3.1.2;
2. сохранённая pre-port 4.04 mod/DLC-конфигурация;
3. известная рабочая Remaster development-конфигурация.

Два независимых источника оригинальной 4.04-сборки совпали полностью по основным CPC roots:

```text
mods\modCustomPlayerCharacters       81 / 81 файлов идентичны
dlc\dlcCustomPlayerCharacters       31 / 31 файлов идентичны
mods\mod_sharedutils_oneliners      12 / 12 файлов идентичны
```

CPC-связанные внешние компоненты старой 4.04-сборки также были проверены против текущей тестовой установки и оставлены нетронутыми.

После этого current development roots были полностью забекаплены, оригинальные CPC roots восстановлены byte-for-byte, а Remaster-изменения установлены только через отдельный compatibility layer.

Это исключает попадание случайных development-изменений upstream-файлов в релиз.

---

## 2. Финальная протестированная CPC-среда

### Mods

```text
modCustomPlayerCharacters
mod_sharedutils_oneliners
moddhbcreaturs
modiwptextures
mod0000_CPC_RemasterFix
```

### DLC

```text
dlcCustomPlayerCharacters
dlcCustomPlayerCharacters_FemaleVoice
dlcCustomPlayerCharacters_FemaleVoiceMods
dlcFemaleArmorModels
dlcdhbcreaturs
dlc_cpc_cosmetic_pack
dlc_cpc_remaster_fix
```

### Явно отсутствовал / был отключён

```text
modSharedImports
```

Важный момент для upstream: оригинальная Nexus-страница CPC исторически требует Community Patch - Shared Imports, однако финальная Remaster-конфигурация проверена именно при отсутствии `modSharedImports`.

Поэтому публичный release должен явно требовать отключить/удалить Shared Imports.

Полная причинность всех возможных взаимодействий Shared Imports повторно не исследовалась на финальном этапе; поддерживаемая конфигурация определяется проверенным состоянием: **Shared Imports отключён**.

---

## 3. Script compatibility layer

Финальный compatibility mod содержит **28 содержательных Remaster-specific WitcherScript overrides**.

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

Два файла, отличавшихся от baseline только пустыми строками, намеренно не включены:

```text
NR_FormattedLocChoiceAction.ws
NR_LocalizedPreviewChoiceAction.ws
```

Скан сохранённой 4.04 mod stack не выявил different-content conflicts по этим 28 release paths.

---

## 4. Resource aliases и gameplay definitions

### Симптом

Alias registry Remaster работал, но CPC aliases не регистрировались:

```text
GetResourceAliases() -> 1901 aliases

Geralt                     присутствует
Ciri                       присутствует
nr_replacer_witcher        отсутствует
nr_replacer_witcheress     отсутствует
nr_replacer_sorceress      отсутствует
```

### Рабочее решение

Добавлен отдельный compatibility DLC, который монтирует CPC definitions в Remaster-compatible форме.

В рабочий слой входят необходимые resource/gameplay definitions, включая:

```text
gameplay.xml
def_item_quest.xml
jo_def_head_items.xml
nr_def_head_items.xml
nr_def_items.xml
_female_variants_extension.xml
```

для соответствующих `items` / `items_plus`.

Compatibility DLC также монтирует необходимые CPC entity-template directories и resource aliases.

Это восстановило:

- player aliases;
- Chameleon/player switching;
- female resources;
- head/face definitions;
- preset resource resolution.

### Рекомендация upstream

Для native Remaster-версии definitions лучше оформить как штатный Remaster-compatible DLC mounter, а не сохранять legacy-допущения загрузчика.

---

## 5. Локализация

Legacy CPC localization использовала старую сериализацию strings.

Проверенная конвертация RU дала:

```text
source version: 162
output version: 164

strings: 528
TEXT MISMATCHES: 0
BAD TERMINATORS: 0
```

Проверялись CPC menu labels и quick-switch strings.

Финальный compatibility layer сохраняет полный набор из **18 `.w3strings`** рабочей Remaster-конфигурации. EN и RU отдельно конвертировались/валидировались во время разработки.

### Рекомендация upstream

Пересобрать localization resources в актуальном формате, сохраняя IDs и декодированный текст. Localization IDs не должны использоваться как признак наличия binary/optional content.

---

## 6. Cloth / APEX migration

Это была крупнейшая проблема совместимости.

### Legacy layout

Старые CPC `.redcloth` — небольшие legacy cooked `CApexClothResource` wrappers. Реальный APEX payload хранится через старый physics cache, а не так, как в современных Remaster resources.

Все **45 CPC cloth cache records** имели структуру:

```text
97-byte old cache record wrapper
+
NvParameterized / NVIDIA APEX payload
```

Чистый APEX начинается на offset 97 с:

```text
5A 5B 5C 5D
```

### Рабочая миграция

Для каждого CPC cloth resource:

```text
legacy CPC redcloth metadata
+
его собственный original APEX payload
↓
WolvenKit CR2W -> JSON
добавление apexBinaryAsset
JSON -> CR2W
↓
modern runtime-compatible CR2W v162 redcloth
```

Валидация:

```text
rebuilt CR2W -> JSON
metadata без apexBinaryAsset == legacy metadata
APEX byte count == original
APEX SHA256 == original
```

Batch покрывает все **45 CPC cloth resources**.

Три уже runtime-proven ресурса были сохранены как known-good modern resources, остальные 42 автоматически rebuilt + round-trip validated.

### Вывод по outfit_18

Один outfit может использовать несколько независимых cloth resources. Для `outfit_18` потребовались:

```text
sleeves_long.redcloth
skirt_long.redcloth
skirt_short.redcloth
```

Миграция только двух первых оставляла часть платья отсутствующей.

### Рекомендация upstream

Нужен явный mapping appearance/entity → все используемые cloth resources; нельзя предполагать «один outfit = один cloth».

---

## 7. Современный collision.cache

Рабочий Remaster format:

```text
CC3W v11
```

Reverse engineering показал:

```text
record metadata + 0x1C
uint64 LE
= FNV-1a 64 от СЖАТОЙ zlib record

header + 0x30
uint64 LE
= FNV-1a 64 от filename table + metadata table index
```

Первая hybrid-сборка с правильными offsets/sizes, но старыми hashes падала с:

```text
Collision cache index file is corrupted: CRC error
```

После пересчёта per-record и global-index hashes REDkit `optimizecollisioncache` принял cache.

В релизе используется modern cache со всеми 45 CPC cloth records.

---

## 8. Стратегия упаковки

Успешный release flow не recook'ит уже валидные migrated resources:

```text
validated modern CR2W v162 resources
        |
        | direct WCC pack
        v
blob0.bundle
metadata.store
validated CC3W v11 collision.cache
```

Не использовать REDkit `Install Project` против original CPC Cosmetic Pack. Во время разработки этот путь мог создавать/перезаписывать частичный `dlc_cpc_cosmetic_pack`.

Original cosmetic DLC сохраняется неизменным, compatibility layer работает отдельно.

---

## 9. Inventory 3D preview

### Симптом

Inventory UI работал, но central 3D player отсутствовал и у Геральта, и у CPC character.

### Причина

Две executable lines оказались склеены с `// ^ NR_MOD`:

```witcherscript
// ^ NR_MOD                _isEntitySpawning = true;

// ^ NR_MOD                theGame.GetGuiManager().UpdateSceneEntityItems( items, enhancements );
```

Всё после `//` стало comment.

### Исправление

```witcherscript
// ^ NR_MOD
_isEntitySpawning = true;

// ^ NR_MOD
theGame.GetGuiManager().UpdateSceneEntityItems( items, enhancements );
```

### Runtime result

Подтверждено:

- inventory preview Геральта;
- preview custom female CPC;
- корректное применение appearance/equipment.

---

## 10. Ложный Female Speech warning

Legacy CPC использовал:

```witcherscript
!NR_IsIdStrExists(2115940999)
```

как sentinel установки Female Speech Pack и warning `2115940540`.

В Remaster:

```text
female voices работают
localization sentinel отсутствует
=> false missing-pack warning
```

### Исправление

Удалена только localization-based проверка Female Speech presence и добавление warning `2115940540`.

Независимые проверки остальных optional CPC DLC сохранены.

### Runtime result

Подтверждено:

- female voices работают;
- ложного warning нет при load save;
- ложного warning нет при входе в CPC/Chameleon menu.

### Рекомендация upstream

Optional audio/content нужно определять по реальному DLC/resource/audio marker, а не localization string.

---

## 11. questItemQuantity merge defect

Во время финального аудита найден второй collapsed-comment bug:

```witcherscript
// ^ NR_MOD ^            isFulfilled = ProcessCompare( comparator, itemQuantity, count );
```

`ProcessCompare(...)` — штатная executable vanilla logic и не должна быть comment.

В release восстановлено:

```witcherscript
// ^ NR_MOD ^
isFulfilled = ProcessCompare( comparator, itemQuantity, count );
```

Вывод для будущих rebases: после automated/manual transfer `NR_MOD` blocks нужен отдельный scan на executable code после `//`.

---

## 12. Shared Imports в данном release

Upstream CPC Nexus page исторически требует **Community Patch - Shared Imports**.

Финальная Remaster-конфигурация протестирована с:

```text
modSharedImports = absent
```

и публичная документация требует его отключить/удалить.

Это следует считать compatibility boundary данного release. Если upstream интегрирует изменения нативно, взаимодействие с Shared Imports можно исследовать отдельно.

---

## 13. Финальный состав релиза

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

Не публикуются:

```text
NR_Remaster* diagnostic probes
*.bak*
old rollback trees
REDkit temporary output
modcpc_remaster_fix experimental bundle
отдельный development mod0000_CPC_AllClothFix
test-only backup directories
```

---

## 14. Regression test matrix

Финальная functional testing включала:

- старт игры / script compilation;
- load existing save;
- Geralt → Witcheress → Sorceress → Geralt;
- CPC/Chameleon menu;
- presets / faces / hair;
- inventory preview Геральта;
- inventory preview female CPC;
- female voice playback;
- отсутствие false Female Speech warning;
- outfit_05;
- outfit_18;
- несколько других cloth outfits;
- fast travel / normal gameplay smoke test.

Пользователь сообщил, что финальная RC2.2-конфигурация работает нормально.

---

## 15. Известная pre-existing content-проблема

Outfits / dresses **35–40** могут по-прежнему не отображаться.

Такое поведение существовало ещё в pre-port Next-Gen baseline, поэтому это отдельная upstream/content-проблема, а не новая Remaster regression.

---

## 16. Основной инженерный вывод

Безопасный порт — это не «сконвертировать всё» и не «resave всё через REDkit».

Рабочая стратегия:

```text
сохранить оригинальные CPC semantics/assets
+
модернизировать только engine-facing serialization, registration и API deltas
+
изолировать Remaster compatibility delta
+
валидировать относительно независимо сохранённого original baseline
```

Это даёт upstream чистый путь: либо использовать patch как отдельный слой, либо интегрировать те же изменения в будущий native CPC release.
