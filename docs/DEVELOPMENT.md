# odelix-workstation — руководство разработчика

**r15.11 · 2026-09-28 · PROPOSED / не статус runtime.** В этом файле объединены локальная архитектура, контракты, карта модулей, тесты и выпуск. Подробные предметные спецификации сохранены отдельно.

[Вход в repo](../AGENTS.md) · [Локальная выборка задач](../delivery/CONTEXT.json)


<!-- BEGIN GENERATED IMPLEMENTATION BASIS -->
<a id="implementation-basis"></a>
## Основа реализации: что берём, что пишем и где интегрируем

**r15.11. React/TypeScript и Dockview core — действующий baseline. eTape SELECTED_FOR_IMPLEMENTATION: selective fork UI-механики ET-01..07 из commit 79ee189069a85d3efc871bd5472de3135351d0e1. Сначала WKS-018, затем WKS-003/002/011/019 и WKS-007. Это выбор источника и задач, не выполненный импорт. Market/Product/Pi, Layer/Selection и numerical truth остаются нашими; performance UNMEASURED. Внешние данные доступны через Odelix DataBinding/provider client, не через OpenBB SDK в браузере; native flow не подменяется snapshots.**

Это локальная генерируемая выборка общего решения, а не отдельный редактируемый реестр. Полные metadata — `delivery/CONTEXT.json → implementation_blueprint`. Изменения предлагает владелец repo через Coordinator; генератор обновляет общий и локальные виды вместе. Конкретный upstream/pin и supply-chain проверяются перед включением, не по наличию названия в таблице.

**Три разных зависимости:** библиотека/внешний код; внешний сервис/данные; контракт другого Odelix repo. Последний не разрешает копировать чужую реализацию. Ниже сами архитектурные bindings; реестр документальных источников в конце файла — другая сущность.

| Источник | Режим | Модули этого repo | Задачи |
|---|---|---|---|
| [OWN](#reuse-BND-032) | REUSE_OWN | WKS-APP, WKS-VIEW | [ODX-WKS-008](#issue-ODX-WKS-008), [ODX-WKS-016](#issue-ODX-WKS-016) |
| [PI](#reuse-BND-033) | CONSUME_OWN_RELEASE | WKS-APP, WKS-VIEW | [ODX-WKS-006](#issue-ODX-WKS-006), [ODX-WKS-010](#issue-ODX-WKS-010) |
| [REACT](#reuse-BND-034) | LIBRARY | WKS-APP, WKS-VIEW | [ODX-WKS-001](#issue-ODX-WKS-001), [ODX-WKS-009](#issue-ODX-WKS-009), [ODX-WKS-010](#issue-ODX-WKS-010) |
| [TANSTACK](#reuse-BND-035) | LIBRARY | WKS-APP, WKS-VIEW | [ODX-WKS-001](#issue-ODX-WKS-001), [ODX-WKS-009](#issue-ODX-WKS-009) |
| [CHART](#reuse-BND-036) | LIBRARY | WKS-SCN | [ODX-WKS-002](#issue-ODX-WKS-002) |
| [DOCK](#reuse-BND-037) | LIBRARY | WKS-LYT | [ODX-WKS-003](#issue-ODX-WKS-003) |
| [EDITOR](#reuse-BND-038) | LIBRARY | WKS-CFG | [ODX-WKS-004](#issue-ODX-WKS-004) |
| [DESKTOP](#reuse-BND-039) | ALTERNATIVE_NOT_SELECTED | WKS-APP | [ODX-WKS-005](#issue-ODX-WKS-005), [ODX-WKS-006](#issue-ODX-WKS-006) |
| [GPU](#reuse-BND-040) | ALTERNATIVE_NOT_SELECTED | WKS-VIEW | [ODX-WKS-008](#issue-ODX-WKS-008) |
| [ALPHAQUANT](#reuse-BND-041) | REUSE_OWN_UX | WKS-APP, WKS-VIEW | [ODX-WKS-011](#issue-ODX-WKS-011), [ODX-WKS-012](#issue-ODX-WKS-012), [ODX-WKS-013](#issue-ODX-WKS-013), [ODX-WKS-015](#issue-ODX-WKS-015) |
| [ETAPE](#reuse-BND-047) | SELECTIVE_PORT | WKS-APP, WKS-LYT, WKS-SCN, WKS-VIEW | [ODX-WKS-002](#issue-ODX-WKS-002), [ODX-WKS-003](#issue-ODX-WKS-003), [ODX-WKS-007](#issue-ODX-WKS-007), [ODX-WKS-008](#issue-ODX-WKS-008), [ODX-WKS-011](#issue-ODX-WKS-011), [ODX-WKS-018](#issue-ODX-WKS-018), [ODX-WKS-019](#issue-ODX-WKS-019) |
| [OPENBB](#reuse-BND-050) | CONTRACT_CONSUMER_NOT_OPENBB_IMPORT | WKS-SCN, WKS-VIEW | [ODX-WKS-020](#issue-ODX-WKS-020), [ODX-WKS-021](#issue-ODX-WKS-021) |

<a id="reuse-BND-032"></a>
### OWN: REUSE_OWN

**Берём:** Наш опубликованный Market/compute contract и fixtures; существующее Rust-ядро остаётся в Market.

**Пишем сами:** Локальный consumer adapter и независимая интеграционная проверка; для Execution — согласованный simulation port.

**Не переносим / граница:** Не копировать book/journal/sim в этот repo и не использовать чужие приватные пути.

**Источник:** `https://github.com/a3ka/hft-platform/tree/4ddd392e14e1dbd4c0b2511e50ddc9e48b8707e0`. **Source pin:** `4ddd392e14e1dbd4c0b2511e50ddc9e48b8707e0`; для ETAPE/OPENBB это выбранная ревизия исходников, для прочих — provenance; не installed package lock. **Проверка:** `INHERITED_DECLARED_SCOPE_NOT_FRESH_SOURCE_AUDIT`.

**Предлагаемые локальные места из действующих карточек** (не подтверждённый code checkout):

| Issue / модуль | Пути |
|---|---|
| [ODX-WKS-008](#issue-ODX-WKS-008) / `WKS-VIEW` | `packages/high-rate-renderer/`; `tests/renderer-benchmark.spec.ts` |
| [ODX-WKS-016](#issue-ODX-WKS-016) / `WKS-APP` | `packages/market-stream/`; `packages/scene/`; `packages/views/`; `tests/` |

<a id="reuse-BND-033"></a>
### PI: CONSUME_OWN_RELEASE

**Берём:** Публичный Odelix Harness package/events, построенный владельцем поверх Pi; не второй Pi fork в этом repo.

**Пишем сами:** Наш host binding или typed client/session adapter; Product размещает Run, Workstation отображает его события.

**Не переносим / граница:** Не импортировать приватные Skills в клиент; не форкать Pi повторно; не строить новый agent loop.

**Источник:** `https://github.com/earendil-works/pi/blob/main/packages/coding-agent/docs/sdk.md`. **Source pin:** `NOT_SELECTED`; для ETAPE/OPENBB это выбранная ревизия исходников, для прочих — provenance; не installed package lock. **Проверка:** `INHERITED_DECLARED_SCOPE_NOT_FRESH_SOURCE_AUDIT`.

**Предлагаемые локальные места из действующих карточек** (не подтверждённый code checkout):

| Issue / модуль | Пути |
|---|---|
| [ODX-WKS-006](#issue-ODX-WKS-006) / `WKS-APP` | `apps/desktop/`; `packages/desktop-bridge/`; `tests/desktop-recovery.spec.ts` |
| [ODX-WKS-010](#issue-ODX-WKS-010) / `WKS-VIEW` | `packages/views-agents/`; `packages/views-replay/`; `tests/console-replay.spec.ts` |

<a id="reuse-BND-034"></a>
### REACT: LIBRARY

**Берём:** React + TypeScript: UI composition и типы. Next.js относится к browser host при подтверждении выбранной конфигурации, не ко всем пакетам WKS.

**Пишем сами:** Наши компоненты и bindings; high-rate данные вне React setState на каждый tick.

**Не переносим / граница:** Это не готовый терминал, не общий backend и не permission boundary.

**Источник:** `https://nextjs.org/docs`. **Source pin:** `NOT_SELECTED`; для ETAPE/OPENBB это выбранная ревизия исходников, для прочих — provenance; не installed package lock. **Проверка:** `INHERITED_DECLARED_SCOPE_NOT_FRESH_SOURCE_AUDIT`.

**Предлагаемые локальные места из действующих карточек** (не подтверждённый code checkout):

| Issue / модуль | Пути |
|---|---|
| [ODX-WKS-001](#issue-ODX-WKS-001) / `WKS-APP` | `packages/object-runtime/`; `packages/command-runtime/`; `packages/scene/`; `tests/scene-contracts.test.ts` |
| [ODX-WKS-009](#issue-ODX-WKS-009) / `WKS-VIEW` | `packages/views-options/`; `tests/options-context.spec.ts` |
| [ODX-WKS-010](#issue-ODX-WKS-010) / `WKS-VIEW` | `packages/views-agents/`; `packages/views-replay/`; `tests/console-replay.spec.ts` |

<a id="reuse-BND-035"></a>
### TANSTACK: LIBRARY

**Берём:** TanStack Query для request lifecycle/cache server objects; точный пакет/pin выбирается в задаче.

**Пишем сами:** Query keys с tenant/object/revision, invalidation и typed API client.

**Не переносим / граница:** Не класть каждый tick и полную книгу в query cache как authoritative Market state.

**Источник:** `https://tanstack.com/query/latest`. **Source pin:** `NOT_SELECTED`; для ETAPE/OPENBB это выбранная ревизия исходников, для прочих — provenance; не installed package lock. **Проверка:** `INHERITED_DECLARED_SCOPE_NOT_FRESH_SOURCE_AUDIT`.

**Предлагаемые локальные места из действующих карточек** (не подтверждённый code checkout):

| Issue / модуль | Пути |
|---|---|
| [ODX-WKS-001](#issue-ODX-WKS-001) / `WKS-APP` | `packages/object-runtime/`; `packages/command-runtime/`; `packages/scene/`; `tests/scene-contracts.test.ts` |
| [ODX-WKS-009](#issue-ODX-WKS-009) / `WKS-VIEW` | `packages/views-options/`; `tests/options-context.spec.ts` |

<a id="reuse-BND-036"></a>
### CHART: LIBRARY

**Берём:** TradingView Lightweight Charts — price/time series, axes и custom-series/primitive extension API.

**Пишем сами:** RendererPort, ChartCluster, SelectionRef/overlays; mapping exact values для отображения.

**Не переносим / граница:** Не готовые heatmap/footprint/orderflow interpretation; библиотека не источник цены или replay truth.

**Источник:** `https://github.com/tradingview/lightweight-charts`. **Source pin:** `NOT_SELECTED`; для ETAPE/OPENBB это выбранная ревизия исходников, для прочих — provenance; не installed package lock. **Проверка:** `INHERITED_DECLARED_SCOPE_NOT_FRESH_SOURCE_AUDIT`.

**Предлагаемые локальные места из действующих карточек** (не подтверждённый code checkout):

| Issue / модуль | Пути |
|---|---|
| [ODX-WKS-002](#issue-ODX-WKS-002) / `WKS-SCN` | `packages/chart-adapter-lightweight/`; `packages/scene/src/selection.ts`; `packages/scene/src/context-chip.tsx`; `tests/selection-chart.spec.ts`; `packages/chart-adapter-lightweight/ChartPanel.tsx` |

<a id="reuse-BND-037"></a>
### DOCK: LIBRARY

**Берём:** Dockview core и React bindings — docking, splits, tabs, layout serialization.

**Пишем сами:** Наш LayoutPort, ViewInstance/Scene bindings, изменение среды через команды и Product revisions.

**Не переносим / граница:** dockview-enterprise — отдельная коммерческая лицензия; встроенный layout undo/redo не считать MIT-core. Product ChangeSet/Undo этим не заменяется.

**Источник:** `https://dockview.dev/docs/overview/licence/`. **Source pin:** `NOT_SELECTED`; для ETAPE/OPENBB это выбранная ревизия исходников, для прочих — provenance; не installed package lock. **Проверка:** `LICENSE_SPLIT_RECHECKED_2026-09-23_NOT_PACKAGE_ADOPTION`.

**Предлагаемые локальные места из действующих карточек** (не подтверждённый code checkout):

| Issue / модуль | Пути |
|---|---|
| [ODX-WKS-003](#issue-ODX-WKS-003) / `WKS-LYT` | `packages/layout/`; `packages/layout/adapters/dockview/`; `tests/layout-recovery.spec.ts`; `packages/layout/adapters/dockview/WorkbenchShell.tsx`; `packages/panel-runtime/PanelFrame.tsx`; `packages/panel-runtime/registry.ts`; `packages/link-groups/LinkGroups.ts` |

<a id="reuse-BND-038"></a>
### EDITOR: LIBRARY

**Берём:** CodeMirror 6 — редактор текста/формул и extension API.

**Пишем сами:** SpecEditorPort, syntax/diagnostics, edit-to-draft и безопасный submit в Research.

**Не переносим / граница:** Редактор не исполняет пользовательский код и не определяет исследовательскую семантику.

**Источник:** `https://codemirror.net/`. **Source pin:** `NOT_SELECTED`; для ETAPE/OPENBB это выбранная ревизия исходников, для прочих — provenance; не installed package lock. **Проверка:** `INHERITED_DECLARED_SCOPE_NOT_FRESH_SOURCE_AUDIT`.

**Предлагаемые локальные места из действующих карточек** (не подтверждённый code checkout):

| Issue / модуль | Пути |
|---|---|
| [ODX-WKS-004](#issue-ODX-WKS-004) / `WKS-CFG` | `packages/spec-editor/`; `packages/spec-editor/adapters/codemirror/`; `tests/spec-editor.spec.ts` |

<a id="reuse-BND-039"></a>
### DESKTOP: ALTERNATIVE_NOT_SELECTED

**Берём:** Tauri ИЛИ Electron — один host после отдельной проверки требований.

**Пишем сами:** Тонкий host, signed updates, window/security policy; общий browser WKS UI.

**Не переносим / граница:** Не оба одновременно, не отдельный UI и не private Skills в desktop bundle.

**Источник:** `https://v2.tauri.app/`. **Source pin:** `NOT_SELECTED`; для ETAPE/OPENBB это выбранная ревизия исходников, для прочих — provenance; не installed package lock. **Проверка:** `INHERITED_DECLARED_SCOPE_NOT_FRESH_SOURCE_AUDIT`.

**Предлагаемые локальные места из действующих карточек** (не подтверждённый code checkout):

| Issue / модуль | Пути |
|---|---|
| [ODX-WKS-005](#issue-ODX-WKS-005) / `WKS-APP` | `apps/desktop-spike/`; `docs/ADR-desktop-host.md`; `tests/desktop-host.spec.ts` |
| [ODX-WKS-006](#issue-ODX-WKS-006) / `WKS-APP` | `apps/desktop/`; `packages/desktop-bridge/`; `tests/desktop-recovery.spec.ts` |

<a id="reuse-BND-040"></a>
### GPU: ALTERNATIVE_NOT_SELECTED

**Берём:** PixiJS либо собственный WebGL2 renderer — выбранный ограниченный путь плотных графиков.

**Пишем сами:** Heatmap/footprint rendering, clipping, buffers, deletes/revisions, восстановление контекста.

**Не переносим / граница:** Не отдельный Market reducer на панель; SDK не подтверждает производительность без наших измерений.

**Источник:** `https://pixijs.com/`. **Source pin:** `NOT_SELECTED`; для ETAPE/OPENBB это выбранная ревизия исходников, для прочих — provenance; не installed package lock. **Проверка:** `INHERITED_DECLARED_SCOPE_NOT_FRESH_SOURCE_AUDIT`.

**Предлагаемые локальные места из действующих карточек** (не подтверждённый code checkout):

| Issue / модуль | Пути |
|---|---|
| [ODX-WKS-008](#issue-ODX-WKS-008) / `WKS-VIEW` | `packages/high-rate-renderer/`; `tests/renderer-benchmark.spec.ts` |

<a id="reuse-BND-041"></a>
### ALPHAQUANT: REUSE_OWN_UX

**Берём:** Пользовательские AlphaQuant Workspaces/Options прототипы — interactions, layout и storyboard.

**Пишем сами:** Перенос в shared WKS components; настоящие Scene/Selection/Evidence/Replay bindings.

**Не переносим / граница:** Не выдавать sample data/эмуляции/эвристики за готовую аналитику или торговые сигналы.

**Источник:** `odelix-web:docs/PRODUCT-SURFACE.md#prototype-migration`. **Source pin:** `original archive preserved in archive/bundles/AlphaQuant-prototypes-user-input.zip`; для ETAPE/OPENBB это выбранная ревизия исходников, для прочих — provenance; не installed package lock. **Проверка:** `INHERITED_DECLARED_SCOPE_NOT_FRESH_SOURCE_AUDIT`.

**Предлагаемые локальные места из действующих карточек** (не подтверждённый code checkout):

| Issue / модуль | Пути |
|---|---|
| [ODX-WKS-011](#issue-ODX-WKS-011) / `WKS-APP` | `apps/dev-workbench/`; `packages/client-runtime/`; `tests/dev_workbench.test.ts` |
| [ODX-WKS-012](#issue-ODX-WKS-012) / `WKS-VIEW` | `packages/renderers/footprint/`; `packages/scene/`; `tests/footprint_view.test.ts` |
| [ODX-WKS-013](#issue-ODX-WKS-013) / `WKS-VIEW` | `packages/views/semantic-flow/`; `packages/commands/`; `tests/semantic_view.test.ts` |
| [ODX-WKS-015](#issue-ODX-WKS-015) / `WKS-VIEW` | `packages/views/similarity/`; `packages/adapters/market-similarity/`; `tests/similarity_view.test.ts` |

<a id="reuse-BND-047"></a>
### ETAPE: SELECTIVE_PORT

**Берём:** ET-01 shell/panel registry; ET-02 link-groups; ET-03 scheduler; ET-04 chart lifecycle; ET-05 DOM; ET-06 tape; ET-07 panel-demand lifecycle. Точный source→destination — локальный ETAPE-UI-IMPORT.json.

**Пишем сами:** Odelix composition root; Scene/Selection/Command/Workspace ports; typed stream/history adapter, identity/time/quality, отказ/cleanup и наши проверки.

**Не переносим / граница:** Не брать целиком App.tsx/AppShell, Go engine, WS/stores, SQLite, execution/keys/order UI, synthetic US bars и шрифты.

**Источник:** `https://github.com/earlisreal/eTape/tree/79ee189069a85d3efc871bd5472de3135351d0e1`. **Source pin:** `79ee189069a85d3efc871bd5472de3135351d0e1`; для ETAPE/OPENBB это выбранная ревизия исходников, для прочих — provenance; не installed package lock. **Проверка:** `DATED_SOURCE_AUDIT_PLUS_2026_09_27_RANGE_RECHECK_NOT_BUILD`.

**Предлагаемые локальные места из действующих карточек** (не подтверждённый code checkout):

| Issue / модуль | Пути |
|---|---|
| [ODX-WKS-002](#issue-ODX-WKS-002) / `WKS-SCN` | `packages/chart-adapter-lightweight/`; `packages/scene/src/selection.ts`; `packages/scene/src/context-chip.tsx`; `tests/selection-chart.spec.ts`; `packages/chart-adapter-lightweight/ChartPanel.tsx` |
| [ODX-WKS-003](#issue-ODX-WKS-003) / `WKS-LYT` | `packages/layout/`; `packages/layout/adapters/dockview/`; `tests/layout-recovery.spec.ts`; `packages/layout/adapters/dockview/WorkbenchShell.tsx`; `packages/panel-runtime/PanelFrame.tsx`; `packages/panel-runtime/registry.ts`; `packages/link-groups/LinkGroups.ts` |
| [ODX-WKS-007](#issue-ODX-WKS-007) / `WKS-SCN` | `packages/market-stream/`; `packages/render-data-worker/`; `tests/render-buffer.spec.ts`; `packages/market-stream/PanelDemandRegistry.ts` |
| [ODX-WKS-008](#issue-ODX-WKS-008) / `WKS-VIEW` | `packages/high-rate-renderer/`; `tests/renderer-benchmark.spec.ts` |
| [ODX-WKS-011](#issue-ODX-WKS-011) / `WKS-APP` | `apps/dev-workbench/`; `packages/client-runtime/`; `tests/dev_workbench.test.ts` |
| [ODX-WKS-018](#issue-ODX-WKS-018) / `WKS-APP` | `vendor/etape-ui/SOURCE.json`; `vendor/etape-ui/LICENSE`; `packages/render-runtime/Scheduler.ts`; `tests/etape-import-boundary.test.ts` |
| [ODX-WKS-019](#issue-ODX-WKS-019) / `WKS-VIEW` | `packages/views-flow/DomPanel.tsx`; `packages/views-flow/TapePanel.tsx`; `packages/views-flow/renderers/ladder/`; `packages/views-flow/renderers/tape/`; `tests/etape-flow-panels.spec.ts` |

<a id="reuse-BND-050"></a>
### OPENBB: CONTRACT_CONSUMER_NOT_OPENBB_IMPORT

**Берём:** OpenBB ODP pinned separate service for early Deribit bars/chain and ECB dated reference snapshots. FRED API profile disabled: bounded personal copies are separate, not ODP/Pi/product data.

**Пишем сами:** Source/precision/quality UI, cancellation/generation and as-shown replay. No FRED fallback; service permissions cannot promote PERSONAL_USE corpus into product namespace.

**Не переносим / граница:** Не ODP Workspace/AI/recorder; no broad proxy, no vendor keys in client, no AGPL copy into private core. HTTP boundary не юридический safe harbor.

**Источник:** `https://github.com/OpenBB-finance/OpenBB/tree/3e071fcc2cd9f891cac6040ae60296dba76dab46`. **Source pin:** `3e071fcc2cd9f891cac6040ae60296dba76dab46`; для ETAPE/OPENBB это выбранная ревизия исходников, для прочих — provenance; не installed package lock. **Проверка:** `SOURCE_RECHECKED_2026_09_27_RUNTIME_NOT_RUN`.

**Предлагаемые локальные места из действующих карточек** (не подтверждённый code checkout):

| Issue / модуль | Пути |
|---|---|
| [ODX-WKS-020](#issue-ODX-WKS-020) / `WKS-SCN` | `packages/market-data-client/external-provider.ts`; `packages/client-runtime/data-bindings.ts`; `packages/views-reference/ReferenceRatesPanel.tsx`; `tests/external-data-preview.spec.ts` |
| [ODX-WKS-021](#issue-ODX-WKS-021) / `WKS-VIEW` | `packages/views-options/ExternalChainPanel.tsx`; `packages/views-options/StrikeInspector.tsx`; `tests/external-chain-preview.spec.ts` |

### Сервисы и datasets: прямые, общие и отложенные подключения

Только direct Issue означает scope конкретного подключения. Related/generic — класс работ, не выбранный provider. Future/deferred строки не надо реализовывать автоматически; они сохранены, чтобы отдельный repo не потерял архитектурный замысел.

| Источник | Покрытие | Прямые задачи | Связанный общий scope | Решение/условие |
|---|---|---|---|---|

### Унаследованные варианты — не дополнительные зависимости

Связь по source group не доказывает выбор каждого пакета. Ниже сохранены reference-кандидаты, связанные с локальными sources. Более старые решения могут расходиться с текущим scope; не разрешать их молча.

| Компонент | Исторический статус | Роль/граница |
|---|---|---|
| [React](https://github.com/facebook/react) + TypeScript | `ADOPT` | Presentation/composition baseline собственной web-native Workstation; React components не владеют domain state; shell semantics принадлежат `WKS-*` |
| [TanStack Query](https://github.com/TanStack/query) | `ADOPT-LIMITED` | Server-state cache, retries и invalidation в `WKS-APP`; Не становится event/domain store; mutations идут только через application commands |
| [CodeMirror](https://github.com/codemirror/dev) | `ADOPT-LIMITED` | Редактор config-as-code, predicates и позднее research specs; Только editor adapter; не extension runtime и не command owner |
| [TradingView Lightweight Charts](https://github.com/tradingview/lightweight-charts) | `ADOPT-LIMITED` | Candles, series, axes и low-rate overlays; `MarketChartRendererPort`; не heatmap/footprint и не owner Scene/selection |
| [Dockview](https://github.com/dockview/dockview) core | `SPIKE` | IDE-like dockable/persistent panes; Только OSS core; adopt после OS-02 serialization/keyboard/popout/8h test |
| [PixiJS](https://github.com/pixijs/pixijs) | `SPIKE` | GPU-capable 2D heatmap/footprint/high-rate overlay; Сравнить с thin custom WebGL2 renderer; WebGL2 baseline, WebGPU progressive |
| Electron / Tauri | `SPIKE` | Desktop delivery одной web-native codebase; ADR-003 после 8h session, GPU, memory, multi-window и OS matrix |
| [Pi](https://github.com/earendil-works/pi) | `FORK / OWNED FOUNDATION` | Весь Odelix Harness: runtime, skills/hooks/tools/teams/rules, Critic, memory, Missions, traces and evals; Exact upstream commit + patch digest; no external wrapper; Product/Market domain schemas remain producer-owned |
| Pi provider packages / direct provider SDKs | `FORK FOUNDATION / ADOPT-LIMITED` | Model/provider access внутри Pi fork; HarnessVersion pin, Product secret binding, one provider path per run |

### Ранний внешний data layer (генерируемый scope)

OpenBB ODP SELECTED_FOR_IMPLEMENTATION, runtime NOT_RUN; в этом repo применяются только его обязанности. Native flow/replay и права не подменяются внешним snapshot. Полный normative contract принадлежит Market; consumer получает producer artifact.

| Profile | Mode | Доказанная граница / ограничения | Issues |
|---|---|---|---|
| ODP-DERIBIT-BARS | HISTORICAL_POLL | explicit date range; no full-history default; source precision preserved, not exact exchange reconstruction; no aggression/price-bin data | `ODX-MKT-091` |
| ODP-ECB-REFERENCE | DATED_CURRENT_SNAPSHOT | daily.xml, not historical series; not executable intraday FX price; observation date not exact publication timestamp | `ODX-MKT-091` |
| ODP-DERIBIT-CHAIN | COMPOSITE_CURRENT_SNAPSHOT | one connection per expiry in upstream, require measured fanout cap; 2s per expiry receive timeout; partial/exceptions not complete proof; BTC/ETH price conversion to USD and round(2); IV percent divided by 100; New York aware row timestamps normalized to UTC preserving instant; contract_size=1 and today-derived DTE are not verified terms; date-only expiry and current universe do not supply historical PIT | `ODX-MKT-037`, `ODX-WKS-021` |
| ODP-FRED-REVISED-PREVIEW | DISABLED_API_PROFILE_NOT_PERSONAL_FILE_ROUTE | Not installed/selected as active ODP fallback; FRED general restrictions apply beyond API: personal download not corpus storage; Use separate bounded personal file policy; no automated ODP/ML route | `ODX-MKT-090`, `ODX-MKT-042`, `ODX-MKT-043`, `ODX-MKT-044` |

Cache/replay policy: source+model+canonical instrument+query+normalization version+rights credential partition; reauthorize on hit; no silent provider fallback. stored OpenBB response plus versioned deterministic normalization, not upstream event replay; no vendor call during saved replay. Public activation: requires actual Product grants, source/AGPL/data rights and operational acceptance; local preview is not public authorization.

### Выбранная UI-основа и исторические альтернативы

[Точный выбранный scope и порядок переноса](FOUNDATION-DECISION.md#selected-ui-scope). eTape UI ET-01..07 — SELECTED_FOR_IMPLEMENTATION; source intake WKS-018. [Machine-readable import plan](../delivery/ETAPE-UI-IMPORT.json). Theia/Open MCT/EdgeDepth — исторические альтернативы, не повторный конкурс. Runtime/build/стоимость остаются непроверенными.


**Приёмка внешнего компонента:** existing-code check → точный package/commit и license/NOTICE/dependencies → adapter test с недоступностью/ошибками/качеством → фактический pin и результат. Не создавать второй runtime/численный engine под видом ускорения. Текст этого блока не устанавливает packages, не покупает сервисы и не выдаёт production rights.
<!-- END GENERATED IMPLEMENTATION BASIS -->

<a id="architecture"></a>
## Архитектура


<a id="уточнение-r151-scalable-read--не-поздняя-оптимизация"></a>
### Scalable read — не поздняя оптимизация

**21 сентября 2026 · PROPOSED; source/runtime status не перепроверен этой сборкой.** При изменении порядка ранних работ действует [план live-read](#DEP-13674f89c9) и [Phase-A requirements](#DEP-686a4a4cec). Рынок не принадлежит подписке: P0 truthfulness + cold guard, затем S1a contracts, S2 shared producer/local fan-out, S3 instrument core и S4 blocks. Ранний F — ограниченный pilot после P0/S2; STK-013 отдельно принимает workload-specific scale. Ни F, ни каталог файлов не доказывают 100k users. Данные/footprint/depth не переписывать заново; WKS продолжает fixture/runtime integration по готовым границам, не ждёт всего распределённого deployment.

Окна пользователя не key вычисления; 1s — только исторические aggregates, не запрет внутрисекундной книги/ленты. NoChange/Replace(empty)/Patch/Invalidated различаются. Историческая ликвидность сохраняется, текущая удаляется по выбранной семантике. Стабильный digest и state/meaning/wire versions с golden tests; converter optional, controlled prewarm обязателен. Все live updates и rollback остаются без публичного full replay. E/семантика потребляют общие instrument features; Product выдаёт grants, не становится прокси всех ticks.


This repo is the owned replacement for the Emacs runtime idea: addressable
objects, total command registry, durable workspace, panes, keyboard completeness,
config-as-code and agent-addressable context, implemented web-native.

```text
packages/
  application-client   generated Market/Product/Harness clients and sessions
  object-navigation    stable object routes/history/focus
  command-runtime      named commands, keymaps, capability-aware invocation
  pane-runtime         view lifecycle/focus/container semantics
  layout-runtime       durable layouts/recovery/migrations
  scene-interaction    chart/heatmap/footprint/selection/layers
  config-runtime       preferences/keymaps/config-as-code
  sdk                  supported exports for odelix-web/plugins
apps/
  desktop              native host/bridge/packaging/updater
```

Baseline: thin owned React/TypeScript shell. Dockview, Lightweight Charts,
PixiJS/WebGL2, TanStack Query and Electron are adapters, not domain foundations.
Theia is a bounded challenger through ADR-003 only.

Agent panes render `AgentSession/Run/HarnessEvent` from the maintained Pi fork;
they use a sanitized embedded Pi client/Connect bridge and do not host private
Skills/Critic/evals or a second agent loop.

An early flow Workstation is built in parallel with Market/Product before paid Connect. Options data capture starts early; linked options views follow data readiness. Its shared
Scene contracts and local/pilot views proceed on their own technical gates. Commercial activation is separate and does not block their implementation.

The Web application imports versioned `sdk`/runtime packages, but owns Web
routing, public/share/mobile, onboarding and billing composition in its repo.


### Options и research integration (PROPOSED)

Retain command/object/pane/Scene runtime. Options views are shared compositions (chain, smile, payoff, scenarios, policy inspector), not financial owners. Full terminal/Pi distribution is no longer required before narrow Desk/PAPER.

Detailed scope: [OPTIONS-ANALYTICS.md](#DEP-037669e492).


### Research/strategy capability requirements

Scenes, stable objects/commands, linked replay и research panels. Основные owners: WKS-APP/OBJ/CMD/VIEW/LYT/SCN/CFG/EXT.

Новая поверхность использует эти же contracts и semantic fixtures; численные/торговые engines не копируются в UI или prompts. MODULE описывает actual/planned paths раздельно.

### Полная рабочая спецификация r12

[WORKSTATION-SPEC.md](WORKSTATION-SPEC.md) — адресуемые views, весь исходный каталог, panes/workspaces/lenses, Commands/Selection/ChangeSet, Options, Research, Agents, Scene/recovery и acceptance. Раннее «Desk только после оплаты» не запрещает минимальный context/result viewer на C1/I1; полный Workstation остаётся later gate.


### Workstation-first r15: текущая приёмка

Начать shared browser workbench сразу: chart cluster, footprint, depth, health, selection и replay. Raw/semantic/combined — слои одного объекта; full Desktop позже. Исходный 72-view каталог сохраняется.

Каноническая детализация: [SEMANTIC-FLOW-UX.md](WORKSTATION-SPEC.md#semantic-flow-ux); [current delivery](#DEP-b335630551).


<a id="contracts"></a>
## Контракты

**Статус этой сборки:** перечисленные семейства — спецификация. Заголовок «Published» в унаследованном тексте означает целевую поверхность публикации, не доказательство существующего release. Реальный pin/digest и conformance требуются до integration acceptance.


<a id="уточнение-r151-scalable-read--не-поздняя-оптимизация-1"></a>
### Scalable read — не поздняя оптимизация

**21 сентября 2026 · PROPOSED; source/runtime status не перепроверен этой сборкой.** При изменении порядка ранних работ действует [план live-read](#DEP-13674f89c9) и [Phase-A requirements](#DEP-686a4a4cec). Рынок не принадлежит подписке: P0 truthfulness + cold guard, затем S1a contracts, S2 shared producer/local fan-out, S3 instrument core и S4 blocks. Ранний F — ограниченный pilot после P0/S2; STK-013 отдельно принимает workload-specific scale. Ни F, ни каталог файлов не доказывают 100k users. Данные/footprint/depth не переписывать заново; WKS продолжает fixture/runtime integration по готовым границам, не ждёт всего распределённого deployment.


Consumes pinned Market Scene/Replay, Product API and Pi Harness event clients.
Publishes versioned
TypeScript packages and JSON schemas for ObjectRef, CommandDescriptor,
WorkspaceSnapshot, Pane/View contract, Scene viewport/selection/layers and config.

The public SDK is the only supported integration for `odelix-web`. Internal
React stores/components are not contracts. A contract change requires shared
acceptance fixtures in Desktop and Web consumers.

Before SCENE_CONTRACT_READY, consumer fixtures may use a pinned DRAFT schema. FLOW_DESK_LOCAL does not publish a stable external SDK. Before FLOW_DESK_PILOT, published Scene semantics and SDK compatibility are frozen under SCENE_CONTRACT_READY. Query/Replay contracts freeze at QUERY_REPLAY_CONTRACT_READY before external pilot, independently of payment.

Harness UI consumes `AgentSessionRef/RunRequest/HarnessEvent/TerminalOutcome`
owned by `odelix-harness`. Start/cancel/follow-up goes through Product
transport/capability context; Workstation never imports Pi internals.
`ODELIX_PROCEDURE_VERIFIED` is displayed only from a Harness-signed artifact; local
composition is labelled separately.


### Options и research integration (PROPOSED)

Stable instrument/selection/candidate/quote refs and typed provenance. Calculations shown by client never override Market/Execution. Options-specific components are reused by Web through published SDK rather than source copying.

Detailed scope: [OPTIONS-ANALYTICS.md](#DEP-037669e492).


### Research/strategy capability requirements


Общие StrategySpec/ExperimentSpec — Product; calculation/data manifests — Market; agent handoff — Harness; order/policy — Execution. Schema producer owns source, Stack registry discovers it. DRAFT entries не считаются published; copy/paste бизнес-типов между repos запрещён.

### Восстановленный UI contract

View/Workspace/Selection/Command/ChangeSet и stream/replay semantics раскрыты в [WORKSTATION-SPEC.md](WORKSTATION-SPEC.md). Proposed view IDs не объявлены опубликованными schemas. Общая boundary — [Contracts and Integration](#DEP-55b72d67f4).


<a id="modules"></a>
## Модули


| ID | Owns | Foundation |
|---|---|---|
| WKS-APP | sessions/generated clients/client projection cache | React + TanStack adapter |
| WKS-OBJ | object routes/history/focus | owned model |
| WKS-CMD | command registry/keybindings/invocation | owned model |
| WKS-PANE | pane/view lifecycle/focus | Dockview adapter |
| WKS-LYT | layout schema/persistence/recovery | owned schema + Dockview |
| WKS-SCN | viewport/layers/selection/synchronized cursors | LWC + Pixi/WebGL2 |
| WKS-CFG | preferences/keymaps/config-as-code | owned schema; editor later |
| DESKTOP | native bridge/package/updater | Electron provisional |

Scene is the difficult differentiated module. Docking/commands/preferences are
commodity mechanics but Odelix owns their meaning and contracts.


Повторяющийся блок сохранён один раз: [см. раздел](#architecture).


<a id="testing"></a>
## Проверка


<a id="уточнение-r151-scalable-read--не-поздняя-оптимизация-2"></a>
### Scalable read — не поздняя оптимизация

**21 сентября 2026 · PROPOSED; source/runtime status не перепроверен этой сборкой.** При изменении порядка ранних работ действует [план live-read](#DEP-13674f89c9) и [Phase-A requirements](#DEP-686a4a4cec). Рынок не принадлежит подписке: P0 truthfulness + cold guard, затем S1a contracts, S2 shared producer/local fan-out, S3 instrument core и S4 blocks. Ранний F — ограниченный pilot после P0/S2; STK-013 отдельно принимает workload-specific scale. Ни F, ни каталог файлов не доказывают 100k users. Данные/footprint/depth не переписывать заново; WKS продолжает fixture/runtime integration по готовым границам, не ждёт всего распределённого deployment.


- shared host-independent acceptance slice for commands/objects/layout/Scene;
- 100 restart/crash-recovery cycles with stable object identity;
- 8-hour synthetic/live stream soak: FPS, heap growth, dropped/resync frames;
- focus/keymap/pane lifecycle and layout migration tests;
- Scene live/replay frame equivalence and selection precision;
- Web consumer compatibility against published SDK;
- desktop cold start, bundle, updater and GPU context-loss recovery.


### Options и research integration (PROPOSED)

Test selection→objective context integrity, live/replay/expiry identity, residual-risk labels, focus/keyboard, package compatibility and no accidental trading capabilities in research build.

Detailed scope: [OPTIONS-ANALYTICS.md](#DEP-037669e492).


### Research/strategy capability requirements


Meaningful acceptance scenarios: viewport consistency, layout recovery, shared asOf, stable object refs, backpressure and long-session recovery. Здесь перечислены planned tests; executed results должны ссылаться на commit/CI.

### Workstation acceptance r12

Набор WS-01…WS-15 и границы фактически выполненной документальной проверки находятся в [WORKSTATION-SPEC.md, §15](WORKSTATION-SPEC.md). Runtime soak, crash/reconnect и privacy checks требуют реального build.


<a id="runbook"></a>
## Выпуск и эксплуатация


Pin Market/Product client and SDK versions. Run shared acceptance in Desktop and
the Web consumer, long-session soak, layout migration and renderer recovery.
Never publish a runtime package from an uncommitted local sibling source. Keep
rollback installer and previous compatible `stack.lock` combination.
Artifact scanning must prove the Desktop build contains no private hosted
Skill/hook/agent/Critic/eval machinery.


### Options и research integration (PROPOSED)

Keep 8h/GPU/recovery and private-artifact scans. Desktop installation does not grant signing rights; explicit safe signer integration comes only with activated PAPER/LIVE controls.

Detailed scope: [OPTIONS-ANALYTICS.md](#DEP-037669e492).


### Research/strategy capability requirements


Начинать с inventory существующего кода/Issues. Глобальный порядок и A0 находятся в `DEP-9359295523` (см. локальный реестр зависимостей); Project metadata включает нормализованный Module согласно r15.3; дополнительные поля не добавляются автоматически. Не добавлять ручной второй статус-журнал.




<a id="early-external-data"></a>
<a id="ранняя-data-ветка-r158-реализация-и-владельцы"></a>
## Ранняя data-ветка: реализация и владельцы

[Workstation data UX](WORKSTATION-SPEC.md#external-data-ux) определяет per-layer source bindings, Inspector и сохранённый response replay. WKS-020/WKS-021 подключают наш MarketDataPort; импорт OpenBB Python/SDK, чужих DTO или provider keys в браузер запрещён. Это consumer внешнего профиля, не новый data backend.

Использовать выбранный eTape/Dockview host и готовую chart-механику. WKS-020 ждёт лишь contract/policy и UI-композицию; actual backend принимается STK-016. Не ждать native S3/S4/WKS-007 для HTTP snapshot. WKS-009 позже расширяет ранние option-компоненты capability-aware, а не создаёт вторую chain.

Native footprint/heatmap доступны только на native recorded/event inputs; external candles их не заменяют. External view может быть useful сейчас и остаться источником контекста позже.
<!-- R159 agent-views -->
## Decision timeline, Agent Registry и data coverage

WKS-022 — независимый read-only Flight Recorder над Product projection, без WKS-014. WKS-023 — минимальный Fleet/Approval client. WKS-024 — Builder над готовым AgentSpec/plan. WKS-010 сохраняет Memory Inspector/Guided Replay. Все используют выбранную eTape panel mechanics и один Dockview, не новую оболочку.

WKS-025 расширяет существующий data Inspector source/coverage/vintage/knownAt. Недоступные inputs и rights-pending видимы; hash-only не позволяет тайно скачать source. Общие ранние экраны не ждут новые агентные или full collection gates. Ключей/LLM/canonical market truth в React views нет.

<!-- BEGIN GENERATED REPO CONTEXT -->

<a id="modules-index"></a>
## Каталог модулей этой области

Один primary owner в каждой задаче; affected modules отдельно. AREA — служебная классификация. Идентификаторы сохранены. Activation — scope текущего плана, не доказательство исполнения; независимый implementation_status в JSON.

| ID | Вид | Activation | Источник в этом repo |
|---|---|---|---|
| `WKS-APP` | MODULE | ACTIVE | [docs/DEVELOPMENT.md](#modules) |
| `WKS-CFG` | MODULE | ACTIVE | [docs/DEVELOPMENT.md](#modules) |
| `WKS-CMD` | MODULE | ACTIVE | [docs/DEVELOPMENT.md](#modules) |
| `WKS-EXT` | MODULE | BOUNDARY_ONLY | [docs/WORKSTATION-SPEC.md](WORKSTATION-SPEC.md#runtime-modules) |
| `WKS-LYT` | MODULE | ACTIVE | [docs/DEVELOPMENT.md](#modules) |
| `WKS-OBJ` | MODULE | ACTIVE | [docs/DEVELOPMENT.md](#modules) |
| `WKS-PANE` | MODULE | ACTIVE | [docs/DEVELOPMENT.md](#modules) |
| `WKS-SCN` | MODULE | ACTIVE | [modules/scene-interaction/MODULE.md](../modules/scene-interaction/MODULE.md) |
| `WKS-VIEW` | MODULE | ACTIVE | [docs/WORKSTATION-SPEC.md](WORKSTATION-SPEC.md#runtime-modules) |

<a id="work-queue"></a>
## Локальная очередь и следующий шаг

Полный текст каждой задачи — в [delivery/CONTEXT.json](../delivery/CONTEXT.json), это автоматически полученная выборка, не второй редактируемый backlog. Найдите объект по `id`, прочитайте `work`, `acceptance`, `paths`, `depends_on`, `primary_module_id`.

Сначала действующий Issue, actual commit/permissions, затем следующее допустимое действие. Planned wave не статус и не мандат. Внешняя зависимость должна предоставить артефакт/fixture; соседний checkout не предполагается.

| ID | Модуль | Волна | Результат | Зависимости |
|---|---|---|---|---|
| <a id="issue-ODX-WKS-001"></a>`ODX-WKS-001` | `WKS-APP` | 1 | Создать shared client runtime: Objects, Commands, Scene | ODX-PRD-027, ODX-STK-001 |
| <a id="issue-ODX-WKS-002"></a>`ODX-WKS-002` | `WKS-SCN` | 1 | Подключить Lightweight Charts через RendererPort и SelectionRef | ODX-WKS-001, ODX-WKS-018 |
| <a id="issue-ODX-WKS-003"></a>`ODX-WKS-003` | `WKS-LYT` | 1 | Адаптировать eTape shell/link groups поверх Dockview core и нашего LayoutPort | ODX-WKS-001, ODX-WKS-018 |
| <a id="issue-ODX-WKS-004"></a>`ODX-WKS-004` | `WKS-CFG` | 4 | Подключить CodeMirror как общий SpecEditorPort | ODX-WKS-001, ODX-PRD-015 |
| <a id="issue-ODX-WKS-005"></a>`ODX-WKS-005` | `WKS-APP` | 7 | Выбрать один Desktop host по bounded Electron/Tauri spike | ODX-WKS-003, ODX-WEB-003 |
| <a id="issue-ODX-WKS-006"></a>`ODX-WKS-006` | `WKS-APP` | 7 | Реализовать thin Desktop host, update и session recovery | ODX-WKS-005, ODX-WKS-004 |
| <a id="issue-ODX-WKS-007"></a>`ODX-WKS-007` | `WKS-SCN` | 1 | Написать worker stream adapter и bounded render buffers | ODX-WKS-002, ODX-MKT-017, ODX-MKT-027 |
| <a id="issue-ODX-WKS-008"></a>`ODX-WKS-008` | `WKS-VIEW` | 1 | Подключить ранний bounded heatmap renderer к Workstation | ODX-WKS-007, ODX-MKT-027 |
| <a id="issue-ODX-WKS-009"></a>`ODX-WKS-009` | `WKS-VIEW` | 3 | Сделать Option Chain/Strike/Volatility views на typed contracts | ODX-WKS-007, ODX-MKT-024 |
| <a id="issue-ODX-WKS-010"></a>`ODX-WKS-010` | `WKS-VIEW` | 9 | Добавить Agent Console, Memory inspector и Guided Replay | ODX-WKS-009, ODX-HAR-013, ODX-PRD-026 |
| <a id="issue-ODX-WKS-011"></a>`ODX-WKS-011` | `WKS-APP` | 1 | Создать browser dev-host общей Workstation без Desktop-блокера | ODX-WKS-001, ODX-WKS-002, ODX-WKS-003 |
| <a id="issue-ODX-WKS-012"></a>`ODX-WKS-012` | `WKS-VIEW` | 1 | Отрисовать полноценный footprint через общий RendererPort | ODX-WKS-002, ODX-MKT-018 |
| <a id="issue-ODX-WKS-013"></a>`ODX-WKS-013` | `WKS-VIEW` | 2 | Добавить Semantic/Combined режим и Evidence inspector | ODX-WKS-001, ODX-PRD-028 |
| <a id="issue-ODX-WKS-014"></a>`ODX-WKS-014` | `WKS-SCN` | 3 | Сделать as-shown replay и явную повторную интерпретацию | ODX-WKS-013, ODX-PRD-028, ODX-MKT-022 |
| <a id="issue-ODX-WKS-015"></a>`ODX-WKS-015` | `WKS-VIEW` | 4 | Подключить Find Similar к MarketSimilarityPort | ODX-WKS-012, ODX-MKT-026 |
| <a id="issue-ODX-WKS-016"></a>`ODX-WKS-016` | `WKS-APP` | 1 | Подключить блочную историю, bounded cache и честный resync к Workstation | ODX-WKS-007, ODX-MKT-032 |
| <a id="issue-ODX-WKS-017"></a>`ODX-WKS-017` | `WKS-VIEW` | 1 | Собрать локальный footprint и Inspector на тех же общих WKS packages | ODX-WKS-011, ODX-MKT-035 |
| <a id="issue-ODX-WKS-018"></a>`ODX-WKS-018` | `WKS-APP` | 0 | Зафиксировать eTape UI subset, provenance и изолированную исходную сборку | ODX-STK-001 |
| <a id="issue-ODX-WKS-019"></a>`ODX-WKS-019` | `WKS-VIEW` | 1 | Перенести read-only DOM и Time & Sales eTape на Odelix render models | ODX-WKS-018, ODX-WKS-003, ODX-MKT-035 |
| <a id="issue-ODX-WKS-020"></a>`ODX-WKS-020` | `WKS-SCN` | 1 | Подключить внешний data preview, source bindings и snapshot Inspector | ODX-WKS-011, ODX-MKT-090, ODX-PRD-031 |
| <a id="issue-ODX-WKS-021"></a>`ODX-WKS-021` | `WKS-VIEW` | 1 | Показать раннюю внешнюю Option Chain и Strike Inspector без native-flow claims | ODX-WKS-020, ODX-MKT-037 |
| <a id="issue-ODX-WKS-022"></a>`ODX-WKS-022` | `WKS-VIEW` | 2 | Показать независимый Flight Recorder timeline и coverage | ODX-WKS-011, ODX-PRD-032, ODX-EXE-008 |
| <a id="issue-ODX-WKS-023"></a>`ODX-WKS-023` | `WKS-VIEW` | 3 | Добавить минимальные Fleet/Approval views для hosted работы | ODX-WKS-011, ODX-PRD-033, ODX-PRD-035, ODX-PRD-024 |
| <a id="issue-ODX-WKS-024"></a>`ODX-WKS-024` | `WKS-CFG` | 5 | Собрать Agent Builder над готовым AgentSpec и plan | ODX-WKS-023, ODX-PRD-033, ODX-EXE-009 |
| <a id="issue-ODX-WKS-025"></a>`ODX-WKS-025` | `WKS-VIEW` | 3 | Добавить Data Coverage и выбор версии макрорядов без потери контекста | ODX-WKS-020, ODX-MKT-046 |

<a id="dependencies"></a>
## Внешние зависимости и источники

**Реестр ниже не является очередью обязательного чтения.** Большинство записей — происхождение решений; артефакт запрашивается только для конкретной необходимой зависимости задачи. **Независимость checkout не означает отсутствие зависимостей продукта.** Здесь записаны логические координаты издателя, а не относительные переходы в соседнюю папку. `source_sha256` в локальном JSON удостоверяет только исходный документ r15.3. Ни одна строка не является доказательством выпуска schema/SDK.

Для конкретной задачи получить через TaskPacket или разрешённый артефактный канал: producer + contract ID, точный release/commit, schema/package digest, fixtures, consumer-conformance и разрешённые операции. Записать фактический локальный путь после получения; пока artifact не предоставлен, зависимая runtime-работа не готова. Локальные fixture/design-задачи возможны по своему мандату.

Не заменять чужую схему её ручной копией. Обновление версии — producer release → consumer pin → conformance → интеграция. Разрешение читать артефакт не даёт права изменять другой repo.

| Ref | Publisher | Логический источник | Статус исходника |
|---|---|---|---|
| <a id="DEP-037669e492"></a>`DEP-037669e492` | `odelix-market` | `docs/OPTIONS-ANALYTICS.md` | DOCUMENT_SNAPSHOT_ONLY |
| <a id="DEP-13674f89c9"></a>`DEP-13674f89c9` | `odelix-market` | `docs/PHASE-A-REQUIREMENTS.md#scalable-read-programme` | DOCUMENT_SNAPSHOT_ONLY |
| <a id="DEP-143d61eff7"></a>`DEP-143d61eff7` | `odelix-stack` | `reference/OSS-REVIEW-RU.md` | DOCUMENT_SNAPSHOT_ONLY |
| <a id="DEP-392b062cdc"></a>`DEP-392b062cdc` | `odelix-web` | `docs/PRODUCT-SURFACE.md#prototype-migration` | DOCUMENT_SNAPSHOT_ONLY |
| <a id="DEP-4cc69c028e"></a>`DEP-4cc69c028e` | `odelix-stack` | `docs/DELIVERY-ROADMAP.md` | DOCUMENT_SNAPSHOT_ONLY |
| <a id="DEP-52ea262d35"></a>`DEP-52ea262d35` | `odelix-stack` | `docs/INTEGRATIONS.md#code-reuse` | DOCUMENT_SNAPSHOT_ONLY |
| <a id="DEP-55b72d67f4"></a>`DEP-55b72d67f4` | `odelix-stack` | `docs/05-CONTRACTS-AND-INTEGRATION.md` | DOCUMENT_SNAPSHOT_ONLY |
| <a id="DEP-686a4a4cec"></a>`DEP-686a4a4cec` | `odelix-market` | `docs/PHASE-A-REQUIREMENTS.md` | DOCUMENT_SNAPSHOT_ONLY |
| <a id="DEP-9359295523"></a>`DEP-9359295523` | `odelix-stack` | `docs/DELIVERY-RUNBOOK.md` | DOCUMENT_SNAPSHOT_ONLY |
| <a id="DEP-a43d217e08"></a>`DEP-a43d217e08` | `odelix-stack` | `docs/PROJECT.md` | DOCUMENT_SNAPSHOT_ONLY |
| <a id="DEP-b18ccf612f"></a>`DEP-b18ccf612f` | `odelix-product` | `docs/STRATEGY-RESEARCH-AND-EXECUTION.md` | DOCUMENT_SNAPSHOT_ONLY |
| <a id="DEP-b335630551"></a>`DEP-b335630551` | `odelix-stack` | `AGENTS.md` | DOCUMENT_SNAPSHOT_ONLY |
| <a id="DEP-be1ad84d43"></a>`DEP-be1ad84d43` | `odelix-web` | `docs/PRODUCT-SURFACE.md` | DOCUMENT_SNAPSHOT_ONLY |
| <a id="DEP-cb2ad81050"></a>`DEP-cb2ad81050` | `odelix-stack` | `docs/DELIVERY-RUNBOOK.md#approval-and-admission` | DOCUMENT_SNAPSHOT_ONLY |
| <a id="DEP-f5aa8c7a14"></a>`DEP-f5aa8c7a14` | `odelix-web` | `docs/PRODUCT-SURFACE.md#prototype-migration` | DOCUMENT_SNAPSHOT_ONLY |

`UNRESOLVED_SOURCE_REFERENCE` — отсутствующий документ/якорь исходного пакета явно зарегистрирован; содержание не придумано. Для historical source его можно оставить архивной ссылкой, для обязательной зависимости — запросить источник. Public URLs в предметных документах сохранены как датированные ссылки и не проверялись онлайн этой сборкой.
