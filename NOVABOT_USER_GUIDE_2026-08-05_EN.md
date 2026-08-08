# NovaBOT User Guide

🇫🇷 **French version:** [NOVABOT_USER_GUIDE_2026-08-05_FR.md](NOVABOT_USER_GUIDE_2026-08-05_FR.md)

---

# NovaBOT User Guide

Date: August 5, 2026  
Audience: NovaBOT Desktop and NovaBOT Companion users

## 1. Overview

This guide walks new users through the process from the first time they open NovaBOT to its day-to-day use.

Each chapter follows the recommended setup order.

NovaBOT is a Windows application that connects Telegram to MetaTrader 5. It can listen to Telegram groups or channels, recognize their trading signals, apply the rules of the active profile, execute orders on MT5, monitor their lifecycle, and publish the results to a private Telegram group.

The application includes four main modules:

- **Telegram**: connection, source selection, filtering, and listening;
- **MetaTrader 5**: broker connection, Money Management, execution, and monitoring;
- **Dashboard**: statistics based on MT5 executions and evidence;
- **Copy Trader**: copying from an MT5 source account to an MT5 or MT4 target.

NovaBOT Companion lets you remotely view NovaBOT's status. Companion is a read-only monitoring tool: it cannot send trading orders.

> **Important** — NovaBOT can automatically send real orders. Always perform initial tests on demo accounts and check volumes, the broker symbol, Stop Loss, and Take Profits before enabling listening.

## 2. Requirements

Before you begin, prepare:

- a Telegram account accessible from the official application;
- an `API ID` and `API Hash` obtained from `my.telegram.org`, under **API development tools**;
- a working MetaTrader 5 installation;
- the login, password, and server for the MT5 account;
- algorithmic trading authorization in MT5;
- for an MT4 target: an MT4 installation and DLL imports enabled for the NovaBOT EA.

The MetaTrader terminal must be able to connect to the selected account and display the broker's symbols. Names may vary by broker, for example `XAUUSD`, `XAUUSD-VIP`, or another variant.

## 3. Getting Started

For an initial setup, follow this order:

1. create a NovaBOT profile;
2. connect Telegram;
3. create the profile's bot and private Telegram group;
4. select sources in **TELEGRAM TRANSFER**;
5. configure Telegram filtering;
6. connect the MT5 account;
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
- MT5 shows the correct login and server;
- algorithmic trading is allowed;
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
- MT5 account;
- selected Telegram sources;
- filtering rules;
- Money Management settings;
- Smart command dictionary;
- histories, mappings, statistics, and monitoring settings.

A change made in one profile should therefore not be considered active in other profiles.

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

When Telegram, the bot, the private group, the sources, and MT5 are ready, click **ENABLE LISTENING**.

NovaBOT then transfers admitted messages to the private group and submits recognized signals to the MT5 pipeline. Click again to disable listening.

### 5.7 Manually retrieving an earlier signal

The **RETRIEVE A TRADE FROM A MESSAGE** button lets you browse a conversation, select an earlier message, and send it through the processing pipeline.

This action remains subject to the rules of the active profile. The tolerances and options used are those saved when the message is retrieved.

## 6. Connecting MetaTrader 5

### 6.1 Preparing MT5

Before connecting NovaBOT:

1. install and open the broker's MT5 terminal;
2. make sure the account can connect in MT5;
3. enable algorithmic trading;
4. display the required symbols in Market Watch.

### 6.2 Connecting from NovaBOT

1. Open the **MetaTrader 5** tab.
2. Select the detected terminal path. Use the selector if several installations exist.
3. Enter the login, password, and server.
4. Click **DISCOVER SERVERS** if the server is not listed.
5. Click **CONNECT METATRADER**.
6. In the console, check the account name, login, server, balance, and algorithmic-trading status.

NovaBOT then monitors the terminal and updates the interface if the connection is lost.

### 6.3 Broker symbols and aliases

After connecting:

- **COLLECT SYMBOLS** records information about the available symbols;
- **GENERATE ALIASES (BROKER)** builds mappings between common names and broker-specific names.

Use these functions if Telegram publishes `GOLD` or `XAUUSD` while the broker uses a specific suffix. Always check the symbol shown in the execution preview.

### 6.4 Testing without Telegram

The MT5 manual-entry area lets you submit signal text to the same parser and execution pipeline. Use a demo account and a minimum lot to validate aliases and Money Management.

## 7. Configuring Money Management

Open the MT5 tab and click **MONEY MANAGEMENT**. Settings are profile-specific.

The `?` icons provide a description of the corresponding setting.

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

Check the four displayed values: real MT5 balance, virtual capital, protected capital, and risk calculation base.

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

Split entry is configured separately for each group selected in **TELEGRAM TRANSFER**.

For an admissible and sufficiently wide MARKET zone, NovaBOT can create:

- a MARKET branch;
- a LIMIT branch inside the zone.

The available volume is split between the two branches. If the lot for each TP cannot produce two volumes that comply with the broker minimum, split entry is not applied.

**Allow LIMIT orders after TP1** determines what happens to sibling LIMIT orders:

- option disabled: LIMIT orders that have not triggered are canceled after MT5 confirms MARKET TP1;
- option enabled: LIMIT orders remain active.

## 8. Configuring Smart Commands

Click **TELEGRAM AUTOMATION** in the MT5 module.

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
2. the parent must be correlated with an MT5 batch;
3. the symbol and batch must be identifiable without ambiguity;
4. Modify SL/TP commands must contain a usable new numeric value.

A half close is not executed if the remaining volume or volume to close is incompatible with the broker's minimum and volume step.

## 9. Configuring the Copy Trader

The Copy Trader uses an MT5 source and an MT5 or MT4 target.

### 9.1 Choosing the source

Two modes are available:

- enter an MT5 source account directly in the Copy Trader;
- select **USE THE MT5 MODULE ACCOUNT**.

The second mode is recommended when the source is the same account already connected in the MT5 module. It shares the existing session and avoids two competing initializations of the same terminal.

### 9.2 Choosing the target

1. Select the target platform: **MT5** or **MT4**.
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

The MT4 target uses the `NovaBot_MT4_Slave_ZMQ.mq4` EA and mql-zmq components.

### Installation from NovaBOT

1. Open the **Copy Trader** tab.
2. Select **MT4** as the target platform.
3. Click **INSTALL MT4 BRIDGE**.
4. Confirm the installation.
5. Let the PowerShell script automatically search for MT4 data folders.
6. If it finds several installations, select the one matching the target terminal.
7. If no installation is detected, manually enter the requested data folder.

The script installs or updates:

- the NovaBOT EA in `MQL4/Experts`;
- the mql-zmq include files;
- the required DLLs;
- the associated third-party license.

### Compilation and activation

1. Open MT4 and then MetaEditor.
2. Open `NovaBot_MT4_Slave_ZMQ.mq4`.
3. Compile with **F7**.
4. Make sure there are no compilation errors.
5. Attach the EA to a chart.
6. Enable automated trading and DLL imports.
7. Keep the chart and EA active.

The console should successively show bridge preparation, waiting for the EA, and then `[TGT] Connected`.

The bridge uses local ports 6001 and 6002. NovaBOT can recover the port from an old invisible NovaBOT worker when it can identify it with sufficient confidence. It does not intentionally close a visible instance or an unrelated process.

After the EA in the project folder is updated, run the installation again and recompile the EA in MetaEditor so MT4 uses the new version.

## 11. Using NovaBOT Companion

NovaBOT Companion displays the status of a Desktop profile. It does not send Telegram, Smart, or MT5 commands.

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

The Dashboard displays statistics per Telegram source based on attributable MT5 events and deals.

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
- automatically connect to MetaTrader 5 at startup;
- display mode;
- font size;
- remote monitoring.

Available themes are light, dark, and light blue. NovaBOT adapts its window and tabs to screen and Windows scaling changes.

The **ℹ️ About** icon displays the version, build, copyright, GitHub link, and main third-party components.

## 14. Understanding Notifications

The main Telegram notifications distinguish:

- **Signal executed**: confirmed MARKET positions;
- **Signal placed**: accepted pending orders that have not triggered yet;
- **Signal partially executed**: only some orders succeeded;
- **Signal not executed**: no order was confirmed;
- **TP reached and confirmed**: MT5 evidence is available;
- **Break Even applied**: the SL change is confirmed;
- **LIMIT orders canceled**: deletion is confirmed by MT5;
- **Trade finished**: no active operation remains in the monitored batch.

The price shown for a MARKET operation is its execution price. The price of a pending order is its placement price.

## 15. Recommended Daily Procedure

### At startup

1. launch the correct profile;
2. check Telegram and MT5;
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

- check the MT5 connection and algorithmic trading;
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
- the corresponding Telegram and MT5 logs;
- tickets and retcodes confirmed by the terminal.

Behavior observed in one profile does not mean that other profiles use the same rules.
