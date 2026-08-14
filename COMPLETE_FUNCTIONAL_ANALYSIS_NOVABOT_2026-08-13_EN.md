🌐 Language: [Français](./COMPLETE_FUNCTIONAL_ANALYSIS_NOVABOT_2026-08-13_FR.md) | [English](./COMPLETE_FUNCTIONAL_ANALYSIS_NOVABOT_2026-08-13_EN.md)

---

# Complete Functional Analysis of NovaBOT

## Consolidated update — August 14, 2026

This update is based on the active project-root source code as of August 14, 2026. It supplements and, where wording differs, supersedes the August 13 baseline retained below for traceability.

### Verified current scope

- 376 useful files excluding backup archives, reports under `Markdown`, `__pycache__`, and `.pyc` files;
- 248 Python files: 171 production/technical files and 77 test files;
- 90 language JSON files, 18 PNGs, 9 root reference Markdown files, 5 BAT files, 2 PowerShell scripts, one SPEC, one ICO, one MQ4 EA, and one notice;
- latest documented full suite: 1,016 tests, 30 historical assertion failures, 0 errors;
- the changes below also have focused characterization tests.

### Deeper MT4 lifecycle and MT5 parity

The MT4 lifecycle remains separate from the MT5 engine. It was strengthened without routing MT4 execution through MT5:

- a triggered MT4 pending order is promoted into the existing logical batch instead of creating another lifecycle;
- MARKET and LIMIT branches from the same signal keep the same Telegram correlation and business batch;
- history is queried by ticket and reconciled with live inventories;
- TP, initial SL, break-even, secured SL, Smart Close, manual close, partial close, and pending deletion are classified from available MT4 evidence;
- a partial close does not become a new position and is not counted again as a terminal close;
- MT4-specific intermediate notices do not replace contractual lifecycle notifications;
- “Allow LIMIT orders after TP1” remains the source of truth for keeping or cancelling sibling LIMIT orders.

MT4 history is still limited to the history loaded by the terminal. Temporarily unavailable live evidence causes deferment or an explicitly unknown cause, never an invented cause.

### MT4 connection, identity, and bridge allocation

MT4 connectivity relies on the EA and ZeroMQ, then on `READY` and heartbeats for the observed account. Current safeguards include:

- account authentication through a temporary INI under the profile-scoped secret directory (`secrets/mt4_runtime`);
- validation of the identity actually published by the EA before declaring the session connected;
- association of the bridge with the terminal that was actually started, with explicit rejection of a different terminal/account;
- cancellable connection work outside the GUI thread;
- fast failure on a confirmed account mismatch;
- automatic allocation of a free ZeroMQ port pair when another NovaBOT instance already uses the historical 6001/6002 pair;
- propagation of allocated endpoints to the EA without closing or hijacking another visible instance’s bridge.

Pywinauto remains limited to bridge installation/configuration. It is not the MT4 account-login mechanism.

### MT4 and Copy Trader responsiveness

Expensive MT4 reads are grouped around a live snapshot that can be reused during a short cycle. The Copy Trader can consume the shared MT4 observer instead of running a second full observation of the same source. UI queues are drained with a bounded budget so Qt regains control between event batches. Repetitive worker logging is reduced without hiding errors or important transitions.

These are transport and observation optimizations. Cache remains non-authoritative for ticks, accounts, positions, pending orders, history, and close causes.

### Copy Trader and the MetaTrader module account

“Use MetaTrader module account” is initialized when the Copy Trader is built, including when MT4 is the active main platform. The MT4 path loads the live identity and shared session immediately; it no longer requires manually clearing and rechecking the option. The route matrix is unchanged: MT5→MT5, MT5→MT4, and MT4→MT5 are allowed; MT4→MT4 remains forbidden.

### Module scrolling and display adaptation

All four primary modules are wrapped in `ModuleScrollViewport`. Content keeps its natural size; horizontal or vertical scroll bars appear when the display mode, font size, or window cannot fit it. Refresh logic still reaches the actual module through the viewport wrapper.

The lower global console no longer has layout priority over module content. `display_policy` derives its height from available space and it is currently limited to two visible lines; remaining content stays available through scrolling. Dialogs continue to use shared display adaptation, except the profile manager under its specific contract.

### Technical messages and languages

Relevant connection messages and technical errors use the `technical.json` translation domain available in all ten languages. Legitimate functional differences remain explicit: MT4 refers to AutoTrading, the EA, DLL permissions, and the bridge; MT5 refers to algorithmic trading and the MT5 API. Harmonization standardizes structure and translation, not platform semantics.

### Safe adjustment of TPs to the actual entry

The Money Management option “Adjust TPs to actual entry” preserves its planned reference and can be reevaluated by the lifecycle, but applying it now goes through one admission rule:

- BUY: adjust only while the executable current price is strictly below the actual entry;
- SELL: adjust only while the executable current price is strictly above the actual entry;
- equal price, already favorable price, or unusable tick: do not adjust;
- BUY uses bid and SELL uses ask for the safety decision;
- STOP orders remain excluded and SL is unchanged.

`ExecutionAdmissionPolicy` owns this rule, which is applied consistently by MT4 and MT5 execution and lifecycle paths. User documentation and UI summaries were updated in all ten languages.

## August 13, 2026 consolidated baseline (retained history)

This section records the verified August 13, 2026 state and is retained for traceability. The August 14 update at the top of this document is authoritative where wording differs.

### Recalculated scope

- 364 useful files excluding `__pycache__` and `.pyc`;
- 245 Python files: 170 production/technical and 75 test files;
- 80 language JSON files, 18 PNGs, 9 Markdown files, 5 BAT files, 2 PowerShell scripts, one SPEC, one ICO, one MQ4 EA, one notice, and one bundled mql-zmq archive;
- successful compilation of `app`, `validation`, and `main.py`;
- 978 tests run, 26 assertion failures, 0 errors.

### Actual dual-platform trading architecture

The MetaTrader tab selects MT5 or MT4 and then a matching discovered installation. Historical `MT5App` remains the UI integration facade, while `active_platform` selects two separate engines:

- MT5 keeps `MT5DomainMixins`, the `MetaTrader5` Python API, `MT5BrokerFacts`, MT5 transports, and the MT5 lifecycle;
- MT4 uses `MT4TradingEngine`, `MT4DomainMixins`, `MT4PlatformSession`, `MT4PlatformAdapter`, MT4 transports, and a separate MT4 lifecycle;
- `app.trading` owns genuinely reusable models, ports, and platform-neutral services;
- `TradingPlatformRegistry` and `TradingRuntimeState` bind the active session and state to a platform identity.

Selecting MT4 does not route execution through the MT5 API; selecting MT5 does not use the ZeroMQ bridge.

### Primary MT4 path

`Telegram → filter → parser → aliases → admission/MM → validation → MT4OrderExecutionMixin → MT4OrderTransport → MT4PlatformAdapter → MT4QueueTransport → ZeroMQ worker → MT4 EA → terminal → live inventories/events → MT4 lifecycle → Telegram outbox → Dashboard/supervision`.

Parsing, Money Management, and admission stay in the application layer. Broker facts and terminal actions remain platform-specific.

### MT4 connection and bridge

A valid connection requires a real MT4 installation, the `.mq4` source and compiled `.ex4`, mql-zmq includes, `libzmq.dll`, `libsodium.dll`, allowed DLL imports and AutoTrading, followed by `READY` and heartbeats for the expected account.

The worker starts the terminal with a temporary INI for the official terminal login flow. Pywinauto is not the account-login mechanism; it is limited to assisted bridge installation/configuration. A running terminal process alone is insufficient: account identity must be reported by the EA.

The installer resolves the MT4 data directory, prefers the bundled mql-zmq archive, copies components and the EA, invokes MetaEditor compilation, and may configure the EA. Presence checks distinguish source, compiled binary, includes, and DLLs.

### MT4 broker facts, ticks, and symbols

The bridge provides account and terminal snapshots, symbol catalog and selection, broker specifications, timestamped ticks, candles, positions, pending orders, and history. Live terminal data is the source of truth. Recovery files never replace a live tick, inventory, or history query. Missing execution-critical data causes an explicit refusal or deferment.

### MT4 execution and Money Management

MT4 covers MARKET, LIMIT, and STOP orders; single, multiple, and split entries; SL/TP modifications; pending deletion; full and partial close; and Money Management protections. MARKET and LIMIT branches from one split-entry signal remain one Telegram context and one logical lifecycle. If one branch executes and another is rejected, the result is reported as partial execution.

`MoneyManagementVolumePlanner` remains the owner of volume calculation. The MT4 engine adapts broker constraints without redefining risk, lots, validation reduction, TP adjustments, or split-entry rules.

### Separate MT4 lifecycle

- `MT4TradeMonitoringMixin` observes live inventories;
- `MT4TradeClassifierMixin` classifies TP, SL, break-even, Smart Close, manual, and unknown causes from MT4 evidence;
- `MT4TradeLifecycleMixin` owns batches, TP notifications, pending orders, transitions, completion, and automations;
- `MT4StateReconciler` reconciles ordered snapshots without treating cache as terminal truth;
- MT4-specific stores prevent MT5 state contamination.

Events are identity-deduplicated. A partial close is not counted again as a terminal close. Once MARKET positions disappear, sibling LIMIT orders may be canceled according to Money Management, with a dedicated notification.

### Dashboard, supervision, and notifications

The Dashboard remains passive and consumes projections from the active platform. Shared collectors accept MT4 and MT5 evidence without trading actions. Remote supervision remains read-only. Platform-aware Telegram messages go through the durable outbox.

### Copy Trader route matrix

| Source | Target | Status |
|---|---|---|
| MT5 | MT5 | historical path preserved |
| MT5 | MT4 | historical worker/EA path preserved |
| primary-module MT4 | MT5 | shared-source observer path |
| MT4 | MT4 | prohibited |

`copy_trader_mt4_source_session()` and `MT4SharedSourceObserver` read live MT4 state. Persistent mappings protect against duplicates but are not live existence proof. MT4 may replace a ticket after partial close; `MT4SourceSnapshotTracker` recognizes the residual identity, transfers the mapping, and reduces the MT5 target without reopening. The historical MT5-source path is unchanged.

### Cache policy and limits

Caches support performance, recovery, or deduplication only. They are never authoritative for ticks, live operations, connection state, final close cause, or current mapping validity. MT4 depends on the EA, ZeroMQ, DLLs, and terminal permissions. Fake-based tests do not replace broker integration tests. Missing bridge or fresh tick blocks dependent decisions. The 26 assertion failures remain visible and are not hidden by this report.


Analysis date: August 13, 2026  
Source of truth: source code in the analyzed folder  
Document type: functional description; no source-code changes

## 1. Scope, method, and evidence level

This analysis covers the actual application entry point, PyQt5 modules, Telegram and MetaTrader integrations, workers, the MT4 Expert Advisor, supervision, persistence, languages, scripts, and tests. Findings were established from call paths starting in `main.py`, public facades and implementations, effective defaults, persistent writes and readers, Telethon handlers, Qt timers, workers, transports, and characterization tests. A file, class, or option name alone was not treated as proof.

Status terminology:

| Status | Meaning |
|---|---|
| Wired | Instantiated or called in the normal runtime path. |
| Optional | Wired, but dependent on an option or user action. |
| Disabled by default | Implemented, but initially inactive. |
| Compatibility | Preserves a historical API, branch, or format. |
| Unwired | Present, with no current production call found. |

### 1.1 Inventory

The tree contains 298 useful files: 245 Python files, 80 JSON files, 18 PNG files, 7 Markdown files, 5 BAT files, 2 PowerShell scripts, one PyInstaller SPEC, one ICO, one MQ4 EA, and one text notice. The 80 language files represent eight translation domains in ten languages. The test suite contains 75 `test_*.py` modules.

## 2. Purpose and functional architecture

NovaBOT is a Windows PyQt5 desktop application that connects Telegram to MetaTrader 5 or MetaTrader 4, executes and tracks trades on the selected platform, and provides a Copy Trader governed by an explicit route matrix.

Its main subsystems are profile/UI management, Telegram reception/filtering/copying, signal and Smart-command parsing, broker-symbol resolution, admission/Money Management/validation, MT5 execution and position actions, lifecycle/notifications/Dashboard, Copy Trader, and read-only remote supervision.

`main.py` wires `TelegramApp.new_trade_text` to `MT5App.process_external_message`, injects the MT5 Smart detector into Telegram, routes `MT5App.trade_outgoing_text` to the private Telegram group, routes durable outbox acknowledgements back to the lifecycle, shares selected Telegram sources with the identity bar and Dashboard, gives Copy Trader the primary MT5 instance for shared-source mode, and lets supervision observe modules without owning their decisions.

## 3. Startup, profiles, and main window

### 3.1 Startup

The entry point enables `multiprocessing.freeze_support()`, applies a Windows AppUserModelID, accepts `--profile <name>`, initializes language/theme/typography/display adaptation, and defers heavy imports until profile selection. Missing MT5 or Copy Trader dependencies produce placeholders rather than disabling Telegram and Dashboard. `cryptography` is required for secrets; Copy Trader additionally depends on Windows and `MetaTrader5`.

### 3.2 Profiles

Profiles can be created, selected, deleted, imported, and exported. Language and theme can be selected before opening. Each profile lives under `%USERPROFILE%/.novabot/profiles/<profile>` with `data`, `logs`, `secrets`, `sessions`, and `mt5_sessions`. A PID lock prevents simultaneous use or deletion of the same profile. Different profiles may run in parallel. ZIP import rejects absolute paths, traversal, and unsupported layouts, and resolves name collisions safely.

### 3.3 Main interface

The main window has Telegram, MetaTrader 4 or MetaTrader 5 according to the selected platform, Dashboard, and Copy Trader tabs. The identity bar displays profile, listened sources, active-platform state, profile-status icon, and setup/status button. The button is red when no setup step is ready, orange when partially ready, and green when complete. Closing stops Copy Trader workers, timers, Telegram loop, supervision observer/transport, and releases the profile lock.

### 3.4 Setup assistant

The optional checklist opens for new profiles and can always be reopened. It navigates to existing modules instead of duplicating their screens. Its eight checks are Telegram API credentials, fully authorized Telegram connection, NovaBOT bot/private group, at least one source, filtering settings, MT5 connection, an explicit `mm_settings` document, and Telegram listening enabled. Progress is stored in `data/onboarding.json`; historical completion and current readiness are distinct. Copy Trader, MT4 bridge, and Companion are outside its scope.

## 4. Preferences, languages, themes, and display

Ten languages are present: French, English, Spanish, German, Italian, Portuguese, Arabic, Chinese, Japanese, and Russian. Translation domains are `main`, `telegram`, `mt5`, `copy_mt5`, `dashboard`, `documentation`, `notifications`, and `onboarding`. System-language detection is cached for the process and falls back to French. Global preferences live in `.novabot/config.json`.

Themes are light, dark, and light blue. Display modes are compact, balanced, extended, zoom, original, and automatic; the default is original. Font modes are normal, medium, and large. Display adaptation reapplies window geometry, tab metrics, and typography after screen/DPI changes. All module headers use theme-aware logos and a shared About dialog. The code currently reports version `v2026.0.300`, build `300`, the NovaBOT copyright, GitHub link, and third-party components.

## 5. Telegram

### 5.1 Connection and secrets

Telegram uses Telethon with API ID, API Hash, phone number, and Telegram code. API credentials and bot credentials are encrypted with Fernet under the profile. The Telethon session is profile-scoped. API editing is available while disconnected and hidden when connected. Invalid API credentials trigger a targeted recovery flow. Onboarding considers Telegram ready only when the transport is connected and the session is authorized.

### 5.2 Reconnection

The watchdog distinguishes expected and unexpected disconnections, prevents concurrent retries, and uses delays of 0, 2, 5, 10, and 30 seconds. It reinstalls handlers when listening was previously active. Revoked/reauthentication-required sessions are reported as requiring intervention.

### 5.3 Bot and private group

NovaBOT talks to BotFather to create the bot, configure description/About/photo, creates a private group, adds the bot, and confirms administrator promotion with bounded retries. The onboarding step requires bot credentials and a non-zero `group_id.txt`. Deletion supports local cleanup and Telegram/BotFather operations while keeping external failures visible.

### 5.4 Sources and listening

Users select channels, groups, or user conversations. Selection is stored in `selected_chats.json` and reused by zone splitting, unconditional group execution, TP offset, identity, and Dashboard. When listening is active, Telethon handlers cover new, edited, and deleted messages. Selection changes can rebuild handlers without recreating the session.

### 5.5 Filtering

Filtering can be global or source-specific. Criteria cover a standalone numeric value, TP marker, SL marker, text/media type, media caption, replies, already-processed messages, edited-message reanalysis, and refusal display/logging. The processed registry is capped at 5,000 identities and uses `source_id:message_id` plus a content fingerprint. A BUY/SELL STOP is not mistaken for Stop Loss. Smart replies can bypass signal-structure requirements but still require replies to be allowed; a Telegram Smart command without a parent is never executed.

### 5.6 Transfer, edits, deletion audit, correlation, and outbox

After admission, Telegram first emits the execution envelope to MT5, then forwards or copies the message to the private group. Protected sources use text/media copying. `telegram_forward_map.json` preserves source/destination and reply roots. Smart commands are represented by their business notification rather than copied as ordinary text.

Edited messages are reparsed only when their fingerprint changes. `TelegramSignalCorrectionService` locates the correlated live batch, rejects identity/entry/TP-count changes, modifies SL/TP, opens missing TP operations per original branch, and avoids reopening historical closed tickets. Plan persistence is atomic only when the full correction succeeds.

Deleted-message listening is passive: it records known text, unknown deletions, and same-text recreation under a new ID without replaying or blocking anything. Durable Telegram notifications use a profile SQLite outbox with leases, retry scheduling, ordered delivery by batch, dead-letter management, parent-context handling, and lifecycle acknowledgement only after durable insertion.

## 6. Parser and broker symbols

`SignalParser` has a primary structured parser and a generic fallback. It recognizes BUY/SELL, LIMIT/STOP, symbols, single entries and ranges, SL, up to four numeric TPs, split concepts, short ranges, French forms, and multiple compatible instructions. Invalid signal-like text receives diagnostics; ordinary conversation is not reported as a malformed signal.

The parser can use `mt5_sessions/symbol_info.json` as a hint but does not require it for normal symbols. Broker resolution prioritizes exact tradable matches and family aliases, supports suffixes, Gold, Forex, indices, energies, crypto, platinum/palladium, and avoids forbidden Gold crosses. Alias data is shared with Copy Trader.

## 7. MetaTrader connection and platform selection

Terminal discovery is centralized and cached across MT5 and Copy Trader. It scans validated MT4/MT5 installations and reads server data plus a central catalog. Connection uses encrypted profile credentials and a selected terminal path. Connection and symbol collection run in one QThread job; symbol refresh failure does not invalidate a successful account connection. A 2.5-second watchdog detects runtime loss.

Before orders, NovaBOT checks terminal, account, symbol, and algorithmic-trading permissions. If Algo Trading is disabled during a Telegram path, it reports this, asks the user, and can activate the Windows terminal control; the original signal stays blocked and is not silently replayed. Manual text entry uses the same parser/execution pipeline.

## 8. Money Management

### 8.1 Effective defaults

| Setting | Default |
|---|---:|
| Mode | fixed lot per TP (`fixed_all`) |
| Fixed lot / TP1–TP4 lots | 0.10 |
| Risk | 1% |
| Equal fixed-total distribution | off |
| Deviation | 5 points |
| Automatic LIMIT conversion | off |
| Distance threshold | 100 points |
| Automatic asset-family tolerance | off |
| Historical tolerance | 100 points |
| BE, TP following, spread adaptation, validation | off |
| Duplicate prevention / trade limit | off |
| Maximum trades | 3 |
| Include pending | off |
| Virtual capital, vault, progressive TP, TP offset | off |

Three volume modes are wired: fixed lot per TP (or shared total), individual TP1–TP4 lots, and risk-derived volume. Volumes are normalized to broker `volume_min`, `volume_max`, and `volume_step`; the primary calculation belongs to `MoneyManagementVolumePlanner`.

Risk uses MT5 equity unless positive virtual capital is enabled. The vault persists protected thresholds, but `get_risk_base()` returns the full virtual capital: the protected amount is not subtracted.

Execution controls include deviation, auto-LIMIT, asset-family tolerance, spread adjustment, duplicate checks, maximum open/pending trades, balanced/strict validation, and per-group unconditional execution. The group override is active only when auto-LIMIT or automatic tolerance is enabled; it bypasses distance handling for the MARKET branch, not terminal, volume, validation, duplicate, or trade-count checks.

Protection includes BE after TP1, SL to TP1 after TP2, previous-TP following, secure-on-pips, progressive partial closes, and TP offset. Progressive close modes are 25/25/25/25, 40/20/20/20, 50/20/20/10, or a custom total of 100%. TP offset can be global or per group and selected for TP1–TP4; it only moves valid TPs toward entry and never crosses entry.

## 9. Admission and order type

MARKET uses ask for BUY and bid for SELL. `usable_tick()` tries twice. Explicit BUY LIMIT, SELL LIMIT, BUY STOP, and SELL STOP retain their intended geometry; an explicit pending order on the wrong side is rejected.

For MARKET instructions, the policy may keep MARKET, block a distant single entry, or convert a zone to LIMIT. Automatic tolerances are Forex 0.02%, metals 0.05%, indices 0.05%, energies 0.10%, and crypto 0.10% of current price. Historical points are used when automatic tolerance is disabled or unusable. `ExecutionAdmissionPolicy.is_tradable()` currently always returns true; no active weekend calendar block exists.

## 10. Split entries

Zone splitting is configured per Telegram source and requires a true zone of at least the configured width. Explicit STOP signals are never split. BUY uses the high bound as entry 1 and low bound as entry 2; SELL reverses that assignment. A MARKET signal produces MARKET plus LIMIT branches, while explicit LIMIT produces two LIMIT branches. TP volumes are calculated first, must be divisible into two valid broker lots, and are then split.

Both branches share batch, magic, SL, TPs, and Telegram context while retaining `entry_1`/`entry_2` roles. Broker-confirmed MARKET TP1 cancels untriggered sibling LIMIT orders unless “Allow LIMIT after TP1” is enabled; TP1 notification precedes cancellation notification. Manual closure of the only MARKET in a composite can cancel the only LIMIT. When enabled, entry-2 activation moves all still-open entry-1 TPs to each position's actual entry. Manual TP1 BE is scoped to sibling positions in the same branch. Validation runs per branch, not as one aggregate composite-risk decision.

## 11. Signal validator

Validation is wired but disabled by default. The selected `balanced` or `strict` mode is used. It runs after broker-symbol resolution, order-type/price selection, spread adjustment, duplicate checks, and volume calculation, but before every `order_send`.

`MarketContext` reads bid/ask/point/digits, 60 M15 candles and 80 H1 candles, calculates ATR14 and EMA20/50 on M15/H1, and labels ASIA/EUROPE/US UTC sessions. Rules run in this order: broker metadata, geometry, spread, H1 trend, TP1/ATR consistency, broker volume, and monetary risk. Missing indispensable data blocks. In balanced mode, missing trend/ATR requests reduction; strict mode blocks.

| Family | Balanced floor | Strict floor | Balanced/strict price ratio |
|---|---:|---:|---:|
| Forex | 25 pts | 15 pts | none |
| Metals | 60 pts | 40 pts | 0.015% / 0.010% |
| Indices | 100 pts | 60 pts | 0.020% / 0.012% |
| Crypto | 2,500 pts | 1,500 pts | 0.050% / 0.030% |
| Energies | 80 pts | 50 pts | 0.050% / 0.030% |
| Other | 50 pts | 35 pts | none |

`ALLOW` uses coefficient 1.0. `REDUCE` uses 0.50 balanced or 0.25 strict, possibly lower for risk budget. Reduced volumes are floor-aligned and must remain valid and strictly lower; otherwise the decision becomes `BLOCK`. `BLOCK` sends no order. Every decision is appended to profile `logs/validation_history.csv`; logging is fail-open, while indispensable-data failures are explicit and safe.

## 12. MetaTrader 5 execution

Each branch follows: normalize legacy tuple/dict parameters; resolve/select broker symbol; create/reuse batch and magic; check terminal/account/Algo Trading; apply trade limit; read tick; choose type/price; optionally adapt spread; check duplicates; compute volumes; optionally validate; send one order per TP through filling fallbacks; build unified result; register lifecycle tracking; persist analytics and publish Telegram output.

MT5 reads are centralized in `MT5BrokerFacts`; actions go through `MT5ActionGateway`, `MT5OrderTransport`, and `MT5PositionTransport`.

## 13. MT5 actions and Smart Automations

Available actions include full close, close half when broker-valid, progressive partial close, BE, SL/TP modification, move SL to TP level, pending SL/TP modification, pending cancellation, and composite Smart Close. A minimum 0.01 position is not falsely reported as 50% closed when a valid partial volume cannot be produced.

Smart families cover close, half close, BE, secure, SL modification, TP modification, and pending cancellation. Detection uses built-in and user dictionaries, Unicode normalization, overlap/ambiguity checks, and classic-signal safeguards. Telegram Smart execution requires an authorized reply and exact batch correlation; it never falls back to another source or arbitrary last batch. Simulation mode reports planned actions without sending them. Execution flows through `SmartCommandExecutor` and `SmartActionGateway`, not direct MT5 calls from the detector.

## 14. Trade lifecycle

The persistent watchlist stores symbol, direction, entry, SL, TP levels, tickets, pending tickets, batch/magic, source and Telegram context, branch roles, notification state, progressive state, Smart results, closure evidence, and delivery acknowledgement.

Closure classification uses broker deals/history, magic/tag/ticket/price tolerance, and distinguishes TP, SL, Smart Close, progressive reductions, manual closes, and unknown evidence. Live price alone does not confirm TP1 actions that require broker confirmation.

Automatic behavior includes TP notifications, BE after TP1, manual-TP1 BE with broker-distance retry, previous-TP SL following, progressive closes, split-entry LIMIT cancellation, entry-1 TP protection after entry 2, restart resynchronization, terminal classification, idempotent final publication, Dashboard archiving, and cleanup only after durable publication state allows it.

## 15. Telegram trade notifications

Opening messages include actual symbol/type, entry, SL, TPs, position/pending count, total volume, distribution, risk, potential gain, MT5 tickets/retcodes, and success/partial/failure footer. Lifecycle messages cover TP confirmation, BE, protection, partial/progressive/manual/Smart closes, pending cancellation, final outcome, and explicit failure. Reply context points to the copied original signal. The persistent outbox preserves ordering and retry across restarts.

## 16. Dashboard

Telegram/filter/parser/execution/lifecycle events are stored in `dashboard_analytics.sqlite3`. Dashboard periods are all time, 30 days, 7 days, and today. It uses only terminal-confirmed financial evidence attributable to the expected account/batch, including profit, commission, swap, and fees. Source reset is non-destructive and sets a cutoff date. Quality score weights success 25%, profit factor 20%, drawdown 15%, deep TPs 15%, consistency 10%, net result 10%, and coverage 5%; confidence reaches maximum at 20 confirmed trades.

## 17. Copy Trader MT5 to MT5/MT4

The source is MT5, either through its own worker or the connection already owned by the main MT5 module. Shared mode never reinitializes or closes the primary session. The target may be MT5 or MT4. Copy can start only after both connections are confirmed.

At startup, source inventory is compared with the persistent ticket map and target inventory. Live mapped operations are excluded; confirmed stale mappings are removed; unverifiable mappings are kept for safety. If everything is mapped, no initial OPEN is sent. Otherwise users choose Ignore (default), Copy with a second confirmation, or Cancel. Runtime anti-duplication remains a second safety layer.

Workers replicate OPEN, UPDATE, and CLOSE for positions and pending orders, including partial reductions, SL/TP, pending price, deletion, and pending-to-position transition. Exact master/slave tickets are stored in `copy_trader_ticket_map.json`. Volume modes are multiplier, equity ratio, and fixed lot, multiplied by a safety coefficient and aligned to target broker limits.

MT4 uses ZeroMQ command port 6001 and event port 6002. The EA supports open/close/delete/modify and publishes READY, HEARTBEAT, inventory, OPENED, CLOSED, UPDATED, ALIAS_LEARNED, and ERROR. Heartbeat timeout is eight seconds. The PowerShell installer auto-detects MT4 data folders, installs mql-zmq, copies the EA/notices, and asks for compilation and DLL-import authorization. The worker may reclaim the bridge from an attributable invisible old NovaBOT process but never terminates an unidentified third-party or visible NovaBOT process.

## 18. Remote supervision and NovaBOT Companion

Supervision defaults to local `127.0.0.1:8765` without authentication. Private mode chooses a local private IPv4 and requires Bearer authentication. The only routes are `GET /supervision/health`, `/identity`, `/snapshot`, and `/events?after=<sequence>`. All write methods are rejected; no remote trading/action route exists.

The observer projects public profile, Telegram, MT5, Dashboard, configuration, MM, health, and events, masking logins and sanitizing values. The private token is generated locally, protected by Windows DPAPI, and available through a pairing QR. Companion source code is absent; only this external read-only client contract is evidenced.

## 19. Persistence, caches, and logs

Profile data includes startup/settings/onboarding, selected chats, filter settings/processed fingerprints, forward map, deletion cache, notification outbox, Smart dictionary, group ID, MT5/MM settings, aliases, lifecycle watchlist, chat history, Dashboard SQLite, Copy Trader settings, and ticket map. Secrets include Telegram Fernet key/API/bot/session, MT5 key/credentials, encrypted Copy Trader passwords, and DPAPI supervision token. Logs cover Telegram/MT5/Copy consoles, filter, Smart commands, deletions, trades, market snapshots, validation CSV, and MT4 worker.

Profile JSON stores use atomic or tolerant writes according to their role. Analytics/logging/passive-audit failures are fail-open. Missing data required for trading produces an explicit refusal.

## 20. Internal ownership boundaries

`MT5BrokerFacts` owns reads; `MT5ActionGateway` owns raw actions; transports adapt requests/retcodes; `ExecutionAdmissionPolicy` owns geometry/family/tolerance; `MoneyManagementVolumePlanner` owns volume calculation; `ValidationEngine` owns ALLOW/REDUCE/BLOCK; `TelegramSignalCorrectionService` owns edited-signal changes; lifecycle stores/publishers own tracking/final idempotence; Dashboard and supervision are passive readers.

`MT5App` retains an explicit mixin order for public compatibility. `ExecutionOrderPolicy` is tested in isolation but no current production path calls it; order selection is now owned by `MT5OrderExecutionMixin`.

## 21. End-to-end functional paths

### 21.1 Telegram signal

Telethon event → passive deletion cache → source/global filter → analytics/log → MT5 envelope → parser → optional TP offset → compatible batch grouping → optional split branches → broker symbol/facts → admission/order/price/duplicate/volume → optional validation/reduction → MT5 TP orders → lifecycle → durable reply notification → broker-history monitoring/automation → Dashboard archive → supervision projection.

### 21.2 Smart reply

Authorized reply → Smart detection → exact parent/batch resolution → option/mode/value validation → gateway action → confirmed business notification. The original Smart text is not copied as a duplicate.

### 21.3 Edited message

Same ID/new fingerprint → reanalysis → rebuilt plan → original batch lookup → modify existing SL/TP and add missing TP operations → persist only on full success → publish summary and edit copied Telegram message when permitted.

### 21.4 Copy Trader

Confirmed source/target → mapping/inventory review → ignore/synchronize/cancel → source OPEN/UPDATE/CLOSE → target symbol/volume resolution → target worker execution → returned ticket → persistent mapping until confirmed close/delete.

## 22. Optional or disabled-by-default features

Initially disabled: Telegram/MT5 auto-connect, auto-LIMIT, automatic tolerance, spread adaptation, validation, duplicate prevention, trade limit, virtual capital, vault, BE, TP/SL following, secure-on-pips, progressive TP, TP offset, split entries, per-group override, and individual Smart commands. Local read-only supervision is enabled by default.

## 23. Historical compatibility and non-current code

Legacy parser tuples, `move_sl_to_tp1_on_tp2`, dated symbol-info files, comments/magic/watchlist fallbacks, stable MT5 mixin order, and old forward-map roots remain supported. `ExecutionOrderPolicy` is characterized but unwired. The Smart “simulation only” docstring is historical and does not describe execution mode.

## 24. Tests and observed contracts

Commands:

```text
python -m compileall -q app validation main.py
python -m unittest discover -s validation/tests -p "test_*.py"
```

Observed result: compilation succeeded; 978 tests ran; 26 assertion failures; 0 errors. Coverage includes profiles, CLI, languages/themes/display/About/onboarding, parser/aliases/orders/filter/Telegram/outbox/Smart/MM/split/validation, MT5 transports/actions/lifecycle, Dashboard, supervision, Copy Trader, workers, EA/installer contracts, and bridge recovery. Tests use fakes and AST for some contracts and do not prove external Telegram, broker, terminal, market, or network availability.

The 26 failures comprise one outdated About metadata expectation, seven classifier expectations, one lifecycle TP ordering/persistence expectation, one terminal manual/unknown message expectation, one live-TP monitoring expectation, one onboarding style expectation, one progressive preset/default expectation, and thirteen Telegram-filter assertions including four parameterized cases. No failure was corrected or hidden.

## 25. Observable limitations and divergences

- Windows-specific MT5/MT4/DPAPI/UIA behavior is not portable as-is.
- MT4 requires a compiled EA, mql-zmq, DLLs, and DLL imports enabled; only one local worker can own ports 6001/6002.
- Companion is absent and supervision is strictly read-only.
- No more than four numeric TPs are executed.
- The named weekend check is inactive because `is_tradable()` always returns true.
- Split-entry validation evaluates branches separately, not aggregate composite exposure.
- Vault protected capital is not subtracted from risk base.
- Dashboard never invents financial results without attributable terminal evidence.
- Telegram Smart commands without an exact parent correlation do nothing.
- Edited-signal correction rejects identity, entry, or TP-count changes.
- Deleted-message audit records only; it does not block or replay.
- About reports build/version 200, supervision identity still receives `5.0`, and the analyzed folder has another number.
- The multi-profile launcher still names `NOVABOT-WORKING 141.0.exe` and five hard-coded profiles.
- `main.ps1` contains two restart loops; the second is unreachable while the first infinite loop runs.
- No general project license is declared; the mql-zmq Apache 2.0 notice is separate.

## Conclusion

The analyzed code implements the full Telegram → filtering → parsing → correction/offset → broker resolution → admission → Money Management → validation → MT5 execution → lifecycle → notifications → Dashboard → supervision chain, plus optional MT5-to-MT5/MT4 replication. The implementation is profile-scoped, heavily characterized by tests, and preserves several historical compatibility contracts. This document is a translation only; no NovaBOT source file was changed.
