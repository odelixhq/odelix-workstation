# Odelix Workstation — полная спецификация рабочей среды

> **Текущая редакция документа: r15.11, 28 сентября 2026.** Добавлен ранний external-data scope; исходные исторические срезы ниже датированы отдельно. Изменение спецификации не является выполнением runtime/источниковой приёмки.

> **Основа и reuse текущей редакции:** [локальная карта внешних компонентов и адаптеров](DEVELOPMENT.md#implementation-basis). Там указаны источник, режим использования, наши модули/пути, задачи и ограничения. Кандидат не установленная зависимость; этот предметный документ не требует реализации библиотечной механики с нуля.


> Текущий пакет r15.11 · 28.09.2026. Независимая версия предметной спецификации и датированное происхождение указаны ниже. Нормативные gates — локальный CONTEXT; старые source measurements не текущие замеры.

**Уточнение scope Research: r12.1 · 2026-09-18.** Пользовательские примеры не определяют встроенную стратегию или обязательный dataset; статус реализации не меняется.

**Редакция документа:** r15.11 · 2026-09-28 · PROPOSED. Исходная версия сохранена в истории; runtime не подтверждён.
**Статус:** восстановленная и согласованная целевая спецификация, не описание готовой реализации.
**Owner:** `odelix-workstation`; данные и business objects остаются у Market/Product/Harness/Execution.

<a id="first-release-scope"></a>
## Объём первого результата: local, pilot и целевой каталог

**r15.11 · proposed scope, не результат испытания.** 72 views / 12 panel roles / 15 workspaces / 12 lenses — целевой каталог. Количество шагов сравнительного shell-spike не определяет число views релиза.

| Уровень | Обязательный сценарий | Явно не является prerequisite |
|---|---|---|
| FLOW_DESK_LOCAL / ODX-STK-014 | Общий browser dev-host; raw candle и footprint из ограниченного recorded sample; CVD/качество; time/price selection → Inspector; сохранение локального cursor/selection и replay/rewind | OIDC/Fastify production, первый платёж, Jev, Deribit, Desktop, repo transfer. Только loopback/изолированная среда, без нагрузки на production |
| FLOW_DESK_PILOT / ODX-STK-101 | Тот же client/runtime; real shared snapshot/stream, DOM и Time & Sales (WKS-019), footprint, bounded heatmap, CVD/depth/profile по гарантированным данным, Selection/Inspector/reconnect/replay, Product grants и оба P0 | Все 72 views, forecast, полный Options, billing и распределённый deployment. Пилот не доказывает 100k users |
| PRO_WORKSTATION | Целевой каталог и профессиональная глубина активируются по конкретным Issues, quality и load gates | Наличие строк каталога само по себе не активирует модуль или внешний сервис |

MKT-035 публикует producer fixtures и ограниченный replay sample; WKS-017 использует те же пути renderer, что WKS-012. Нет второго demo-продукта и второй рыночной арифметики. Synthetic-only демонстрация может показать UI, но не закрывает требование recorded sample для FLOW_DESK_LOCAL. Отсутствие auth в локальной вехе не разрешает анонимный production endpoint.


## 1. Назначение и основание восстановления

Workstation — программируемая рабочая среда трейдера, а не набор графиков с отдельным чат-виджетом. Она сохраняет исследовательский контекст: какие инструменты и интервалы открыты, какие объекты выделены, что подтверждает или опровергает гипотезу, какой эксперимент запущен и какое изменение предложил агент. Человек, command palette и подключённый агент работают с теми же адресуемыми объектами и разрешёнными командами.

Восстановлены предметные возможности ранней Emacs workstation и web master specification. **Emacs, Doom, Elisp, обязательный отдельный daemon и ранний monorepo не возвращаются.** Действующая основа — собственный тонкий React/TypeScript runtime с библиотеками за adapters. Спецификация не объявляет все её представления частью первого релиза.

Источники: [ранняя полная workstation — архивный источник](DEVELOPMENT.md#DEP-cb2ad81050), [ревизия v2 — архивный источник](DEVELOPMENT.md#DEP-cb2ad81050), [web master — архивный источник](DEVELOPMENT.md#DEP-cb2ad81050), [актуальное описание](DEVELOPMENT.md#DEP-a43d217e08), [foundation analysis](FOUNDATION-DECISION.md). Исторические технологические оценки имеют даты исходников; эта редакция не перепроверяет их как сегодняшние свойства сторонних продуктов.

## 2. Что сохраняется при смене платформы

| Исходная идея | Актуальная реализация | Что не переносится |
|---|---|---|
| Buffer с собственным контекстом | `ViewInstance` с object/query binding, selection, focus и revision | Emacs buffer как обязательный runtime |
| Major mode | View definition: renderer adapter, commands, typed context и permissions | Elisp и mode-specific бизнес-логика |
| Window/pane | Pane role + layout binding | Число окон как число сервисов |
| Command/keymap | Единый named Command Registry, keyboard/mouse/agent adapters | Скрытые обходные команды AI |
| Saved workspace | Versioned Workspace с layout, object refs, lenses и time domain | Сохранение только геометрии окон |
| Pi pane | Typed session/events UI + thin embedded/local client по потребности | Vterm-скрейпинг или private Skills на клиенте |
| SVG/GPU tiers | React/DOM для документов и таблиц; chart/GPU adapters для визуализации | Обязательный native sidecar до измерения |
| Внешняя память | Product-owned objects, decisions и consent; поиск по refs | Бесконтрольный transcript как финансовая истина |
| Полный терминал | Постепенное расширение общего client runtime | Завершение всех views/Desktop как обязательное условие первого рабочего экрана |

Имена `view.*` ниже — предложенные идентификаторы миграции, не опубликованный API. Их семантика выведена из исходных major modes; реальный выпуск требует schema/fixtures и producer release.

## 3. Объекты и владельцы

| Объект | Что хранит | Authority |
|---|---|---|
| Workspace | Задача, bindings, layout, focus, lenses, privacy и ссылки на исследования | Product Investigation; WKS управляет клиентской проекцией |
| ViewInstance | Вид, параметры query, независимый viewport/selection, reference на объект | WKS для представления; сам объект — его domain owner |
| Pane/Layout | Геометрия, группа вкладок, видимость и focus | WKS; persisted Workspace через owner API |
| SceneSnapshot | Зафиксированный состав сцены, market scope, clock и source refs | Product Investigation; Market — series/frames |
| SelectionRef | Instrument, time/price bounds, objects, layers, revisions и asOf | Context/Scene contract |
| Lens | Смысловой фокус; нужные поля/overlays/default commands | Versioned definition; не новая market truth |
| Evidence/Claim/Thesis/Watch | Доказательства, утверждения, версии гипотез и условия | Product Investigation |
| AgentSession/Run/Result | Ход процедуры, events, evidence и итог | Pi Harness; durable placement/usage у Product |
| Experiment/StrategyVersion | Спецификация, trials, validation и версии | Product Research; численный compute — Market |
| Position/Order/Fill | Реальное или simulated торговое состояние | Execution либо reconciled external authority |
| Portfolio/Sleeve | Учёт капитала, mandate, allocation и attribution | Product Capital, не client state |

Панель может показывать данные нескольких owners, но не становится владельцем их состояния. Смена layout, renderer или внешнего агента не меняет object IDs.

## 4. Информационная архитектура без перегруза

Default workspace показывает один Primary Canvas, одну AI/Context-панель, компактную строку состояния и Command Bar. Evidence, Chain, Footprint, Journal и Inspector открываются по задаче. Каталог из 72 исходных типов views — возможность открыть нужный объект, а не 72 одновременно видимые вкладки.

Одна primary lens и не более двух supplemental lenses — исходное правило композиции. Risk/invalidation/status overlays имеют приоритет, но не скрывают выбранный пользователем контекст. Агент предлагает изменения через preview; пользователь может отклонить их. Свёрнутая панель сохраняет binding, но перестаёт требовать ненужный high-rate render.

Workspace manager умеет сохранить, клонировать, восстановить, экспортировать и сбросить layout отдельно от исследований. Сброс layout не удаляет Thesis, Trial Ledger или торговую историю. Saved view хранит instrument scope, временную область, lens, visible layers, query parameters, object refs и режим LIVE/REPLAY/PAPER, а не только размеры окон.

## 5. Каталог исходных 72 представлений

В таблице сохранены содержание и разбиение исходного каталога. Новые Options/Strategy/Capital-представления из более поздних документов дополняют его в §§9–12. Каталог задаёт target capability, не readiness.

| Proposed View ID | Исходный mode | Группа | Содержание |
|---|---|---|---|
| `view.market-dashboard` | `market-dashboard-mode` | Global/attention buffers | regime delta, watchlist, risk strip, Thesis deltas |
| `view.attention-queue` | `attention-queue-mode` | Global/attention buffers | material notifications, wakes, stale evidence |
| `view.notification-detail` | `notification-detail-mode` | Global/attention buffers | trigger, related objects, reason |
| `view.data-health` | `data-health-mode` | Global/attention buffers | feed freshness, gaps, venue quality |
| `view.calendar-catalyst` | `calendar-catalyst-mode` | Global/attention buffers | expiries, macro/news calendar |
| `view.market-state` | `market-state-mode` | Market and visualization buffers | canonical snapshot, freshness, provenance |
| `view.market-chart` | `market-chart-mode` | Market and visualization buffers | price, volume, levels, evidence, scenarios |
| `view.market-profile` | `market-profile-mode` | Market and visualization buffers | volume/TPO/liquidity profile |
| `view.market-comparison` | `market-comparison-mode` | Market and visualization buffers | synchronized instruments/venues |
| `view.market-event` | `market-event-mode` | Market and visualization buffers | material event detail |
| `view.annotation-ledger` | `annotation-ledger-mode` | Market and visualization buffers | drawings with authors/provenance |
| `view.footprint` | `footprint-mode` | Order flow and liquidity buffers | bid/ask volume, imbalance, delta |
| `view.liquidity-heatmap` | `liquidity-heatmap-mode` | Order flow and liquidity buffers | historical depth/liquidity migration |
| `view.dom` | `dom-mode` | Order flow and liquidity buffers | aggregated depth and queue context |
| `view.tape` | `tape-mode` | Order flow and liquidity buffers | filtered trades, blocks, liquidations |
| `view.cvd` | `cvd-mode` | Order flow and liquidity buffers | venue/side cumulative delta |
| `view.flow-comparison` | `flow-comparison-mode` | Order flow and liquidity buffers | spot/perp and venue contributions |
| `view.liquidations` | `liquidations-mode` | Order flow and liquidity buffers | clusters, cascades, provenance |
| `view.perpetuals-state` | `perpetuals-state-mode` | Perpetuals and derivatives buffers | OI, funding, basis, leverage state |
| `view.oi-map` | `oi-map-mode` | Perpetuals and derivatives buffers | OI across venues/instruments |
| `view.basis` | `basis-mode` | Perpetuals and derivatives buffers | curve, annualized basis, venue spread |
| `view.funding` | `funding-mode` | Perpetuals and derivatives buffers | current/forecast/history |
| `view.leverage-events` | `leverage-events-mode` | Perpetuals and derivatives buffers | OI jumps, deleveraging, liquidation regimes |
| `view.options-overview` | `options-overview-mode` | Options buffers | expiry map, IV regime, skew, flow summary |
| `view.options-chain` | `options-chain-mode` | Options buffers | bid/ask/IV/OI/volume/Greeks |
| `view.options-surface` | `options-surface-mode` | Options buffers | vol surface, term, skew/smile |
| `view.options-flow` | `options-flow-mode` | Options buffers | trades, blocks, direction assumptions |
| `view.options-exposure` | `options-exposure-mode` | Options buffers | scenario bands, assumptions, sensitivities |
| `view.expiry` | `expiry-mode` | Options buffers | strikes, pin/settlement context, timeline |
| `view.investigation` | `investigation-mode` | Evidence and reasoning buffers | question, scope, runs, outcome |
| `view.evidence-ledger` | `evidence-ledger-mode` | Evidence and reasoning buffers | evidence for/against, freshness, provenance |
| `view.evidence-detail` | `evidence-detail-mode` | Evidence and reasoning buffers | payload/method/source/links |
| `view.claim-ledger` | `claim-ledger-mode` | Evidence and reasoning buffers | observed/derived/inferred/assumed claims |
| `view.claim-detail` | `claim-detail-mode` | Evidence and reasoning buffers | support, objections, revisions |
| `view.critic` | `critic-mode` | Evidence and reasoning buffers | alternatives, missing evidence, verdict |
| `view.source-browser` | `source-browser-mode` | Evidence and reasoning buffers | citations/data provenance, not generic web browser |
| `view.thesis` | `thesis-mode` | Thesis, scenario and attention buffers | summary, causal graph, lifecycle, invalidations |
| `view.thesis-graph` | `thesis-graph-mode` | Thesis, scenario and attention buffers | nodes/edges/evidence relations |
| `view.thesis-diff` | `thesis-diff-mode` | Thesis, scenario and attention buffers | exact semantic changes |
| `view.scenario` | `scenario-mode` | Thesis, scenario and attention buffers | conditions, path, signals, implications |
| `view.scenario-compare` | `scenario-compare-mode` | Thesis, scenario and attention buffers | side-by-side discriminating evidence |
| `view.watcher` | `watcher-mode` | Thesis, scenario and attention buffers | DSL, preview, triggers, notification policy |
| `view.watcher-list` | `watcher-list-mode` | Thesis, scenario and attention buffers | lifecycle, next evaluation, last wake |
| `view.mission` | `mission-mode` | Thesis, scenario and attention buffers | objective, state, budget, checkpoints |
| `view.mission-list` | `mission-list-mode` | Thesis, scenario and attention buffers | portfolio of background work |
| `view.trade-agent` | `trade-agent-mode` | Agent and run buffers | local session, input, answer, structured cards |
| `view.agent-run` | `agent-run-mode` | Agent and run buffers | plan, tools, workers, budgets, outcome |
| `view.agent-tool` | `agent-tool-mode` | Agent and run buffers | typed tool artifact with freshness |
| `view.agent-sessions` | `agent-sessions-mode` | Agent and run buffers | resume/search/archive sessions |
| `view.workspace-diff` | `workspace-diff-mode` | Agent and run buffers | proposed panes/lenses/annotations/actions |
| `view.memory-inbox` | `memory-inbox-mode` | Agent and run buffers | proposed durable learnings |
| `view.portfolio` | `portfolio-mode` | Portfolio, risk and execution buffers | equity, exposure, concentration, limits |
| `view.position-list` | `position-list-mode` | Portfolio, risk and execution buffers | positions, linked Thesis, risk |
| `view.position` | `position-mode` | Portfolio, risk and execution buffers | lifecycle, expected/observed path, guard |
| `view.risk` | `risk-mode` | Portfolio, risk and execution buffers | shocks, budgets, utilization, stale inputs |
| `view.proposal-list` | `proposal-list-mode` | Portfolio, risk and execution buffers | drafts, approvals, expiry |
| `view.proposal-review` | `proposal-review-mode` | Portfolio, risk and execution buffers | exact immutable proposal and risk diff |
| `view.order-intent` | `order-intent-mode` | Portfolio, risk and execution buffers | authorized action before execution |
| `view.orders` | `orders-mode` | Portfolio, risk and execution buffers | authoritative order/fill state |
| `view.kill-switch` | `kill-switch-mode` | Portfolio, risk and execution buffers | halt status and scoped controls |
| `view.replay` | `replay-mode` | Replay, review and learning buffers | clock, controls, state, hidden future |
| `view.episode-browser` | `episode-browser-mode` | Replay, review and learning buffers | cohorts, tags, difficulty, data quality |
| `view.decision-journal` | `decision-journal-mode` | Replay, review and learning buffers | research/decisions/overrides |
| `view.postmortem` | `postmortem-mode` | Replay, review and learning buffers | asOf decision quality and outcome |
| `view.playbook` | `playbook-mode` | Replay, review and learning buffers | user-owned rules and candidates |
| `view.eval-report` | `eval-report-mode` | Replay, review and learning buffers | scorecards, regressions, replay results |
| `view.provider-settings` | `provider-settings-mode` | Settings and extension buffers | BYOK providers, role routing, status |
| `view.capability-settings` | `capability-settings-mode` | Settings and extension buffers | tool/skill permissions |
| `view.skill-browser` | `skill-browser-mode` | Settings and extension buffers | local/community/premium catalogue |
| `view.connector` | `connector-mode` | Settings and extension buffers | data/broker/integration health |
| `view.workspace-manager` | `workspace-manager-mode` | Settings and extension buffers | save/clone/share/reset |
| `view.diagnostics` | `diagnostics-mode` | Settings and extension buffers | versions, logs with redaction, renderer status |

Полная машиночитаемая карта с номерами строк исходника: [WORKSTATION-VIEW-CATALOG.json](WORKSTATION-VIEW-CATALOG.json). На клиенте и сервере не создаются 72 отдельных сервиса или business modules. Один reusable inspector/table renderer может обслуживать несколько view types при сохранении их контрактов.

## 6. Двенадцать ролей панелей

| Pane role | Что обычно показывает | Обязательность |
|---|---|---|
| Primary Canvas | Chart/Heatmap/Replay/Options Surface | почти всегда |
| Context Pane | Market State, perpetuals, options overview | по задаче |
| Investigation Pane | Agent Session или Investigation | при ASK/research |
| Evidence Pane | Evidence/Claim Ledger/Critic | при проверке выводов |
| Thesis & Attention Pane | active Thesis, scenarios, watchers | компактно/раскрываемо |
| Flow Pane | Footprint/CVD/DOM/Tape | Flow lens/workspace |
| Options Pane | Chain/Flow/Exposure | Options lens/workspace |
| Portfolio/Risk Pane | positions, risk, proposal | Portfolio/pretrade |
| Inspector Pane | detail выбранного object | transient |
| Timeline Pane | events, replay clock, ThesisDelta | Replay/review |
| Command Dock | Command Bar, ASK input, parsed intent | глобально |
| Status/Risk Strip | data freshness, model/run, portfolio risk | глобально компактно |


## 7. Пятнадцать рабочих пространств

| Workspace | Primary pane/view | Supporting panes/views | Typical lens | Главная user story |
|---|---|---|---|---|
| Morning | `*Dashboard: Morning*` | Attention, Catalysts, Portfolio, Agent Brief | Regime | понять, что materially изменилось |
| Market | Chart | Market State, compact Thesis, Agent | Price | изучать instrument без перегруза |
| Level Investigation | Chart | Agent, Evidence/Claims, Flow on demand | Price + Flow | объяснить acceptance/rejection уровня |
| Flow | Footprint/Heatmap | DOM, Tape, CVD, Flow Compare, Agent | Flow/Liquidity | понять aggression vs price impact |
| Options | Options Surface/Overview | Chain, Flow, Exposure, Expiry, Agent | Options | понять vol/expiry/positioning context |
| Quant Lab | comparison/result scene | cohort table, run/eval, notebook/export | Quant | проверить base rate и robustness |
| Thesis | Thesis/Graph | Claims, Evidence, Scenarios, Watchers, Agent | Thesis | собрать и обновлять гипотезу |
| Missions | Mission list | Mission detail, Watchers, Attention, budget | Attention | управлять background research |
| Portfolio | Portfolio/Risk | Positions, Thesis, scenario shocks | Risk | видеть совокупный риск и связи |
| Pretrade | Proposal | Thesis, Evidence, Critic, Risk, Execution costs | Execution | решить approve/reject/no-trade |
| Position Guard | Position | Chart, ThesisDelta, Risk, Agent | Position | сопровождать причину, а не только PnL |
| Replay | Replay scene | Clock, Claims/Thesis, Agent Run, Journal | Replay | исследовать без look-ahead |
| Review | Journal/Postmortem | decision replay, evidence, playbook | Review | учиться без outcome bias |
| Operations | Data Health | Connectors, Missions, Positions, Safety | Operations | работать при incident/degradation |
| Custom | user-selected | user-selected within policy | any | собрать личный workflow |


`Attention` и `Operations` в графе Typical lens исходной таблицы обозначают смысловой фокус workspace, а не автоматически ещё две зарегистрированные lenses. Реестр lenses в следующем разделе содержит 12 элементов; добавление новой lens требует отдельной definition. `Custom` не расширяет разрешения пользователя.

## 8. Двенадцать lenses и контекстные команды

| Lens | Выделяет | Возможные panes/overlays |
|---|---|---|
| Price | structure, levels, volume, acceptance | Chart, profile |
| Flow | aggression, imbalance, absorption, CVD | Footprint, Tape, CVD |
| Liquidity | resting/replenishing/migrating depth | Heatmap, DOM |
| Perpetuals | OI, funding, basis, liquidations | Perpetuals, OI map |
| Options | IV, skew, term, flow, expiry scenarios | Surface, Chain, Exposure |
| Thesis | claims, scenarios, invalidations, evidence gaps | Thesis, Claims, Watchers |
| Position | expected vs observed path, PnL/risk | Position, Chart, Guard |
| Execution | spread, depth, slippage, venue risk | Cost/Risk panes |
| Replay | point-in-time clock, hidden future | Replay, Timeline |
| Regime | cross-asset/venue/material deltas | Dashboard, comparison |
| Quant | cohorts, distributions, sensitivity | Quant results |
| Review | decisions, overrides, outcome decomposition | Journal, Postmortem |


Lens меняет акценты сцены, supporting panes и набор контекстных действий, сохраняя instrument, viewport, cursor/asOf и selected objects. Indicator рассчитывает или показывает одну series; он не заменяет lens. Workspace сохраняет задачу и расположение; lens может переключаться внутри него.

Command Registry содержит ID, input/output schema, semantic owner, availability predicate, grant requirements, side-effect class, undo capability и audit policy. Mouse, keyboard, Command Bar и агент вызывают одинаковый owner command. Возможные категории: navigation, view/layout, selection/lens, investigation, draft mutation, research, export, approvals и отдельно Execution.

Пример взаимодействия: пользователь выделяет участок → спрашивает о реакции цены на агрессивный поток → видит parsed intent и Context chips → получает proposed Footprint/Evidence panes и annotations с основаниями → применяет конкретный ChangeSet. Имена команд публикуются после conformance tests; текст пользовательского запроса не является разрешением на mutation или сделку.

## 9. Terminal и профессиональные Options workflows

Terminal объединяет underlying chart, depth/DOM, tape, footprint, heatmap, VWAP/profile, CVD, leverage и versioned market events. У каждого показателя видны источник, venue, units, временное окно и data quality. Candle-proxy CVD не маскируется под агрессорный поток. Нельзя восстановить точные числа из картинки вместо typed inputs.

| Options view/workflow | Поведение и связи | Критерий проверки |
|---|---|---|
| Overview / Command Center | Underlying/index, IV/RV, term/skew, expiry context, material changes | Числа ведут к source/compute и asOf |
| Smart Chain | Bid/ask sizes, IV/Greeks/OI, filters по expiry/delta/liquidity, multi-selection | Stable instrument IDs; sorting не меняет выбранные legs |
| Strike Inspector | Один strike/expiry object из Chain, Gamma, Flow или Scenario | Переход сохраняет expiry/cursor/model refs |
| Gamma Map | Concentration, signed assumptions, observed flow и inventory estimate отдельно | Нельзя смешать модель inventory с наблюдаемым фактом |
| Options Flow | Prints/blocks/RFQ metadata только при наличии прав; inferred structures отдельно | Inference label и сведения о недостающих данных |
| Volatility | Surface/smile/skew/term, IV/RV, percentiles и regimes | Methodology, interpolation coverage и model labels |
| Scenarios | Условия, alternatives, invalidation, evidence for/against | Revision diff, не silently overwritten forecast |
| Strategy Lab | Intent/template/pro builder → общий StrategySpec | Не создаётся новый engine для каждой вкладки |
| Payoff/Valuation | Terminal payoff отдельно от pre-expiry scenario и executable quote | Time/spot/IV shocks; cash/settlement currencies видимы |
| Positions / Guard | P&L/Greeks/concentration, expiry, Hold/Close/Roll/Reduce comparison | Свежий reconciled snapshot и отдельный approval |
| Options Scalping | Синхронные underlying/option views и gated ticket | Selection не превращается в order send |
| Options Replay | Общий clock для chain, prints, surface и underlying | Ни один read не уходит за replay asOf |

Нажатие на option print перемещает underlying cursor к тому же моменту. Переход Scenario → Lab переносит Thesis/context и создаёт draft, а не подписанную сделку. При отсутствии bid/ask интерфейс показывает unavailable; mark не становится исполнимой ценой. Показываемая конструкция может быть supported для definition, но unavailable для valuation/backtest/paper/live; пять статусов независимы.

Полная исходная детализация Options сохранена в [PRODUCT-SURFACE.md](DEVELOPMENT.md#DEP-be1ad84d43), расчётные владельцы — [OPTIONS-ANALYTICS.md](DEVELOPMENT.md#DEP-037669e492), lifecycle — [STRATEGY-RESEARCH-AND-EXECUTION.md](DEVELOPMENT.md#DEP-b18ccf612f).

## 10. Ask, ChangeSet, Thesis и Watch

`SelectionRef` должен включать scene/workspace ID, stable instrument refs, time domain, time/price bounds, cursor/asOf, visible/selected object refs, layers и base revisions. Context inspector показывает включённые и исключённые данные, gaps/freshness, privacy и бюджет. Screenshot допускается как дополнительный неавторитетный input для UI, не для вычисления цен или риска.

Fast Ask возвращает небольшой evidence-backed ответ; Deep Investigation — ограниченный Run с progress, conditional specialists и Critic. Presenter не добавляет новых numerical claims. Follow-up ссылается на предыдущие objects/results, а не бесконечно наращивает transcript.

ChangeSet содержит target IDs, base revisions, typed operations, reason/evidence, rights, reversible flag и preview. Apply проверяет revision повторно. При конфликте — новый preview; автоматическое overwriting не допускается. Undo возвращает только обратимое состояние среды. Отмена финансового приказа или закрытие позиции — самостоятельные команды, не UI undo.

Thesis хранит horizon, claims, alternatives, invalidation, evidence for/against, expiry и revisions. Watch компилируется в явные predicates, freshness rules, cooldown, budget и notification policy. Mission использует несколько Watch и bounded Runs; исчезновение feed не считается подтверждением. На desktop sleep hosted Mission продолжает работу только если действительно активирована и имеет scheduler/budget; локальная недолговечная задача не выдаётся за серверную.

## 11. Research, Compare, Journal и обучение

Compare имеет два разных смысла: синхронное сравнение instruments/venues и поиск похожих исторических эпизодов. Для similarity задаются feature definition, normalization, universe, asOf eligibility и метод. Similarity score не является вероятностью повторения исхода. Поиск похожих эпизодов в replay не видит будущее; просмотр outcome выполняется в отдельной фазе Review.

Quant Lab показывает Hypothesis/StrategySpec, DataCapabilityReport, ExperimentSpec, immutable trial IDs, progress, cost, failed/skipped cases, validation и Strategy Passport. Code editor/console — интерфейс sandbox job, не shell в hosted market-analysis worker. Workspace не владеет вторым simulator: существующий Rust compute вызывается через owner contract. Пользователь задаёт inputs/formulas, видит dependency view, редактирует независимые entry/exit и сравнивает варианты. Исследование связи может обойтись без позиции; ни одна именованная стратегия не обязательна. Отрицательный результат сохраняется и не блокирует полезный Investigation.

Journal связывает Scene, данные на момент решения, accepted/edited/rejected proposals, manual actions, imported trades и outcomes. CSV cold start содержит provenance и limitations, не получает неподтверждённые timestamp/fees. Postmortem разделяет качество процесса и результат. Tutor поддерживает Guided Replay, Live Tutor и Review на тех же domain objects. Simple/Trader/Quant меняют глубину интерфейса, не truth или полномочия.

## 12. Agents, Missions, Memory и Capital

WKS-022 показывает независимый Decision timeline: input refs, события, outcome availability и completeness IMPORTED/RECONSTRUCTABLE/VERIFIED_AS_SHOWN. Отсутствующие source bytes не скрываются. WKS-023 реализует минимальные Fleet и аутентифицированные approvals; WKS-024 добавляет Builder после готового Product AgentSpec. WKS-010 сохраняет весь Memory Inspector/Guided Replay scope.

Эти возможности — расширения существующих Agents/Journal/Replay views, не новый каталог и не обязательная часть первых экранов. ONLINE/24-7 — сочетания Product-осей; закрытие окна не останавливает hosted Mission, а недоступный Node не объявляется остановленным. Preview/apply привязаны к digest/revision/expiry.

Capital остаётся стратегической программой, кроме конкретных PAPER-фасадов. User code, ключи, Risk Kernel и canonical market state не переходят в React. Hosted private Skills не входят в client build.

## 13. Scene/stream/rendering contract

Существуют три разных тракта. Market Gateway передаёт high-rate series/frames напрямую авторизованному клиенту. Product обслуживает coarse queries, objects и команды. Harness возвращает редкие semantic events и результаты Runs. High-rate tick path не идёт через LLM и не раздувает React state.

Scene protocol задаёт snapshot ID, schema/method version, instrument/time domain, layer IDs, units, viewport and transform, source epochs, sequence/watermark и availableAt. Delta применяется только к совместимому base snapshot и epoch. На gap или неизвестный ordinal клиент останавливает зависимый increment, запрашивает resync, отображает stale и не соединяет несовместимые сегменты.

Backpressure различает: durable trades/ledger events нельзя silently drop; отображаемые frames можно coalesce по контракту; пропуск frames не означает пропуск authoritative records. Viewport subscriptions ограничивают диапазон и детализацию. Скрытые вкладки снижают render work. Decimation помечается как presentation и не меняет расчётные inputs.

Базовые категории renderer: document/table DOM; price chart adapter; analytical plot; high-rate Canvas/WebGL; optional measured GPU/native backend. Docking/chart/GPU packages заменяемы через narrow ports. Shared Scene/selection/hit-test contracts обязательны. GPU loss даёт восстановление или ограниченный fallback с видимыми потерянными слоями, не выдуманную полную parity.

Replay clock является общим для всех связанных views, agent inputs и query tools. После перехода назад очищаются или изолируются будущие snapshots/caches; никакой доступный через hidden panel future quote не должен попасть в Run. LIVE/REPLAY/PAPER маркируется постоянно.

## 14. Browser/Desktop, безопасность и восстановление

Общий web-native client core предоставляет objects, commands, panes, layouts, scene, config и published SDK. Browser shell владеет routing/account/share; Desktop добавляет host adapters для native windows, filesystem opt-in, keychain и signed updates. Выбор конкретного wrapper фиксируется spike; отсутствие всех native функций не блокирует Connect.

Persistent workspace хранит schema version и migration path. Local crash recovery не перезаписывает более новую server revision. При reconnect клиент сверяет cursor, snapshot, grants и mutable state, затем восстанавливает subscriptions. Старый approval не становится актуальным после восстановления окна. Public share/export проходит preview и rights check; отсутствие права на raw data export не раскрывает его через screenshot metadata или logs.

Клиент не получает private Odelix Skills, rubrics, team prompts или eval corpus. Доступ к hosted capability не равен праву выгрузить реализацию. Third-party extensions не получают новые capabilities по тексту manifest. Production secrets и полный скрытый chain-of-thought не пишутся в diagnostics.

## 15. Delivery и проверяемая приёмка

Единая очередь находится в [Delivery Roadmap](DEVELOPMENT.md#DEP-4cc69c028e): ранние browser WKS/stream/footprint, semantic episodes и options capture/context. Desktop/professional expansion позже; оплата Connect не блокирует первый экран.

| Проверка | Acceptance |
|---|---|
| WS-01 Identity parity | Один object открывается из UI/API/агента с тем же ID/revision |
| WS-02 Selection | Price/time bounds, layers и asOf совпадают с Context inspector и tools |
| WS-03 Commands | Mouse/keyboard/agent получают одинаковый policy verdict |
| WS-04 Preview conflict | Stale ChangeSet не применяется к более новой Scene |
| WS-05 Undo | Обратимые изменения восстановлены; торговая операция не объявлена отменённой |
| WS-06 Stream recovery | Gap/epoch/reconnect требуют корректного resync, не silent stitching |
| WS-07 Replay firewall | Future data недоступны всем связанным views/tools/cache |
| WS-08 Options semantics | Instrument/currency/expiry/model и executable/mark различимы |
| WS-09 Long session | Длительный soak на объявленном dataset; heap/listener/subscription growth измерен |
| WS-10 Degraded renderer | Context loss/fallback не выдаётся за полный набор данных |
| WS-11 Privacy | Export/share/diagnostics не раскрывают чужие/private/запрещённые данные |
| WS-12 Agent interruption | Cancel/reconnect восстанавливает actual state, не повторяет неизвестный side effect |
| WS-13 Research | Paper/synthetic/historical/live разделены; trials и skipped cases не теряются |
| WS-14 Catalog coverage | Все 72 source modes имеют mapping; 12 pane roles, 15 workspaces, 12 lenses сохранены |
| WS-15 Accessibility | Keyboard focus/reordering/inspector/approvals доступны без мыши; controls не различаются только цветом |

Это проектные acceptance criteria. В r12 выполнена документальная проверка WS-14; runtime-тесты WS-01–13/15 не запускались.

## 16. Что сознательно не объявляется решённым

Не подтверждены actual implementation, performance/FPS, выбранный production wrapper/GPU library, поддержка popout во всех hosts, commercial data rights, текущие broker permissions и доступность browser-agent integration. Реальный implementation inventory заполняется по коду/CI. Исторические числа стоимости, времени и third-party capabilities не становятся результатом этой ревизии.


## Внешние терминалы — уточнение r13

FinceptTerminal не становится основой Workstation: прочитанные файлы находятся в Qt/C++ дереве, а лицензирование содержит противоречие. Код, assets и готовые layouts не переносятся. Возможные требования к node-based составлению зависимости реализуются самостоятельно в рамках существующих Scene/Selection/ChangeSet/Command contracts, если пользовательский процесс оправдывает такой editor. Простое название функции другого терминала не создаёт новый обязательный экран. Existing React/TypeScript shell, общие web/desktop objects и calm-by-default UX сохраняются.

Подробности и статусы: [единый реестр заимствования](DEVELOPMENT.md#DEP-52ea262d35), [проверка исходников](DEVELOPMENT.md#DEP-143d61eff7).


## Актуализация r15 — рабочий flow и проверяемая семантика

Начать shared browser workbench сразу: chart cluster, footprint, depth, health, selection и replay. Raw/semantic/combined — слои одного объекта; full Desktop позже. Исходный 72-view каталог сохраняется.

Подробная обязательная спецификация: [SEMANTIC-FLOW-UX.md](#semantic-flow-ux). Старые утверждения о полноте прототипа и исторические оценки сроков не являются runtime evidence.

<a id="18-shared-read-integration--r151"></a>
## 18. Shared-read integration

Нормативная передача и приёмка: [план Market](DEVELOPMENT.md#DEP-13674f89c9). WKS-007/008 исправляют application semantics; WKS-016 реализует блоки и bounded client cache. Начать UI можно на frozen fixtures; завершённая runtime-поставка требует producer→wire→real consumer tests.

Raw graph не создаёт отдельный market reducer при subscribe/pan/zoom/timeframe. Пользовательский ViewQuery адресует общие данные. Стакан и незакрытая секундная свеча обновляются внутри секунды; исторический base не ограничивает event processing. Смена разрешения сохраняет exact semantics/coverage, а не скрытую аппроксимацию.

NoChange сохраняет old timestamp. Replace([]) очищает полный названный текущий scope. Patch применяет explicit delete/upsert; Invalidated показывает unknown, не zero. Закрытые прошлые колонки не стираются. Late revision не применяется поверх новой; mid-window escape не называется market cancel. Повторные blocks/frames не удваивают значения. Потеря live-continuity вызывает bounded resync с manifest/cut, а не бесконечный reconnect к непомещающемуся snapshot.

F pilot и SCALE_READY — разные результаты. STK-101 не означает 100k readiness; STK-013 проверяет workload/ресурсы целой связки. Текущие source contracts не объявлены внедрёнными данным документом. Каталог 72 views/12 roles/15 workspaces/12 lenses сохранён полностью.



---
<a id="semantic-flow-ux"></a>
## Workstation-first: raw flow, semantic layers и связанный options context


**1.0.0 · 2026-09-20 · PROPOSED.** Дополняет полную [Workstation specification](WORKSTATION-SPEC.md); исходные 72 views / 12 panel roles / 15 workspaces / 12 lenses не удаляются и не становятся all-at-once release.

### Первый экран

Один browser workbench: price/footprint canvas, near heatmap/COB при доступности, CVD, depth BID/ASK, profile, общий курсор/ось, health/lag/source strip, selection и Inspector/Ask. Browser dev-host в `odelix-workstation` — средство разработки reusable client packages, не второй Product backend и не новый публичный Web-продукт. `odelix-web` потом компонует те же packages. Desktop host позже.

Данные поступают через adapter текущего Market wire. Local fixture позволяет начинать UI одновременно с producer. Реальное пользовательское подключение требует раннего grant; unsafe public gateway не считается допустимым ускорением.

### Режимы

`RAW_PRICE`, `RAW_FOOTPRINT`, `SEMANTIC`, `COMBINED`. Смена режима сохраняет instrument, viewport, time/cursor, selection и object refs. Семантическая метка не меняет OHLC; raw доступен в один шаг. Grid/price bins, время, units и CVD anchor видимы. High-rate stream/renderer находится вне React rerender и вне LLM context.

### Semantic object

Бар — отображение EpisodeRef; один эпизод может пересекать бары. Клик раскрывает Observation → Interpretation → Alternative → Missing evidence → Links. У ordinary/mixed/insufficient состояния есть собственное тихое отображение. Пока прогноз не принят, показывать «Интерпретация; прогноз не оценён», а не декоративный процент. Source freshness и model confidence не объединять в одну «точность».

Actions: Explain/Ask; Show supporting data; Compare similar; Save Thesis; Open Replay; Draft research condition. Последнее сохраняет draft, а не исполняет приказ. Preview/Apply/Undo относится к UI/документным изменениям; отмена анализа и реальная отмена приказа не синонимы Undo.

### Время и перерисовка

Незакрытый бар — provisional. При новом input старая оценка становится stale, а не живёт бесконечно. Persisted revisions позволяют показывать As shown then и Reanalysis now как разные режимы. Timeline использует visibleAt, а не размещает поздний ответ на eventAt задним числом. При недоступном provider график/выделение/replay не деградируют до blank screen.

### Options

Сохранить отдельную глубину Options prototype: chain, flow tape, IV/skew/term, strike/expiry map, scenarios и synchronized underlying. Многоопционная структура только candidate, если matching/legs не подтверждены. Observed option flow не dealer inventory. Переключение options episode → underlying сохраняет общий asOf и evidence. До OPTIONS_FLOW_READY — honest tape/quality или fixtures, не уверенные assumptions.

### Find Similar

UI сигнатуры/совпадений взять из prototype; production values — Market, не sample heuristics. Метод/нормализация/version и отрицательные совпадения видны. Similarity не probability; future outcomes не участвуют в retrieval признаках. Corpus/exclusions фиксируются до просмотра результатов.

### Приёмка раннего WKS

Настоящий sample отображается согласованно во всех слоях; swap renderer не меняет числа; reconnect/switch instrument/revision сохраняют корректность; stale/partial ясно отличаются от нуля. Keyboard navigation и screen-reader labels есть у critical actions. Базовые workload проверки совместно с Market Phase A; полные high-rate/desktop матрицы не блокируют первый read-only workflow.

Issues WKS-001/002/003/007/008/011/012 начинаются в первой волне; 013 — semantic UI, 014 — as-shown replay, 009 — options связка, 015 — similarity. Все paths proposed, реализации в этом бандле нет.


---
<a id="runtime-modules"></a>
## Runtime modules — локальная область Workstation

Перенесено из межрепозиторной сводки без изменения перечня/обязанностей. Старые имена gates здесь — датированный design vocabulary, не статус или новая очередь.

### 8.4 Workstation Runtime modules

Workstation Runtime — не шестая domain group и не отдельный backend. Это
модульная клиентская оболочка внутри repository `odelix-workstation`,
публикуемая как versioned packages для Desktop и отдельного `odelix-web`.
Она реализует
принципы программируемой среды из Product Charter: адресуемые объекты, единый
язык команд, долговечные workspaces, panes/views, keyboard completeness,
config-as-code и открытые contracts.

Client foundation — собственная **TypeScript + React** web-native оболочка.
React является presentation/composition mechanism, а не владельцем Workspace,
Scene, command semantics или domain state. Готовые UI/runtime packages ниже —
сменяемые private/adapters; они не образуют fork внешней платформы.

| ID / client module | Ответственность | Технологическая база | Разрешённые зависимости | Gate |
|---|---|---|---|---|
| `WKS-APP application-client` | Session, typed command/query transport, policy errors и projection cache | Свой TS client; React; TanStack Query как cache adapter | application contracts и transport adapters | SCENE_CONTRACT_READY / FLOW_DESK_PILOT |
| `WKS-OBJ object-navigation` | Stable routes, open/focus/back/forward/reopen для addressable objects | Своя object model + browser routing adapter | `WKS-APP`, нейтральные IDs/refs `PLT-CK` | FLOW_DESK_PILOT → PRO_WORKSTATION |
| `WKS-CMD command-runtime` | Registry команд, palette, bindings и единый dispatch | Своя command registry; UI только presentation | command descriptors через `WKS-APP`; capability decisions | SCENE_CONTRACT_READY / PRO_WORKSTATION |
| `WKS-VIEW pane-view-runtime` | View registry, pane lifecycle, focus и renderer ports | Своя registry/lifecycle + React views | `WKS-OBJ`, public projections через `WKS-APP` | FLOW_DESK_PILOT → PRO_WORKSTATION |
| `WKS-LYT layout-runtime` | Persistent Workspace layout и session recovery | Своя schema/recovery; Dockview SPIKE adapter | `WKS-VIEW`, public `INV-WSP` commands/queries | PRO_WORKSTATION expansion |
| `WKS-SCN scene-interaction` | Chart viewport, selection, cursors, layers и overlays | Своя Scene semantics; Lightweight Charts + Pixi/custom WebGL2 adapters | `WKS-VIEW`, public `INV-SCN`/`INV-LNS` contracts | SCENE_CONTRACT_READY / FLOW_DESK_PILOT |
| `WKS-CFG configuration-runtime` | Versioned layout/keymap/preferences as code | Свои schemas; CodeMirror только editor adapter | public schemas `WKS-CMD/WKS-LYT`; без domain imports | PRO_WORKSTATION expansion |
| `WKS-EXT extension-runtime` | Поздний sandbox predicates/commands/views | Свой SDK; CEL, затем возможно Extism/WASM | только published extension SDK и capability port | Expansion |

Границы, предотвращающие новую связную клиентскую «глыбу»:

1. `WKS-*` не импортируют domain aggregates, repositories или vendor types.
2. Business truth остаётся у `INV-*`, `MKT-*` и `PLT-*`; client хранит только
   projection/cache/local preferences.
3. `WKS-CMD` не знает о pane implementations; command target задаётся
   descriptor/handler registration.
4. `WKS-VIEW` не знает бизнес-смысл объекта; view подключается по typed
   projection contract.
5. `WKS-SCN` не вычисляет market evidence и не создаёт вторую MarketState.
6. Browser (`odelix-web`) и Desktop композируют те же published runtime
   modules; delivery-specific API заканчивается в adapter/bridge.
7. Любой новый edge между `WKS-*` добавляется только при невозможности выразить
   связь через существующий command, projection или renderer port.

---



<a id="etape-implementation"></a>
## Реализация UI-механики: eTape selective fork

**Источник выбран, код ещё не принят в runtime.** ET-01..07 определены в [FOUNDATION-DECISION](FOUNDATION-DECISION.md#selected-ui-scope) и [локальном import manifest](../delivery/ETAPE-UI-IMPORT.json). Это shell/panels/link groups/Scheduler/chart/DOM/tape и часть lifecycle спроса. Наши Layer/Selection/Scene, exact Market data, Product state и Pi остаются владельцами смысла. Полный каталог 72 views не урезан и не выдан за существующий eTape functional set.

Первый local scope остаётся в #first-release-scope; внешний pilot дополнительно принимает адаптированные DOM/tape. WKS-018 не запускает новый конкурс оболочек; library cores не форкаются.

<a id="external-data-ux"></a>
## Внешний data preview: источник принадлежит слою

**r15.8, selected implementation scope; runtime NOT_RUN.** Одна Workstation на React/Dockview с выбранным selective eTape UI получает и native, и external данные через собственный интерфейс. Новой оболочки или OpenBB Workspace нет.

Первый внешний срез: candles по явно обозначенному Deribit futures/perpetual instrument и текущая reference-rate таблица ECB. Не обещает полноценный historical macro series; FRED personal-file route отдельный; API-preview не активен. Следующий ограниченный срез — current Deribit chain и Strike Inspector. Развивается одновременно с native recorded footprint, не заменяет его.

**Per-layer DataBinding** хранит source profile, canonical instrument, data kind, dataset/receipt/revision, transform, quality и временную границу. Пользователь видит publisher и intermediary, delayed/polling/current-snapshot status, единицы/округление, capture interval и unknown completeness. Подключение к HTTP не означает fresh LIVE.

Выделение → Inspector → Ask передаёт именно эту binding/revision. Изменение provider создаёт новый контекст, поздний старый ответ игнорируется по generation. Сравнение разных sources допустимо только явно; нельзя склеить Binance spot и Deribit perpetual с общей подписью BTC. Saved scenes продолжают ссылаться на старый receipt, пока права хранения/доступа действуют.

При отсутствии данных/прав unsupported layers (footprint, heatmap, CVD, trade tape, optionsflow) отключены для этого профиля. Не генерировать недостающие цены/объём и не скрывать качество под красивым интерфейсом. Rendering interpolation, если существует, не входит в Evidence.

Reopen сохранённой external версии не обращается к vendor. Новая загрузка — отдельная revision. Если хранение не разрешено, UI обозначает воспроизведение недоступным; такой режим не закрывает persisted-preview gate. Off/timeout OpenBB не ломает native views.

**Приёмка:** WKS-020 + STK-016 → EXTERNAL_DATA_PREVIEW; MKT-037 + WKS-021 → EXTERNAL_OPTIONS_PREVIEW. Ни одна из них не равна native FLOW_DESK/PIT/SCALE/semantic validation. Local preview не открывает public endpoint; Product access/grants нужны при фактическом внешнем использовании.
<!-- R159 coverage-ui -->
## Coverage и исторические версии данных

WKS-025 расширяет data Inspector выбором source/series/instrument/class/period/vintage и временем знания. Source-specific Deribit personal universe и bounded FRED scope отображаются вместе с pending-rights/unavailable/gaps, а не только успешными значениями. `source_asof` и `system_as_known_at` — разные режимы; latest пересмотр не перерисовывает сохранённое прошлое.

Каждый слой сохраняет DatasetBinding и generation. Source switch/permission loss не запускают скрытый fallback. Ранние flow/external scenes не ждут этого расширения; все прежние 72 view IDs сохраняются.

<a id="pilot-dom-tape"></a>
## Приёмка PILOT: обязательные отображения

FLOW_DESK_PILOT включает свечи/footprint, heatmap/CVD/depth по заявленному профилю, **DOM и Time & Sales (WKS-019)**, точный Selection/Inspector и восстановление. Это не все 72 целевых views. FLOW_DESK_LOCAL — отдельная ограниченная приёмка; её scope не расширяется новым сбором. Если tape входов нет, не подставлять синтетику под видом live.

Личный dataset namespace не появляется в продуктовом picker только по совпадению пользователя. Источник/качество/права видимы на уровне каждого слоя. Agent Validation Profile не называется Agent Passport; Decision/Strategy/Research Passport сохраняют свой отдельный смысл.
