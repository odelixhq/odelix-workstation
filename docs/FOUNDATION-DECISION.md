# Odelix Workstation — выбранная основа и точный UI-перенос eTape

**r15.11 · 28 сентября 2026. Scope decision: SELECTED_FOR_IMPLEMENTATION.** Это выбранный источник UI по уточнению пользователя, а не очередной кандидат. Реализация, fork на GitHub, сборка и performance этой сборкой **не подтверждаются**. Весь бандл не получает автоматического ACCEPTED.

[Вход](../AGENTS.md) · [Локальная архитектура](DEVELOPMENT.md#implementation-basis) · [Точный machine-readable import plan](../delivery/ETAPE-UI-IMPORT.json) · [Первый local/pilot scope](WORKSTATION-SPEC.md#first-release-scope)

<a id="current-recommendation"></a>
<a id="selected-ui-scope"></a>
## 1. Что решено

**React/TypeScript + Dockview core, с selective fork части eTape UI.** Сохраняем выбранную ревизию `79ee189069a85d3efc871bd5472de3135351d0e1`; переносим готовую механику, а не разрабатываем её снова с нуля. Принятие источника отделено от проверки получившейся интеграции.

| Состояние | Значение |
|---|---|
| Решение об источнике | SELECTED_FOR_IMPLEMENTATION |
| Объём | ET-01..ET-07 ниже; не всё приложение eTape |
| Фактический перенос/репозиторий | NOT_VERIFIED; этой документальной работой не создавался |
| Odelix build/tests | NOT_RUN |
| Скорость/стоимость/ёмкость | UNMEASURED |

Не требуется новое согласование самого выбора eTape для перечисленного scope. Требуются обычные code/license/contract checks и полномочия на внешние изменения. Если конкретный файл непригоден, это локальная корректировка порта с evidence, не молчаливый выбор другого терминала.

## 2. Какие именно части заимствуем

| ID / часть | Source entrypoints в eTape | Цель в odelix-workstation | Задачи |
|---|---|---|---|
| ET-01 — Shell, panel frame и реестр панелей | `ui/src/chrome/AppShell.tsx`; `ui/src/chrome/PanelFrame.tsx`; `ui/src/chrome/panels/registry.tsx` | `packages/layout/adapters/dockview/WorkbenchShell.tsx`; `packages/panel-runtime/PanelFrame.tsx`; `packages/panel-runtime/registry.ts` | `ODX-WKS-003`, `ODX-WKS-011` |
| ET-02 — Связывание инструментов между панелями | `ui/src/chrome/linkGroups.ts` | `packages/link-groups/LinkGroups.ts` | `ODX-WKS-003` |
| ET-03 — Планировщик отрисовки | `ui/src/render/Scheduler.ts` | `packages/render-runtime/Scheduler.ts` | `ODX-WKS-018`, `ODX-WKS-019` |
| ET-04 — График: оболочка и lifecycle | `ui/src/chrome/panels/ChartPanel.tsx` | `packages/chart-adapter-lightweight/ChartPanel.tsx` | `ODX-WKS-002` |
| ET-05 — DOM / визуализация стакана | `ui/src/chrome/panels/LadderPanel.tsx` | `packages/views-flow/DomPanel.tsx`; `packages/views-flow/renderers/ladder/` | `ODX-WKS-019` |
| ET-06 — Лента сделок | `ui/src/chrome/panels/TapePanel.tsx` | `packages/views-flow/TapePanel.tsx`; `packages/views-flow/renderers/tape/` | `ODX-WKS-019` |
| ET-07 — Спрос панелей и жизненный цикл подписки | `ui/src/wire/DemandRegistry.ts` | `packages/market-stream/PanelDemandRegistry.ts` | `ODX-WKS-007` |

В таблице перечислены **entrypoints для переноса фрагментов**, не разрешение автоматически копировать весь файл и его зависимости. WKS-018 фиксирует exact import closure и применимые upstream-тесты; новые source files добавляются только с provenance/review. Целевые пути — план до сверки фактического checkout.

## 3. Что значит «форкаем сразу»

**Selective source fork, а не GitHub Fork всего продукта.** Первый шаг WKS-018: локально получить pinned upstream, проверить/зафиксировать нужные файлы и notices, сохранить разрешённый reference subset в `vendor/etape-ui/` и создать воспроизводимую read-only сборку. Адаптированный runtime живёт в наших `packages/`, не запускает vendor app. Реальные hashes появляются после получения bytes; документальный manifest их не выдумывает.

Для закрытой разработки исходники переносятся в существующий приватный `odelix-workstation` в личном аккаунте либо организации, согласно проверенному binding. **Не нажимать Fork с ожиданием приватности:** native GitHub-fork публичного repo тоже публичный. Не создавать восьмой repo и не пушить ничего без явного доступа. [Официальная модель видимости GitHub](https://docs.github.com/en/pull-requests/reference/forks).

Dockview и Lightweight Charts подключаются версиями библиотек: их ядра не форкаем. На source intake фиксируем совместимый lockfile, не обновляем одновременно весь стек major-версий. MIT notices сохраняются. Assets/шрифты/branding не входят в заимствование.

## 4. Что заменяем и что не переносим

Odelix владеет `WorkspacePort`, `Command/Selection`, `MarketStream/HistoryTile` и `HostedHarnessClient`. eTape не становится источником numerical truth или бизнес-объектов. Убираем whole `App.tsx`, engine-config WorkspaceStore, WsClient/BookStore, Go/SQLite engine, exec hooks, broker credentials, order ticket, auto-unlock и торговые hotkeys. Чужие AGENTS/workflows не становятся нашими правилами.

Механика групп получает canonical instrument identity, tenant/user/workspace namespace, validation, generation guard и cleanup. Транспорт не наследует unbounded pending queue либо «delta = full replace». График не наследует US-session flat synthetic bars как данные.

Footprint, heatmap и Options views **не объявляются заимствованными из eTape**: их численные источники и границы уже принадлежат Market/Odelix. Scanner/Stock Info/News/Watchlist не добавляются автоматически в первый scope.

## 5. Последовательность, уже внесённая в Issues

1. `WKS-018` — pinned source subset, provenance/license/import boundaries и Scheduler baseline.
2. `WKS-003` — shell, panel registry/lifecycle и link groups; `WKS-002` — chart-panel поверх нашего RendererPort. Работы параллельны только при независимых files/lockfiles.
3. `WKS-011` — наш dev-host без чужого engine; локальная веха WKS-017/STK-014 по прежнему изолированному scope.
4. `WKS-019` — read-only DOM/tape и их семантические тесты; эти views обязательны в внешнем pilot STK-101, но не искусственный prerequisite первого локального footprint.
5. `WKS-007` — panel-demand adaptation и наш live stream; `WKS-016` — наша история блоками. Остальные UI/Research/Harness задачи используют те же packages, не второй fork.

Каждая стадия оставляет конкретный commit/diff, notices и проверенные результаты. Build failure не заменяется пометкой «not adopted»; предметный blocker фиксируется в своей Issue.

## 6. Обязательная приёмка заимствования

Удаление текущего уровня, пустая замена, отсутствие наблюдения и Invalidated различаются; закрытая история сохраняется. Duplicate/gap вызывают согласованное поведение; устаревшая generation не меняет новый инструмент/selection. Timestamp/units/quality проходят без потери смысла. Никакие расчёты donor не принимаются за Market authoritative data.

Две панели используют общий потребитель разрешённого потока; не создают отдельный server reducer. Pending/buffers ограничены по памяти; logout/unmount освобождают каналы и подписки. Падение painter видно, не выдаётся за свежий рынок. На первом этапе нет сетевых/broker/order capabilities. Наши market gates сохраняют независимые предметные, replay и эксплуатационные проверки.

<a id="historical-audit"></a>
## 7. Исторический обзор — доказательства выбора, не повторная очередь кандидатов

Ниже сохранён датированный аудит **до текущего выбора**. Его слова «рекомендация», «not adoption» и «после принятия» описывают 23 сентября, а не отменяют выбранный scope ET-01..07. Они не являются runtime evidence. Приоритет текущего задания — §§1–6 выше.

# Odelix Workstation — независимый обзор оснований

**Дата: 2026-09-23. Статус: ARCHITECTURAL RECOMMENDATION, NOT ADOPTION / NOT RUNTIME ACCEPTANCE.**

## Решение

Для текущего плана Odelix рекомендована собственная React/TypeScript-композиция на Dockview core с контролируемым заимствованием реализованных UI-механизмов eTape. Это не перенос всего eTape, его Go-движка, брокерских подключений или транспорта без изменений.

Самый богатый подтверждённый набор специализированного orderflow-клиента среди рассмотренных кандидатов — EdgeDepth. Его меньшая пригодность в качестве основного выбора обусловлена не отсутствием функций, а AGPL, C++/WASM-композицией, иной формой входных данных и необходимостью переделывать числовые/временные контракты. При отдельном решении об открытой клиентской части и принятии C++-стека это серьёзная альтернативная основа, а не отвергнутый проект.

Наиболее полноценная готовая универсальная workbench-платформа — Theia. У неё есть реальный browser-only target: прежнее предположение об обязательном отдельном Node backend для любого варианта было слишком сильным. Но финансовые canvas-представления и Odelix objects она не реализует.

## Границы проверки

Выборочно прочитаны исходники всех девяти направлений (OpenSumi core и starter проверены раздельно), manifests, внешние лицензии и точки расширения. Для лидеров дополнительно проверены публичные GitHub Actions. Просмотр отдельных диапазонов не является полным аудитом всего репозитория. Из существования API или тестового файла не делается вывод о проверенной Odelix-интеграции.

Попытка получить полные архивы исходников в локальную среду завершилась DNS-ошибкой codeload.github.com. Локальные npm/cargo/cmake-сборки, тесты приложений, браузерные сценарии, сравнение FPS/RAM и интеграция с hft-platform **не выполнены**. Исходники читались через GitHub-коннектор. Приведённые сведения CI — результаты upstream, не наши запуски. Ни один workflow не запускался этой работой.

Бандл r15.5, пользовательские репозитории, production и GitHub Project не изменены. Новые компоненты не установлены. Внешние аккаунты, биржевые ключи и финансовые операции не использовались.

## Критерий выбора

Оценивается не количество экранов вообще, а остаток полезного кода после удаления несовместимых частей и стоимость дальнейшего сопровождения. Приоритеты: browser-first; React/TypeScript; существующий Rust Market и Fastify; один Pi; точное выделение и асинхронные Evidence; общая выдача без отдельного рыночного состояния на подписку; воспроизводимый replay; отсутствие конкурирующего хранилища бизнес-истины.

Ни у одного кандидата не подтверждён готовый полный Odelix-контракт для immutable Evidence/Thesis, source cut, качества, числовой точности, live/replay и продуктовых разрешений. Это интеграционная работа независимо от оболочки.

## Сравнение

| Кандидат | Основная уже реализованная польза | Цена адаптации под текущий Odelix | Решение |
|---|---|---|---|
| eTape UI | Панели chart/DOM/tape, связывание инструментов, Dockview, imperative stores, цикл перерисовки | Средняя: отделить local engine, execution, transport, символы и persistence | Основной UI-донор |
| Dockview | Раскладка, tabs/groups, сохранение layout, плавающие и отдельные окна | Малая для layout, но это не полная Workstation | Библиотечная основа выбранной композиции |
| EdgeDepth | Footprint, GPU heatmap, DOM/tape, replay и JS/WASM boundary | Высокая для текущего закрытого React-клиента; меньше новой графики | Серьёзная условная альтернативная основа |
| Theia | Workbench, команды, widgets, docking, preferences, browser/desktop families | Средняя/высокая: минимальная композиция, реальные browser services, собственные market views | Резерв при обязательной IDE/extension-платформе |
| Open MCT | Объекты, providers, общая временная модель, исторические/текущие данные | Средняя/высокая: Vue-композиция, mapping Product, custom financial views | Не основная оболочка текущего плана |
| OpenSumi | Модульная React IDE/workbench | Высокая для старого lite starter: большой разрыв версий; core оценивать отдельно | Ниже Theia для новой базовой платформы |
| OpenTerminalUI | Широкие финансовые экраны, связанные графики и presets | Средняя: собственная grid-модель, store/API/Script lifecycle | Выборочные виджеты, не основа |
| Flowsurface | Реальный native Rust/Iced orderflow и pane model | Высокая относительно browser-first; GPL и другой GUI runtime | Только отдельная native-стратегия |
| aggr | Trade aggregation, worker/exchange adapters, charts и пользовательские выражения | Высокая: Vue 2, старый TS, fork chart library, другая граница данных | Reference, не новая основа |

Оценки цены качественные, не сроки и не результаты benchmark. Лицензии библиотек проверяются по конкретным заимствуемым файлам/пакетам; наличие MIT у корня не отменяет зависимостей.

## eTape: заимствовать после отделения от приложения

Подтверждённый полезный каркас — React вокруг imperative stores и chart/canvas surfaces. Реестр реально подключает chart, ladder, tape, scanner, watchlist, stock-info и несколько execution-oriented панелей. Полноценные Odelix footprint/heatmap/options views в этом реестре не подтверждены.

Основные изменения, без которых перенос неприемлем:

1. `App.tsx` создаёт `ws://`-соединение к текущему host и подключает темы, workspace, drawings и исполнение к общему engine API. Это не готовая облачная композиция Odelix. Новый host должен подключать Product и Market через наши интерфейсы.
2. При старте выбирается объединение topics всех зарегистрированных панелей, а не только реально видимых. Не наследовать такую модель подписок и не создавать рыночные вычисления по панели.
3. `BookStore.ts` ключуется строкой symbol и трактует snapshot и delta как полную замену. Требуются canonical instrument ID, точные данные и явный вид обновления. Настоящие L2-deltas нельзя подавать сюда без адаптации.
4. `WsClient.ts` буферизует команды/запросы в массиве, не имеющем в прочитанном классе byte-cap/deadline, и не реализует Odelix stream epoch/source revision. Предпочтительнее заменить transport на наш, а не достраивать поверх несовместимых допущений.
5. `ui/README.md` явно описывает синтетические плоские бары для пропущенных 10-секундных US-session интервалов. Это документированная политика того продукта, не скрытый обман. Но у Odelix gap должен оставаться gap: отключить эту политику либо строго изолировать визуальную интерполяцию от данных и Evidence.
6. Execution providers и команды исключаются из раннего read-only build, а не только скрываются CSS.

Забирать библиотечный layout, подход к lifetime панели, связанный инструмент, dirty-surface scheduler и подходящие rendering components. WorkspaceStore и AppShell composition не превращать в второй источник бизнес-объектов. Сохранить MIT notices и явное происхождение файлов.

## EdgeDepth: что действительно готово и что не совпадает

`FootprintManager` имеет не только декларации, но и реализацию накопления, группировки и кэша. `ShaderHeatmapRenderer` использует GPU-текстуры; в cpp обнаружены фактические uploads. Есть отдельный канал наблюдаемой realtime-глубины, управление cutoff при replay и JS exports управления.

Но `store_footprint` принимает полный минутный snapshot с минутным выравниванием; `get_merged_grouped` отвергает интервалы короче 60 секунд и не кратные минуте. Это конкретное несовпадение с гибкой временной основой Odelix, не просто косметическая переделка интерфейса.

Группировка использует double и собственное правило auto-grid; выбор POC при равных объёмах обходится через unordered_map. Последнее не задаёт независимого канонического tie-break. Не объявлять такой локальный результат авторитетным побитово воспроизводимым расчётом Market. Передаваемый `WSPayload` тоже не равен Odelix source-cut/quality/version contract.

Сильная сторона: много специализированной графики уже написано. Издержки: новый C++/Emscripten toolchain, ImGui/ImPlot-композиция, точные dependency pins, JS-мосты и AGPL. React-панель Ask технически можно связать с WASM, но это не делает интеграцию или лицензию автоматически решённой.

## Theia: исправление прежней оценки

В текущем дереве есть `examples/browser-only/package.json` с target browser-only и отдельные frontendOnly modules. Обязательный Node-процесс на пользователя — неверное универсальное описание.

Однако `frontend-only-application-module.ts` заменяет часть backend-служб ограниченными browser bindings: keystore возвращает пустые значения, backend request не исполняется, ConnectionStatus представлен постоянным ONLINE. Они допустимы как отсутствие соответствующей backend-возможности, но не заменяют реальную Odelix-аутентификацию, соединение и persistence.

Theia даёт больше готовой workbench-инфраструктуры, чем eTape. При этом нужны наши chart/flow views и интеграция объектов Product. Выбирать Theia стоит при действительной необходимости сторонних IDE-расширений, language tooling и глубокой editor-модели. Каталог из 72 финансовых views сам по себе не требует IDE runtime. В минимальную композицию не включать второй AI-runtime.

## Open MCT

Подтверждены namespace/key objects, object providers, composition, transactions и view lifecycle show/destroy. Собственную React-панель технически можно размещать в DOM-точке расширения: нельзя говорить, что Open MCT запрещает React. Но сама штатная оболочка и графики Vue-based; потребуется поддерживать две модели и мосты жизненного цикла.

Product остаётся владельцем Workspace/Thesis/Evidence. Object provider должен представлять эти объекты, не создавать параллельную бизнес-БД. Telemetry по умолчанию использует latest и может пропускать промежуточные значения при throttling; batch — другой явно заданный режим. Ни тот ни другой автоматически не доказывает точность book-delta/replay.

Рекомендация: не основной выбор текущей Odelix. Он был бы сильнее при продукте вокруг общей телеметрии, объектов и исторических dashboards, а не при первичном orderflow/chart-cluster.

## Остальные

OpenSumi core и его Web Lite нельзя оценивать как одну одинаково свежую поставку. В проверенном core 3.9.0/React18, в lite пакеты 2.26.8, React16, TypeScript3.8 и webpack4. Перенос starter потребует отдельной миграции; это не означает, что весь OpenSumi устарел.

OpenTerminalUI `ChartWorkstationPage.tsx` реализует связывание symbol/interval/crosshair/replay/dateRange, сохранённые views и grid/custom areas. Но страница имеет предел девять chart slots и собственные store/API/OpenScript-пути. Предел изменяемый, не фундаментальное ограничение; зато переход от chart grid к общему docking registry потребует переработки.

Flowsurface реально отделяет data/exchange crates и native Iced GUI. Heatmap использует собственные TimeSeries/HistoricalDepth и exchange types; pane enum включает charts, tape и ladder. Совпадение языка Rust с нашим сервером не делает эту GUI-систему браузерной React-основой.

aggr на текущей ревизии использует Vite4 (не webpack), Vue2.7, TypeScript3.9 и отдельный pinned fork Lightweight Charts. Adapter guide ориентирован на публичные биржевые WS/REST, а не Odelix. Поиском найден new Function в пользовательских выражениях; это не доказательство уязвимости, но такую исполняемую семантику нельзя автоматически переносить в многопользовательскую Research-платформу.

Dockview — один из выбранных готовых механизмов, не конкурент целому приложению. Текущий репозиторий разделяет MIT-пакеты и proprietary dockview-enterprise. Проверять лицензии exact pin. Layout undo/redo не заменяет Preview/Apply/Undo для бизнес-объектов.

## Проверенные upstream CI

| Проект / ревизия | Реально прочитанный результат | Граница доказательства |
|---|---|---|
| eTape 79ee189 | Run 35668494643: UI lint/test/build success; весь run failure из-за engine golangci-lint; contract drift step skipped | Не полный зелёный release; подходящие UI-tests выполнены upstream, не у нас |
| EdgeDepth 1fea3c4 | Run 34898738875 build success; workflow содержит native, CSV/Parquet/wire, stream lifecycle, reconnect, heatmap live, realtime depth regressions и WASM build | Шире одной компиляции; всё равно не Odelix-feed или наш load test |
| Open MCT b589f7f | Run 35883671148 e2e-couchdb success; в выборке на этом SHA e2e-perf skipped | Не выдавать skipped performance workflow за измеренную скорость |
| Остальные | manifests/выбранные исходники; полный результат CI для выбранного SHA не установлен в этой работе | Не заявляется runtime или performance readiness |

## Предлагаемая реализация после принятия решения

Одна shell/layout-система: React + Dockview. Один явно выбранный источник заимствования UI: eTape на зафиксированной ревизии. Не переносить целиком App.tsx или broker-oriented composition. Не одновременно мигрировать donor-код и все его dependency major versions.

Нужны четыре наши границы: MarketStream/HistoryTile, Product Workspace/Object, Command/Selection и Hosted Harness client. Объекты Product и численные результаты Market не дублируются.

Первые законченные работы: read-only shell с chart/DOM/tape и отключёнными чужими сетевыми путями; затем наш shared-stream/tiles adapter; затем Selection→Evidence/as-shown replay; отдельно footprint/heatmap renderers поверх уже существующих численных результатов Market. Все изменения проходят delete/empty/nochange/invalidated, duplicate/gap, switch-generation, multi-venue identity, exact units, bounded memory, unmount/reconnect tests.

Принятие основы не означает подтверждение FPS, сроков или готовности всего проекта. Заявления «70–80% готово» из количества чужих экранов не делаются. Для нашего текущего плана у eTape/Dockview лучший ожидаемый баланс полезного UI и размера адаптации; у EdgeDepth больше готовой специализированной графики, но другой совокупный выбор стека и лицензии.

## Источники: зафиксированные ревизии и прочитанные файлы

Список указывает источники, прочитанные полностью либо в релевантных диапазонах. Он не означает полный аудит всего файла или checkout. Дополнительные code-search snippets использованы для поиска callers и extension points.

### eTape

Repository: `earlisreal/eTape`. Commit: `79ee189069a85d3efc871bd5472de3135351d0e1`.

- `LICENSE` — `https://github.com/earlisreal/eTape/blob/79ee189069a85d3efc871bd5472de3135351d0e1/LICENSE`
- `ui/README.md` — `https://github.com/earlisreal/eTape/blob/79ee189069a85d3efc871bd5472de3135351d0e1/ui/README.md`
- `ui/src/README.md` — `https://github.com/earlisreal/eTape/blob/79ee189069a85d3efc871bd5472de3135351d0e1/ui/src/README.md`
- `ui/src/App.tsx` — `https://github.com/earlisreal/eTape/blob/79ee189069a85d3efc871bd5472de3135351d0e1/ui/src/App.tsx`
- `ui/src/wire/WsClient.ts` — `https://github.com/earlisreal/eTape/blob/79ee189069a85d3efc871bd5472de3135351d0e1/ui/src/wire/WsClient.ts`
- `ui/src/data/registry.ts` — `https://github.com/earlisreal/eTape/blob/79ee189069a85d3efc871bd5472de3135351d0e1/ui/src/data/registry.ts`
- `ui/src/data/BookStore.ts` — `https://github.com/earlisreal/eTape/blob/79ee189069a85d3efc871bd5472de3135351d0e1/ui/src/data/BookStore.ts`
- `ui/src/render/Scheduler.ts` — `https://github.com/earlisreal/eTape/blob/79ee189069a85d3efc871bd5472de3135351d0e1/ui/src/render/Scheduler.ts`
- `ui/src/chrome/panels/registry.tsx` — `https://github.com/earlisreal/eTape/blob/79ee189069a85d3efc871bd5472de3135351d0e1/ui/src/chrome/panels/registry.tsx`

### Open MCT

Repository: `nasa/openmct`. Commit: `b589f7f4de94f847aa8660e90fd85c87fa5e4026`.

- `package.json` — `https://github.com/nasa/openmct/blob/b589f7f4de94f847aa8660e90fd85c87fa5e4026/package.json`
- `API.md` — `https://github.com/nasa/openmct/blob/b589f7f4de94f847aa8660e90fd85c87fa5e4026/API.md`
- `src/api/objects/ObjectAPI.js` — `https://github.com/nasa/openmct/blob/b589f7f4de94f847aa8660e90fd85c87fa5e4026/src/api/objects/ObjectAPI.js`
- `src/api/telemetry/TelemetryAPI.js` — `https://github.com/nasa/openmct/blob/b589f7f4de94f847aa8660e90fd85c87fa5e4026/src/api/telemetry/TelemetryAPI.js`
- `src/plugins/plot/PlotViewProvider.js` — `https://github.com/nasa/openmct/blob/b589f7f4de94f847aa8660e90fd85c87fa5e4026/src/plugins/plot/PlotViewProvider.js`

### Theia

Repository: `eclipse-theia/theia`. Commit: `1ccb27deeee5b1c6c3492ae32ae0fc4e459b5b68`.

- `packages/core/package.json` — `https://github.com/eclipse-theia/theia/blob/1ccb27deeee5b1c6c3492ae32ae0fc4e459b5b68/packages/core/package.json`
- `examples/browser-only/package.json` — `https://github.com/eclipse-theia/theia/blob/1ccb27deeee5b1c6c3492ae32ae0fc4e459b5b68/examples/browser-only/package.json`
- `packages/core/src/browser-only/frontend-only-application-module.ts` — `https://github.com/eclipse-theia/theia/blob/1ccb27deeee5b1c6c3492ae32ae0fc4e459b5b68/packages/core/src/browser-only/frontend-only-application-module.ts`

### OpenSumi core

Repository: `opensumi/core`. Commit: `9fee6aa38f96f85ac686f25c2522910067324c1d`.

- `packages/core-browser/package.json` — `https://github.com/opensumi/core/blob/9fee6aa38f96f85ac686f25c2522910067324c1d/packages/core-browser/package.json`

### OpenSumi Web Lite

Repository: `opensumi/ide-startup-lite`. Commit: `b8f2fcfa01bc0a998892e018d573a9c18c7839e0`.

- `package.json` — `https://github.com/opensumi/ide-startup-lite/blob/b8f2fcfa01bc0a998892e018d573a9c18c7839e0/package.json`

### EdgeDepth

Repository: `edgedepthhq/edgedepth-terminal`. Commit: `1fea3c432a35063fbb2674a4f9b2932c977c3afb`.

- `LICENSE` — `https://github.com/edgedepthhq/edgedepth-terminal/blob/1fea3c432a35063fbb2674a4f9b2932c977c3afb/LICENSE`
- `CMakeLists.txt` — `https://github.com/edgedepthhq/edgedepth-terminal/blob/1fea3c432a35063fbb2674a4f9b2932c977c3afb/CMakeLists.txt`
- `src/core/footprint_manager.h` — `https://github.com/edgedepthhq/edgedepth-terminal/blob/1fea3c432a35063fbb2674a4f9b2932c977c3afb/src/core/footprint_manager.h`
- `src/core/footprint_manager.cpp` — `https://github.com/edgedepthhq/edgedepth-terminal/blob/1fea3c432a35063fbb2674a4f9b2932c977c3afb/src/core/footprint_manager.cpp`
- `src/rendering/shader_heatmap_renderer.h` — `https://github.com/edgedepthhq/edgedepth-terminal/blob/1fea3c432a35063fbb2674a4f9b2932c977c3afb/src/rendering/shader_heatmap_renderer.h`
- `protos/messages.proto` — `https://github.com/edgedepthhq/edgedepth-terminal/blob/1fea3c432a35063fbb2674a4f9b2932c977c3afb/protos/messages.proto`
- `.github/workflows/build.yml` — `https://github.com/edgedepthhq/edgedepth-terminal/blob/1fea3c432a35063fbb2674a4f9b2932c977c3afb/.github/workflows/build.yml`

### OpenTerminalUI

Repository: `laanito/OpenTerminalUI`. Commit: `92d8d7618da2ebcb4176dc2c2433bac02915d007`.

- `frontend/src/pages/ChartWorkstationPage.tsx` — `https://github.com/laanito/OpenTerminalUI/blob/92d8d7618da2ebcb4176dc2c2433bac02915d007/frontend/src/pages/ChartWorkstationPage.tsx`

### Flowsurface

Repository: `flowsurface-rs/flowsurface`. Commit: `c1388d4f6765b56ce02783981311cf46435e5c48`.

- `Cargo.toml` — `https://github.com/flowsurface-rs/flowsurface/blob/c1388d4f6765b56ce02783981311cf46435e5c48/Cargo.toml`
- `src/chart/heatmap.rs` — `https://github.com/flowsurface-rs/flowsurface/blob/c1388d4f6765b56ce02783981311cf46435e5c48/src/chart/heatmap.rs`
- `data/src/layout/pane.rs` — `https://github.com/flowsurface-rs/flowsurface/blob/c1388d4f6765b56ce02783981311cf46435e5c48/data/src/layout/pane.rs`

### aggr

Repository: `Tucsky/aggr`. Commit: `41c1609ea3ac7c2dcf12ffba94ac78027e1c0012`.

- `package.json` — `https://github.com/Tucsky/aggr/blob/41c1609ea3ac7c2dcf12ffba94ac78027e1c0012/package.json`
- `docs/ADDING_NEW_EXCHANGE.md` — `https://github.com/Tucsky/aggr/blob/41c1609ea3ac7c2dcf12ffba94ac78027e1c0012/docs/ADDING_NEW_EXCHANGE.md`

### Dockview

Repository: `dockview/dockview`. Commit: `24e622438baa5a901634298f59ad24432652b6ed`.

- `README.md` — `https://github.com/dockview/dockview/blob/24e622438baa5a901634298f59ad24432652b6ed/README.md`

### CI endpoints

- eTape: `https://api.github.com/repos/earlisreal/eTape/actions/runs/35668494643/jobs?per_page=10`
- EdgeDepth: `https://github.com/edgedepthhq/edgedepth-terminal/actions/runs/34898738875`
- Open MCT: `https://github.com/nasa/openmct/actions/runs/35883671148`

### Лицензионная граница

GNU GPL FAQ: `https://www.gnu.org/licenses/gpl-faq.en.html`. Copyleft не запрещает коммерческое использование; форма и семантика объединения компонентов имеют значение. Отдельный процесс или iframe не являются автоматической лицензией на закрытый производный продукт. Для EdgeDepth требуется решение о соответствии AGPL либо отдельное разрешение правообладателя; наличие предложения коммерческой лицензии здесь не подтверждено. Для Theia проверять точные EPL/secondary-license условия и используемые extensions. Это screening архитектуры, не юридическое заключение.


## 8. Проверочный вертикальный срез вместо нового большого конкурса

Основное предлагаемое направление: **выборочный eTape UI donor на существующем Dockview baseline**. Open MCT не первый contender после глубокого обзора. Theia/OpenSumi остаются резервными полными workbench-вариантами; EdgeDepth — отдельная ветка после лицензии. Не запускаем пять переписанных терминалов параллельно и не заставляем Market ждать этого выбора.

Одинаковый read-only срез: один реальный/либо явно SYNTHETIC fixture feed с соответствующим статусом; два связанных market views, DOM/tape/CVD, переключение инструмента; выделение области → SelectionRef → mocked typed assessment → Evidence; сохранение Workspace через test Product port; live/replay switch; producer delete/empty replace/nochange/invalidated и sequence resync.

Проверки: два views не создают два Market core состояния; поздний ответ не меняет новый selection; текущее удаление не стирает закрытую историю; контекст восстанавливается с тем же object ID; high-rate path не гонит каждый tick через весь React tree; нет broker keys/order/auto-unlock кодового пути; лицензии/dependencies/NOTICE учтены. Числа должны приходить из Market fixture, а не от donor arithmetic.

До начала зафиксировать acceptance-device/workload и бюджеты boot, JS/WASM size, памяти, renderer frame latency, reconnect/recovery, source lines/patch surface и времени разработки. Измерить dependency/coupling removal, тесты и upstream update rehearsal. Не обещать «80% кода готово» или 100k-user scale по внешнему виду клиента; серверные характеристики и client workload — отдельные показатели.

Stop conditions: donor нельзя отделить от execution/backend без большой новой системы; требует core fork без понятной maintenance модели; не позволяет сохранить наш time/quality/selection contract; лицензия не принята; нет измеримого выигрыша против существующих AlphaQuant + own baseline. При stop сохраняется baseline, а не блокируется вся Workstation.

<a id="external-data-layer"></a>
## Data layer не меняет выбранный UI donor

eTape ET-01..07 остаётся SELECTED_FOR_IMPLEMENTATION. OpenBB ODP выбран **отдельно** как provider middleware за Odelix data ports; его UI, agent runtime и рыночные stores не перенимаются. [Data preview UX](WORKSTATION-SPEC.md#external-data-ux).

Данные eTape OpenD/Alpaca/Yahoo — только кандидаты для самостоятельного source review; их наличие upstream не разрешает использование аккаунтов, хранения или перераспространения Odelix. Карта ETAPE-UI-IMPORT.json сохраняет исключение donor data backend. Новый transport не расширяет список UI-донорских файлов.

