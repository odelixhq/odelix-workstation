# WKS-SCN — Scene interaction

Owns viewport, layers, time×price selection, cursor synchronization, renderer
scheduling and visual mapping of authoritative Market/Product projections. It
does not own market calculations, Thesis/Evidence state or pane layout.

Required ports: Market Scene stream/replay, Product object refs and host GPU/time
scheduler. Published: viewport/selection/layer contracts and renderer-neutral
Scene snapshot. Adapters: Lightweight Charts for candles/basic overlays;
PixiJS/WebGL2 for heatmap/footprint/order-flow. React only composes controls.

Definition of Done: online and replay BTC scene, heatmap + volume bubbles,
snapshot/delta/resync, bounded memory, selection round-trip to exact context,
8-hour soak and GPU context recovery. ETH subsecond features remain visibly
unavailable until raw L2Delta exists.


## Приёмка r15

Начать shared browser workbench сразу: chart cluster, footprint, depth, health, selection и replay. Raw/semantic/combined — слои одного объекта; full Desktop позже. Исходный 72-view каталог сохраняется. См. [SEMANTIC-FLOW-UX.md](../../docs/WORKSTATION-SPEC.md#semantic-flow-ux) и owner Issue в [общем backlog](../../docs/DEVELOPMENT.md#DEP-b335630551). Новые гарантии требуют tests/evidence; сохранённая спецификация не означает implemented.

<a id="external-data-layer"></a>
## DataBinding в Selection

[UX scope](../../docs/WORKSTATION-SPEC.md#external-data-ux) расширяет SelectionRef ссылками на источники **по слоям**, не заменяет его donor schema. Минимум: source/profile, dataset/receipt/revision, time scope, unit/transform и quality. Opaque native cursor допустим только для native profile; внешний snapshot не получает поддельный journal cursor.

Проверки: cancel/switch generation, два источника одного symbol, partial overlay, as-shown reopen, revocation, unknown revision. Source refresh не меняет сохранённый Selection и не оставляет в replay future input. Domain owner Product публикует расширение; WKS маппит screen geometry на точную ссылку.
<!-- R159 decision-selection -->
## Selection для решений и версий данных

SelectionRef фиксирует decision ID/source cursor, DatasetBinding version/vintage, knownAt, coverage и generation. Переключение времени/источника/права создаёт новую UI generation; late async update не применяется к новой Scene. Backward replay скрывает outcome, ещё не известный в выбранном режиме. Отзыв доступа меняет доступность view, но не переписывает старый as-shown record.
