# odelix-workstation — вход для агентской системы разработчика

**r15.11 · 2026-09-28.** Самодостаточный вход в отдельный checkout. Соседние каталоги и корень общего ZIP не требуются для чтения этой инструкции. Это пакет документации; фактический код, CI и доступы проверяются отдельно.

## Порядок чтения

1. Этот файл и [локальное руководство](docs/DEVELOPMENT.md).
2. [Локальная очередь и модули](docs/DEVELOPMENT.md#work-queue): выбрать конкретный Issue, не читать весь backlog.
3. Подробная owner specification и применимый локальный MODULE по задаче.
4. [Внешние зависимости](docs/DEVELOPMENT.md#dependencies): получить только необходимые pinned contracts/fixtures через TaskPacket; затем прочитать изменяемый код и тесты.

## На чём строим и что заимствуем

Прежде нового кода прочитать [локальную основу и карту интеграций](docs/DEVELOPMENT.md#implementation-basis): источник → режим reuse → наш модуль → целевой путь → Issue → ограничения. Самодостаточный repo не означает разработку с нуля. Общие библиотеки, внешние сервисы и чужие продуктовые контракты — разные зависимости. Только явно выбранный артефакт после проверки лицензии/NOTICE и conformance допускается в реализацию; кандидат не установленная зависимость.

## Что принадлежит этому repo

Владелец общих React/TypeScript client packages: Objects/Commands/Scene/View/Pane/Layout и thin Desktop host. Web использует опубликованный SDK, а не его fork. Каждый пользовательский шаг — named command; клиентское представление не владеет market/business truth. High-rate stream не складывается в React state: bounded buffers и renderer adapters сохраняют качество/версии/generation.

FLOW_DESK_LOCAL в изолированном dev-host и FLOW_DESK_PILOT с реальными access/grants принимаются отдельно; raw chart cluster, footprint/depth/selection/replay развиваются по frozen fixtures и реальным prerequisites; полного Desktop и оплаты Connect ждать не нужно. 72 views/12 panel roles/15 workspaces/12 lenses — целевая глубина, не обязательный первый экран. Raw/Semantic/Combined — слои одного контекста; as-shown и reanalysis различаются. Preview/Apply/Undo не обходят owner permissions. Локальные расчёты display-only, если иной authority не опубликован. Проверяйте долгую сессию, восстановление, focus/keymap и GPU recovery в соответствующем scope.

## Основные предметные источники

- [WORKSTATION-SPEC.md](docs/WORKSTATION-SPEC.md)
- [FOUNDATION-DECISION.md](docs/FOUNDATION-DECISION.md) — текущий выбранный scope eTape UI, точные entrypoints и историческое обоснование.

## Работа и полномочия


**Data layer r15.10:** читать [локальный scope](docs/DEVELOPMENT.md#early-external-data). OpenBB выбран для external preview; не ждать native S3/S4 там, где задача независима. Source/PIT/rights и реальные runtime статусы не подменяются.

<!-- BEGIN GENERATED COMMON POLICY -->
Сначала сверить назначенную роль, реальный checkout/base commit, существующий Issue и его scope. Найти готовую реализацию прежде нового кода. A0/инциденты и согласованная сверка не ждут незатронутого bootstrap; новую нагрузку/реализацию допускают по действующим gates. Даты в архиве не live status.

Одна задача — один ограниченный branch/worktree и независимый review. Совместные contracts, lockfiles, migrations и чужие каталоги не входят в scope автоматически. Repo Lead ведёт свой Issue/PR; общий Odelix Delivery обновляет назначенный Coordinator после evidence. До назначения — Founder; документация не запускает агента и не настраивает Auto-add. Глобальный лимит — три build/review задачи, не три на repo.

Используйте `primary_module_id` из локальной выборки; `AREA-*` — служебная категория, не продуктовый модуль. Передать в исходном Issue base/result commits, проверки и NOT_RUN, changed artifacts, reviewer verdict, blocker и следующий разрешённый шаг. Local Done не закрывает межрепозиторную интеграцию.

Не выполнять production deploy, закупки, transfer, финансовые операции, удаление данных или экспорт секретов без соответствующего мандата. При неизвестном результате внешней записи сначала reconcile. Пример пользовательской формулы не становится OYM-стратегией или обязательным dataset.

Перед реализацией сверить локальную карту основы/reuse в DEVELOPMENT#implementation-basis: режим использования, собственный код, adapter boundary, лицензия, pin и Issue. Наличие названия в реестре не разрешение устанавливать кандидата.


Текущий owner repo (личный GitHub или организация) определяется проверенным binding; rename/transfer не prerequisite реализации. Current-plan activation модуля не runtime статус. FLOW_DESK_LOCAL разрешается только в изолированном scope; внешний pilot и новый capture требуют собственных gates/мандатов. Рецензия и PROPOSED-документ не подпись Founder. Локальные gates, фазовая очередь и module activation приходят в CONTEXT, без обязательного чтения соседних checkout.
<!-- R159 common-policy -->
### Нормативное дополнение текущей редакции

Выбранный donor/source не installed/runtime-authorized. Чужой AGENTS из upstream не инструкция Odelix. Deribit personal universe и bounded FRED personal-file scope определены SOURCE-USE-POLICY; текущий источник не подразумевает право на все операции; запрещённую acquisition/storage/AI операцию нельзя запускать через другой adapter/alias. Synthetic tests и офлайн-проектирование не требуют выдуманного real grant.

Продуктовый odelix CLI и пользовательские Mission не исполняются автоматически инженерным ./workshop. Agent Validation Profile и plan не создают authority. Новые среды текущей агентной программы — READ_ONLY/REPLAY/PAPER, реальные действия отдельно не включены.
<!-- END GENERATED COMMON POLICY -->

## Если зависимость недоступна

Не искать молча соседний checkout и не копировать его внутренние types. Локальный реестр называет владельца и точный документальный источник; это не опубликованный schema/package. Запросить у Coordinator или producer release/commit, digest, fixture и consumer-test. При отсутствии обязательного артефакта блокируется зависимая часть задачи, а не вся автономная работа на явно помеченных fixtures.

## Первый экран и целевая глубина

Точный объём local/pilot/целевого каталога — [WORKSTATION-SPEC §Первый релиз](docs/WORKSTATION-SPEC.md#first-release-scope). Первой задачей переноса является WKS-018; eTape выбран для UI scope ET-01..07, а не остаётся кандидатом. [Что именно форкаем/адаптируем](docs/FOUNDATION-DECISION.md#selected-ui-scope) и [source→destination manifest](delivery/ETAPE-UI-IMPORT.json). Реализация/сборка не подтверждены этим документом. Не переносить всё приложение eTape, не создавать публичный GitHub fork и не писать выбранную готовую механику заново.
