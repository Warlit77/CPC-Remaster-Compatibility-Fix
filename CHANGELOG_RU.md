# История изменений — v1.0.1 (The Witcher 3, патч 5.01)

## Исправления после v1.0.0

- `game/gui/menus/mapMenu.ws`: обновлены `OnUserMapPinSet` и `ToggleUserMapPin` с параметром `insideBounds`; устранена блокирующая ошибка компиляции после 5.01.
- `game/quests/quest_function.ws`: перенесена обновлённая логика выдачи наград (`directlyGranted`, версии фактов, `forceAdd`, проверка идентификаторов).
- Добавлено однократное тихое восстановление отсутствующего `nr_chameleon_potion` без навязчивого уведомления.
- Обновлены контрольные суммы и документация.

## Что осталось прежним

- Оригинальный CPC 3.1.2 обязателен и не заменяется этим патчем.
- Ограничения меню Цири сохранены.
- Внебоевой телепорт чародейки требует оригинальной привязки `NRDodge` в `input.settings`.
- 45 ресурсов одежды, CC3W v11 collision cache, DLC и остальные исправления v1.0.0 не изменялись.

**Важно:** отключить/удалить `modSharedImports`. Комплекты одежды 35–40 могут оставаться недоступными (прежнее ограничение).

---

# Changelog — v1.0.0

## Первый публичный Remaster compatibility release

- Все Remaster-specific изменения вынесены в отдельные compatibility mod/DLC.
- Перед финальным тестированием оригинальные CPC mod и DLC восстановлены byte-for-byte.
- На Remaster перенесены **28 содержательных WitcherScript-изменений**.
- Восстановлена регистрация CPC resource aliases и gameplay/resource definitions.
- Восстановлены переключение персонажей и female appearance/preset resources.
- Сохранён рабочий слой локализации из 18 языковых файлов; EN/RU отдельно валидировались в Remaster string format.
- Мигрированы все **45 CPC cloth resources**.
- Пересобран валидный **CC3W v11** collision cache.
- Исправлено inventory 3D preview для Геральта и custom CPC characters.
- Исправлено ложное “Female Speech Pack is missing” при работающих женских голосах.
- Исправлен merge-баг `questItemQuantity`, из-за которого vanilla `ProcessCompare(...)` оказался закомментирован.
- Диагностические probes, backup-файлы и экспериментальные asset layers исключены из финальной архитектуры.
- Финальная RC2.2-сборка протестирована без обнаруженных регрессий в тестовой конфигурации.

### Важное требование совместимости
`modSharedImports` / Community Patch - Shared Imports должен быть отключён/удалён.

### Известная проблема
Outfits 35–40 могут оставаться неработающими; такое поведение существовало ещё в baseline до данного Remaster compatibility patch.
