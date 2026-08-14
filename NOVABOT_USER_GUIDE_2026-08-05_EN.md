# NovaBOT User Guide

🌐 **Language:** [Français](NOVABOT_USER_GUIDE_2026-08-05_FR.md) | English

---

# NovaBOT User Guide

Last updated: August 14, 2026  
Audience: NovaBOT Desktop and NovaBOT Companion users

## 1. Overview

This guide walks new users through the process from the first time they open NovaBOT to its day-to-day use.

Each chapter follows the recommended setup order.

NovaBOT is a Windows application that connects Telegram to MetaTrader 5 or MetaTrader 4. It can listen to Telegram groups or channels, recognize their trading signals, apply the active profile’s rules, execute orders on the active platform, monitor their lifecycle, and publish the results to a private Telegram group.

The application includes four main modules:

- **Telegram**: connection, source selection, filtering, and listening;
- **MetaTrader 5 or 4**: platform and installation selection, broker connection, Money Management, execution, and monitoring;
- **Dashboard**: statistics based on executions and evidence from the active platform;
- **Copy Trader**: MT5 to MT5/MT4 copying, or copying the primary MT4 account to MT5.

NovaBOT Companion lets you remotely view NovaBOT's status. Companion is a read-only monitoring tool: it cannot send trading orders.

> **Important** — NovaBOT can automatically send real orders. Always perform initial tests on demo accounts and check volumes, the broker symbol, Stop Loss, and Take Profits before enabling listening.

## 2. Requirements

Before you begin, prepare:

- a Telegram account accessible from the official application;
- an `API ID` and `API Hash` obtained from `my.telegram.org`, under **API development tools**;
- a working MetaTrader 5 or MetaTrader 4 installation;
- the login, password, and server for the selected account;
- for MT5: algorithmic trading enabled and, if assisted activation requests it, the Microsoft Visual C++ runtime matching the system;
- for MT4: the compiled NovaBOT EA, mql-zmq, ZeroMQ DLLs, AutoTrading, and DLL imports enabled.

The MetaTrader terminal must be able to connect to the selected account and display the broker's symbols. Names may vary by broker, for example `XAUUSD`, `XAUUSD-VIP`, or another variant.

## 3. Getting Started

For an initial setup, follow this order:

1. create a NovaBOT profile;
2. connect Telegram;
3. create the profile's bot and private Telegram group;
4. select sources in **TELEGRAM TRANSFER**;
5. configure Telegram filtering;
6. select MT5 or MT4 and connect the MetaTrader account;
7. collect symbols or generate aliases if necessary;
8. configure Money Management;
9. send a test signal on a demo account;
10. enable Telegram listening only after checking the result.

### Quick check before enabling listening

Make sure that:

- the header shows the correct profile;
- Telegram shows a connected status;
- the private NovaBOT group exists;
- at least one Telegram source is selected;
- the header shows the correct platform, login, and server;
- MT5 algorithmic trading or MT4 AutoTrading is enabled;
- the volume mode and its values match the intended risk;
- BE, protection, and progressive-close automations are deliberately enabled or disabled.

## 4. Creating Your First Profile

At startup, NovaBOT displays the profile manager.

### Creating a profile

1. Click **NEW**.
2. Enter a clear name, such as the broker, account, or strategy name.
3. Choose the language and theme.
4. Select the new profile from the list.
5. Click **LAUNCH**.

Each profile has its own:

- Telegram credentials and sessions;
- MetaTrader account and platform;
- selected Telegram sources;
- filtering rules;
- Money Management settings;
- Smart command dictionary;
- histories, mappings, statistics, and monitoring settings.

A change made in one profile should therefore not be considered active in other profiles.

### Profile setup assistant

When a new profile is created, NovaBOT displays an optional checklist. It opens existing screens and automatically verifies eight steps:

1. Telegram API credentials;
2. complete Telegram connection;
3. NovaBOT bot and private group;
4. Telegram groups to monitor;
5. verified Telegram filtering;
6. MetaTrader connection;
7. saved Money Management settings;
8. prepared Telegram listening.

**Skip guided setup** closes the flow without removing completed settings. **Resume later** keeps progress. Once everything is complete, the button becomes **Close**. The global button reads **Configure profile** while steps are missing, then **Profile status** once setup is complete. Its color summarizes the state: red when no step is ready, orange when setup is partial, and green when complete.

Telegram is considered ready only after real authentication; closing the phone-number prompt does not complete the step. The bot/group step turns green as soon as creation and administrator assignment are confirmed. Copy Trader, the MT4 bridge, and Companion deliberately remain outside this primary checklist.

### Importing or exporting a profile

- **EXPORT** creates a ZIP archive of the selected profile.
- **IMPORT** restores a profile archive. If the name already exists, NovaBOT selects an available name.

Exporting is recommended before making major changes to Telegram rules, Money Management, or automations.

### Using multiple profiles

Several different profiles can run at the same time. However, the same profile is locked while in use to prevent two instances from modifying the same files.

## 5. Connecting Telegram

### 5.1 Obtaining API credentials

1. Open `https://my.telegram.org`.
2. Sign in with your Telegram account.
3. Open **API development tools**.
4. Copy the `API ID` and `API Hash`.

These credentials provide Telegram API access. They are not the private bot token.

### 5.2 First connection

1. Open the **Telegram** tab.
2. Enter the API ID and API Hash when prompted.
3. Click **CONNECT TELEGRAM**.
4. Enter the phone number with its international country code.
5. Check Telegram and enter the received login code.
6. Wait for the **Connected to Telegram** message.

If the API ID/API Hash combination is invalid, use **EDIT API ID / API HASH** before trying again. This button is hidden while Telegram is connected.

### 5.3 Creating the bot and private group

Click **CREATE A BOT**. NovaBOT communicates with BotFather, stores the bot credentials, and creates the private Telegram group used for transfers and notifications.

Wait for the completion confirmation before closing NovaBOT or Telegram. If the group does not immediately appear on another device, send an initial message in the group to initialize its display in the relevant Telegram application.

The **DELETE BOT** button may offer local deletion or a purge through BotFather. A purge is irreversible.

### 5.4 Choosing the monitored groups and channels

1. Click **TELEGRAM TRANSFER**.
2. Browse the Channels, Groups, and Chats tabs.
3. Select the sources to monitor.
4. Click **Save**.

This selection also populates the group lists available in split-entry and unconditional-execution settings.

### 5.5 Configuring Telegram filtering

Click **FILTERING**. You can use a global configuration or rules specific to each source.

The main criteria are:

- require a numeric value;
- require at least one TP;
- require an SL;
- allow or reject replies;
- allow text, captioned images, captioned documents, and videos;
- filter uncaptioned images or documents, voice messages, GIFs, stickers, and polls;
- ignore messages that have already been processed;
- choose whether edited messages are analyzed again;
- display filtered messages and their reason in the console.

A Smart command sent as a reply must have a recognized parent message. A command without a parent is not executed.

### 5.6 Enabling listening

When Telegram, the bot, the private group, the sources, and the active MetaTrader platform are ready, click **ENABLE LISTENING**.

NovaBOT then transfers admitted messages to the private group and submits recognized signals to the selected MT4 or MT5 pipeline. Click again to disable listening.

### 5.7 Manually retrieving an earlier signal

The **RETRIEVE A TRADE FROM A MESSAGE** button lets you browse a conversation, select an earlier message, and send it through the processing pipeline.

This action remains subject to the rules of the active profile. The tolerances and options used are those saved when the message is retrieved.

## 6. Connecting MetaTrader 5 or MetaTrader 4

In the MetaTrader module, first choose **MetaTrader 5** or **MetaTrader 4** under **Platform**, then select an installation matching that choice. Server groups filter the catalog so the exact server is easier to select.

### 6.1 MetaTrader 5 connection

1. Select **MetaTrader 5** and the detected terminal.
2. Enter the login, password, and server.
3. Use **DISCOVER SERVERS** if needed.
4. Click **CONNECT METATRADER**.
5. Check the account, server, balance, and algorithmic-trading status in the console and header.

If algorithmic trading is disabled, NovaBOT can offer assisted activation. The assistant checks for the required Visual C++ runtime and provides the official Microsoft x64 or x86 link when it is missing. The account is ready only when the MT5 connection and trading authorization are consistent.

### 6.2 Preparing and connecting MetaTrader 4

MT4 uses NovaBOT’s local bridge: a compiled EA, mql-zmq, and ZeroMQ DLLs. If a component is missing, **CONNECT METATRADER** asks you to install the bridge instead of reporting a false connection.

1. Select **MetaTrader 4** and the exact installation.
2. If needed, use **INSTALL MT4 BRIDGE** and follow chapter 10.
3. Enter the login, password, and server.
4. Click **CONNECT METATRADER**.
5. Wait for the EA to publish `READY`, then make sure the header shows **MT4**, the expected server, and the expected login.

Account login uses a temporary profile-scoped INI file. NovaBOT does not use pywinauto to type account credentials. The button becomes **DISCONNECT METATRADER** only after the bridge, EA, and observed account are confirmed. A running terminal without a working EA is not a NovaBOT connection.

### 6.3 Broker symbols and aliases

After the first connection:

- **COLLECT SYMBOLS** records information about available symbols;
- **GENERATE ALIASES (BROKER)** builds mappings between common and broker-specific names.

Collection is not repeated unnecessarily at every startup. Use the button when the broker adds symbols, after changing account/server, or when symbol resolution fails. Always check the resolved broker symbol, such as `XAUUSD-VIP` instead of `XAUUSD`.

### 6.4 Testing without Telegram

The manual-entry area sends text through the same parser and the active platform engine. Use a demo account and a minimum lot to validate aliases, Money Management, and notifications before enabling listening.

## 7. Configuring Money Management

Open the MetaTrader module and click **MONEY MANAGEMENT**. Settings are profile-specific and apply to the active MT4 or MT5 engine.

The `?` icons provide a description of the corresponding setting.

The reset button restores default Money Management values only after confirmation. Export the profile or record custom values before using it.

### 7.1 Capital Management

Three volume modes are available.

#### Fixed lot for all TPs

You enter a fixed volume. When **Split risk equally across each TP** is enabled, this total is distributed among the TPs within the broker's minimum-volume and volume-step constraints. When the option is disabled, the configured volume is applied to each TP.

#### Lot entered per TP

You separately define the lot for TP1 through TP4. Only values corresponding to TPs present in the signal are used.

#### Lot as a percentage of capital

NovaBOT calculates the volume from the capital base, selected percentage, entry, SL, and symbol characteristics. The distribution option determines whether the calculated risk is split among the TPs or repeated for each TP.

The final volume is always adjusted to the minimum, maximum, and step allowed by the broker.

#### Virtual capital and vault

- **Use virtual capital** replaces the displayed real base for the configured calculation.
- **Enable vault** stores protected-capital and threshold information in the profile.

Check the four displayed values: real MetaTrader balance, virtual capital, protected capital, and risk calculation base.

### 7.2 Execution

#### Create a LIMIT order when the gap is high

For a MARKET signal, NovaBOT can convert the execution to LIMIT when the drift between the announced entry and current price exceeds the configured threshold. An explicit BUY/SELL LIMIT or BUY/SELL STOP order keeps its type.

#### Automatic tolerance by asset family

Tolerance can be calculated according to the symbol family: Forex, metals, indices, energies, or cryptocurrencies. The fallback field in points is used when the symbol or its family cannot be determined.

#### Slippage and deviation

Deviation is the margin sent to the broker for execution. It does not replace the distance protection applied before the order.

#### Unconditional execution by group

For groups shown from **TELEGRAM TRANSFER**, this option allows MARKET execution despite unfavorable distance when automatic LIMIT creation or automatic tolerance is enabled. Explicit LIMIT/STOP orders, Money Management, and other protections are not bypassed.

### 7.3 Protection

#### Break Even after TP

**Move to BE when a TP is reached** moves the SL of remaining positions to their entry price, with the configured offset. The move is applied after TP1 confirmation, including when a manual close is identified as the TP1 close.

#### Protection after TP2

The available choices are:

- disabled;
- move SL to TP1 when TP2 is reached;
- move SL to the previous TP whenever a TP is reached.

#### Automatic protection in pips

NovaBOT monitors profit and moves the SL when the configured threshold is reached. The offset determines the profit level locked relative to the entry.

#### Progressive partial closes

These closes are triggered from TP1 onward. They apply to the remaining positions associated with subsequent TPs.

You can choose:

- Balanced: 25 / 25 / 25 / 25%;
- Aggressive: 40 / 20 / 20 / 20%;
- Very aggressive: 50 / 20 / 20 / 10%;
- Custom, with a mandatory total of 100%.

The initial volume must be strictly greater than the activation minimum. An option can close the residual volume at the final TP.

#### Take Profit offset

This option moves only the selected TP1-to-TP4 levels closer to entry. It can apply one configuration to all groups or a separate offset and TP selection to each Telegram group. For BUY, the level is reduced; for SELL, it is increased.

#### Adjust TPs to actual entry

This option preserves the planned distances between signal entry and TPs, then reapplies them from the actual execution price for single, multiple, or split entries. It can be configured for all groups or per group.

A safety rule prevents adjustment when it could unnecessarily extend a trade:

- BUY: adjust only while current price remains strictly below actual entry;
- SELL: adjust only while current price remains strictly above actual entry;
- equal price, already favorable price, or unavailable tick: no adjustment.

SL is not moved and STOP orders are excluded.

### 7.4 Controls

#### Prevent duplicates

NovaBOT compares symbol, direction, SL, TP, and price tolerance. A signal considered a duplicate is blocked before another position is opened.

#### Limit the number of trades

Enable blocking, select the maximum, and indicate whether pending orders must be counted. Once the limit is reached, the new signal is rejected.

#### Signal validation

Validation runs before the order is sent, using the actual broker symbol, symbol information, SL/TP geometry, calculated volume, risk, and available market conditions.

- **Balanced** accepts more degraded situations and may reduce volume.
- **Strict** blocks more often when important validation data is missing or non-compliant.

Possible decisions are:

- `ALLOW`: normal volume;
- `REDUCE`: volume is actually reduced;
- `BLOCK`: no order is sent.

If the broker's minimum or volume step prevents an actual reduction, execution is blocked instead of silently keeping the same volume.

### 7.5 Split Entry and Groups

Split entry can use one configuration for all groups selected in **TELEGRAM TRANSFER**, or separate settings for each group.

For an admissible and sufficiently wide MARKET zone, NovaBOT can create:

- a MARKET branch;
- a LIMIT branch inside the zone.

The available volume is split between the two branches. If the lot for each TP cannot produce two volumes that comply with the broker minimum, split entry is not applied.

**Allow LIMIT orders after TP1** determines what happens to sibling LIMIT orders:

- option disabled: LIMIT orders that have not triggered are canceled after MetaTrader confirms MARKET TP1;
- option enabled: LIMIT orders remain active.

**Move entry 1 TPs to its entry price** acts only when the second entry actually triggers: TPs on first-entry positions are moved to their entry price. The platform must confirm the trigger; merely placing the pending order is not enough.

## 8. Configuring Smart Commands

Click **TELEGRAM AUTOMATION** in the MetaTrader module.

The available families include:

- Secure;
- Break Even;
- Close Half;
- Close;
- Modify SL;
- Modify TP;
- Edit.

Each family can be enabled, disabled, or used in simulation according to the available options. The dictionary accepts custom phrases.

For a command received from Telegram:

1. it must be sent as a reply to the parent signal;
2. the parent must be correlated with a batch on the active platform;
3. the symbol and batch must be identifiable without ambiguity;
4. Modify SL/TP commands must contain a usable new numeric value.

A half close is not executed if the remaining volume or volume to close is incompatible with the broker's minimum and volume step.

## 9. Configuring the Copy Trader

The Copy Trader enforces this route matrix:

| Source | Target | Allowed |
|---|---|---|
| MT5 | MT5 | Yes |
| MT5 | MT4 | Yes |
| Primary-module MT4 account | MT5 | Yes |
| MT4 | MT4 | No |

### 9.1 Choosing the source

Available modes depend on the source platform:

- enter an MT5 source account directly in the Copy Trader;
- select **USE METATRADER MODULE ACCOUNT**.

With MT5, the second mode shares the historical connected session. With MT4, it automatically loads the live identity and session from the primary module and only allows an MT5 target. The checkbox state is applied when the Copy Trader opens; clearing and rechecking it is not required.

### 9.2 Choosing the target

1. Select a target allowed by the route matrix: **MT5** or **MT4**.
2. Enter the login, password, and server.
3. Select the detected terminal.
4. Click **CONNECT**.
5. Wait for the `[TGT] Connected` message before starting the copy.

Once the connection is confirmed, the **CONNECT** button becomes **DISCONNECT**.

### 9.3 Setting the target volume

Three modes are available:

- `MULTIPLIER`: source volume × multiplier × Safety coefficient;
- `EQUITY_RATIO`: source volume × target/source equity ratio × Safety;
- `FIXED`: fixed lot × Safety.

The final volume is adapted to the target symbol's minimum, maximum, and step.

Options let you copy SL/TP and future pending orders. The alias file can enforce symbol mappings; NovaBOT can also learn some broker variants confirmed by the target.

### 9.4 Starting the copy safely

The **START COPYING** button is available only when the source and target are connected.

Before startup, NovaBOT examines source positions and pending orders, persistent mappings, and, when available, whether the associated tickets still exist on the target.

The summary distinguishes:

- open positions on the source;
- operations already copied;
- positions to synchronize;
- pending orders to synchronize;
- total volume to synchronize.

Three choices are offered when eligible operations exist:

#### Ignore already-open trades — recommended

NovaBOT records them as the known initial state without copying them. Only new trades opened after startup are copied.

#### Copy already-open trades

Only unmapped operations or operations whose target mapping has been confirmed stale are proposed. A second confirmation is required before they are immediately sent to the target.

Explicitly confirmed initial synchronization includes existing pending orders even if automatic copying of future pending orders is disabled.

#### Cancel

Copying does not start. Closing the window with the close button has the same result.

If all operations are already associated with the target, NovaBOT displays an information message and starts without sending a new open command.

### 9.5 While copying

The button becomes **STOP COPYING**. The console shows, among other messages:

- `[COPY→OPEN]` when an opening is requested;
- `[MAP]` after the target ticket is confirmed;
- volume, SL/TP, or pending-order changes;
- closes and deletions;
- errors returned by the target.

Use **STOP COPYING** before changing accounts, paths, or the target platform. Then use **DISCONNECT** to release the connections.

## 10. Installing the MT4 Bridge

The MetaTrader 4 module and an MT4 Copy Trader target use the `NovaBot_MT4_Slave_ZMQ.mq4` EA, mql-zmq components, and ZeroMQ DLLs.

### Installation from NovaBOT

1. Select **MT4** in the MetaTrader module, or choose an MT4 target in Copy Trader.
2. Select the exact installation.
3. Click **INSTALL MT4 BRIDGE**.
4. Confirm and do not touch windows or the mouse during automation.
5. Let NovaBOT locate the data folder, copy components, and open MetaEditor for compilation.
6. If several installations exist, verify that the selected terminal matches the intended data folder.

The script installs or updates:

- the NovaBOT EA in `MQL4/Experts`;
- the mql-zmq include files;
- the required DLLs;
- the associated third-party license.

### Compilation, attachment, and activation

NovaBOT attempts to compile the EA automatically in MetaEditor. Make sure `NovaBot_MT4_Slave_ZMQ` appears under **Expert Advisors** and no compilation error is shown. If the EA is not already attached:

1. enter login and password, then click **CONNECT METATRADER**;
2. open a chart for the relevant account;
3. double-click the EA;
4. allow DLL imports and live trading;
5. enable **AutoTrading**;
6. confirm and keep the chart active.

The console should show bridge preparation, waiting for the EA, then connection to the expected account. In the primary module, the header must show `MT4 • server • login`.

The first instance normally uses ports 6001/6002. If that pair belongs to another active NovaBOT instance, another free pair is allocated automatically and sent to the EA. NovaBOT does not deliberately close a visible instance’s bridge or an unrelated process.

After the project EA is updated, run installation again so the embedded version used by MT4 is recopied and recompiled.

## 11. Using NovaBOT Companion

NovaBOT Companion displays the status of a Desktop profile. It does not send Telegram, Smart, or MetaTrader commands.

### 11.1 Enabling monitoring on Desktop

1. Open NovaBOT **Settings**.
2. Under **Remote monitoring**, select **Enable monitoring transport**.
3. Choose **Private network** for a phone or another device.
4. Check the proposed IPv4 address.
5. Select the port, `8765` by default.
6. Token authentication is mandatory in private-network mode.
7. Confirm the settings.
8. Return to settings and check the status, URL, and token fingerprint.

Windows Firewall may require authorization or a local rule. NovaBOT never creates that rule automatically.

### 11.2 Pairing Companion

1. In Desktop settings, click **Show QR code**.
2. In NovaBOT Companion, open the option to add or pair a Desktop.
3. Scan the QR code.
4. Check the proposed profile and address.
5. Confirm pairing.

The QR code contains the private URL and authentication token. Do not share it or publish it in a channel or public screenshot.

### 11.3 Recommended network

The Desktop transport uses HTTP. Use:

- a private, trusted local network; or
- preferably, an encrypted private VPN when the phone is remote.

Do not directly expose the monitoring port to the Internet and do not configure public router port forwarding.

### 11.4 Token and reconnection

- **Copy token** places the token on the PC clipboard.
- **Regenerate token** invalidates earlier pairings.
- After regeneration, display a new QR code and pair again.

For multiple Desktop profiles, create a separate Companion pairing for each profile and each address/port used.

## 12. Using the Dashboard

The Dashboard displays statistics per Telegram source from events and operations attributable to the active MetaTrader platform. It remains passive: it analyzes archived evidence and never sends an order.

You can view:

- signals received and executed;
- success rate;
- confirmed net result;
- Profit Factor;
- realized drawdown;
- TP1 through TP4;
- Break Even and Smart Close events;
- average duration, streaks, and evidence coverage;
- source score and ranking.

Available periods include all data, 30 days, 7 days, and today. A reset per source creates a new statistical cutoff without deleting historical archives.

A result that cannot be attributed with sufficient confidence may remain unknown instead of being estimated.

## 13. General Settings and Interface

Settings include:

- automatically connect to Telegram at startup;
- **Connect to MetaTrader 5 (MT5) on startup**, whose historical label actually reconnects the platform saved in the profile, whether MT5 or MT4;
- main-window display mode;
- font size applied to modules and dialogs, except the profile manager;
- remote monitoring.

Available themes are light, dark, and light blue. Display modes resize the interface according to the available space. Telegram, MetaTrader, Dashboard, and Copy Trader each provide scroll bars when their content does not fit, preventing controls from being compressed.

The global console at the bottom of the main window shows two lines. Earlier messages remain available through its scroll bar; this console does not take priority over the useful height of the active module.

NovaBOT also adapts its window and tabs to screen and Windows scaling changes.

The **ℹ️ About** icon displays the version, build, copyright, GitHub link, and main third-party components.

## 14. Understanding Notifications

The main Telegram notifications distinguish:

- **Signal executed**: confirmed MARKET positions;
- **Signal placed**: accepted pending orders that have not triggered yet;
- **Signal partially executed**: only some orders succeeded;
- **Signal not executed**: no order was confirmed;
- **Pending order triggered**: the pending order became a position on the active platform;
- **TP reached and confirmed**: terminal evidence is available;
- **Break Even applied**: the SL change is confirmed;
- **LIMIT orders canceled**: deletion is confirmed by the active platform;
- **Trade finished**: no active operation remains in the monitored batch.

The price shown for a MARKET operation is its execution price. The price of a pending order is its placement price. A single signal keeps one common batch for its MARKET and LIMIT branches, including on MT4.

## 15. Recommended Daily Procedure

### At startup

1. launch the correct profile;
2. check Telegram and the active MetaTrader platform;
3. verify the correct account and server;
4. review reconnection or batch-restoration messages;
5. check the profile's Money Management;
6. enable listening only after these checks;
7. if using the Copy Trader, connect the source and target, then carefully review initial synchronization.

### Before a major change

1. stop Telegram listening;
2. stop copying;
3. export the profile;
4. change the settings;
5. test on a demo account;
6. progressively re-enable automations.

### Before closing NovaBOT

Check positions and orders directly in MT5 or MT4. Closing NovaBOT does not automatically close operations that are open with the broker.

## 16. Common Troubleshooting

### Telegram rejects the API ID/API Hash

- make sure the API ID is an integer;
- copy the API Hash without spaces;
- use **EDIT API ID / API HASH** while Telegram is disconnected.

### No messages are transferred

- check the Telegram connection;
- make sure the bot and private group exist;
- open **TELEGRAM TRANSFER** and check the selected sources;
- make sure listening is enabled;
- review the filtering reason in the console.

### The signal is transferred but not executed

- check the active-platform connection; for MT5, verify algorithmic trading; for MT4, verify the EA, bridge, DLL imports, and AutoTrading;
- read the displayed reason: parsing, symbol, distance, SL/TP geometry, volume, duplicate, trade limit, validation, or broker retcode;
- check the settings of the active profile;
- do not change a tolerance after the signal and use that new value to interpret a decision that was made earlier.

### Split entry is not applied

- make sure the correct group is enabled;
- check the minimum zone width;
- make sure the signal is not explicitly LIMIT or STOP;
- check that the volume per TP can produce two lots that satisfy the broker minimum.

### The MT4 target remains waiting

- make sure the MT4 terminal is open;
- make sure the EA is attached to a chart;
- enable automated trading and DLL imports;
- reinstall the bridge and recompile the EA with F7;
- wait for `[TGT] Connected` before starting the copy.

### The primary MT4 module remains disconnected

- make sure the selected path is the exact intended terminal;
- make sure the EA and ZeroMQ components are installed, then attach the EA to a chart;
- allow DLL imports and live trading, then enable **AutoTrading**;
- verify that the account observed by the bridge matches the requested login;
- wait for `MT4 • server • login` in the header: the terminal process alone is not a confirmed connection;
- if the bridge is incomplete, use **INSTALL MT4 BRIDGE** without touching windows or the mouse during automation.

### TPs are not adjusted to the actual entry

The option is deliberately safeguarded. It acts only while the current price is still unfavorable relative to the actual executed entry: below the entry for a BUY, or above the entry for a SELL. No adjustment is made if the price is equal or already favorable, the tick is unavailable, or the order is a STOP.

### Companion cannot connect

- make sure Desktop monitoring is running;
- check the displayed IPv4 address and port;
- make sure the phone can reach the same LAN or VPN;
- check Windows Firewall;
- pair again if the token was regenerated.

## 17. Glossary

| Term | Meaning |
|---|---|
| MARKET | Order executed at the available market price. |
| LIMIT | Pending order placed at a more favorable price. |
| STOP | Pending order placed in the direction of continued movement. |
| Pending | Order placed but not yet converted into a position. |
| TP | Take Profit, a price target. |
| SL | Stop Loss, a protection level. |
| BE | Break Even, with the SL moved around the entry price. |
| Batch | Set of positions and orders created from the same signal. |
| Mapping | Association between a source ticket and its target ticket in the Copy Trader. |
| Alias | Mapping between a common symbol name and its broker-specific name. |
| Retcode | Result code returned by MetaTrader. |
| Profile | Isolated environment containing accounts, settings, and histories. |

## 18. Essential Rule

When analyzing an event or incident, always use:

- the relevant profile;
- the settings saved when the signal was processed;
- the corresponding Telegram and MetaTrader-platform logs;
- tickets and retcodes confirmed by the terminal.

Behavior observed in one profile does not mean that other profiles use the same rules.
