# Custom Player Characters - Remaster Compatibility Fix

> **Неофициальный community fix.** Это не официальное обновление CPC и проект поддерживается отдельно от оригинального мода.

[English README](README.md) · [Changelog](CHANGELOG_RU.md) · [Credits](CREDITS.md) · [Техническая документация](docs/TECHNICAL_UPSTREAM_RU.md)

> **ВАЖНО: перед использованием патча отключите/удалите `modSharedImports` (Community Patch - Shared Imports).**  
> В оригинальной инструкции CPC Shared Imports указан как legacy-зависимость, однако данный Remaster compatibility release собран и протестирован при полном отсутствии `modSharedImports`. Одновременная работа обоих слоёв не поддерживается.

## О моде

Это отдельный compatibility layer для **Custom Player Characters (CPC) 3.1.2** в актуальном **The Witcher 3 Next-Gen / Remaster окружении**.

Оригинальный CPC остаётся нетронутым, а все Remaster-specific изменения вынесены в отдельные mod + DLC override-слои.

Цель патча — сохранить исходное поведение CPC, исправив только те engine-facing части, которые перестали корректно работать в Remaster.

## Что исправлено

- Регистрация CPC resource aliases / resource definitions.
- Переключение Geralt / Witcheress / Sorceress.
- Женские лица, головы и CPC presets.
- Совместимость legacy-локализации CPC.
- Невидимая/сломанная нижняя часть одежды и cloth physics.
- Миграция всех **45 CPC cloth resources**.
- Современный **CC3W v11** collision cache.
- Отсутствующая 3D-модель персонажа в inventory preview.
- Ложное предупреждение “Female Speech Pack is missing” при реально работающих женских голосах.
- Remaster API/script compatibility по набору CPC-скриптов.
- Найденный merge-баг в `questItemQuantity`, где vanilla-вызов `ProcessCompare(...)` был случайно закомментирован строкой `NR_MOD`.

## Требования

- Оригинальный **Custom Player Characters**, версия **3.1.2** (Nexus Mod ID **5940**).
- Те же требования к игре/DLC, что и у оригинального CPC.
- Используемые вами optional CPC-addons можно оставить, если ниже прямо не указано иное.

### Обязательное требование совместимости

**Отключить или удалить `modSharedImports` / Community Patch - Shared Imports.**

Это намеренно отличается от старой инструкции оригинального CPC. У Remaster fix собственный compatibility layer, а финальная рабочая конфигурация была протестирована со статусом `modSharedImports: ABSENT_FROM_CURRENT_GAME`.

Перед сообщением об ошибке обязательно отключите Shared Imports и полностью перезапустите игру.

## Установка

1. Установите оригинальный CPC 3.1.2.
2. Убедитесь, что `modSharedImports` / Community Patch - Shared Imports отключён или удалён.
3. Распакуйте архив compatibility fix в корень игры.
4. Не переименовывайте папки:
   - `mods\mod0000_CPC_RemasterFix`
   - `dlc\dlc_cpc_remaster_fix`
5. Полностью перезапустите игру.

## Протестированная CPC-конфигурация

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

### Отключён
- **`modSharedImports`**

Это список среды, в которой проводилось тестирование. Наличие optional CPC-пакета в списке не означает автоматически, что он обязателен для всех пользователей.

## Известная проблема

**Outfits / dresses 35–40 могут по-прежнему не отображаться.**

Они уже не работали в исходном Next-Gen baseline, использованном при портировании. Поэтому это рассматривается как отдельная upstream/content-проблема CPC, а не новая Remaster-регрессия патча.

## Совместимость с другими модами

Релиз содержит **28 Remaster-specific WitcherScript overrides**. В сохранённой 4.04-сборке, использованной для разработки, конфликтующих версий этих script paths среди установленных модов не обнаружено.

## Credits / permission

Оригинальный **Custom Player Characters** — **nikich340**.

Данный compatibility patch предназначен для публикации с разрешения автора оригинального CPC. Он не является самостоятельной копией CPC: оригинальный мод остаётся обязательным.

## Версия

**1.0.0 — первый публичный Remaster compatibility release**
