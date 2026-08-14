# 🚀 NovaBOT Desktop

🇫🇷 **Français :** [README.fr.md](README.fr.md)  
📘 **Detailed user guide:** [NOVABOT_USER_GUIDE_2026-08-05_EN.md](NOVABOT_USER_GUIDE_2026-08-05_EN.md)

---

**NovaBOT** is a Python Windows application that automates trading-signal processing between **Telegram** and **MetaTrader 5 or MetaTrader 4**.

It monitors Telegram groups and channels, filters and interprets their messages, applies the active profile’s rules, executes orders on the selected platform, monitors their lifecycle, publishes notifications to a private group, and feeds a passive analytics Dashboard.

> **Important** — NovaBOT can automatically send real orders. Initial tests should always be performed on a demo account with controlled volumes.

---

# ✨ Features

## 🧭 Setup Assistant

An optional checklist guides new-profile setup without replacing the existing modules.

It automatically verifies:

- Telegram API credentials;
- complete Telegram connection;
- the NovaBOT bot and private group;
- Telegram groups to monitor;
- Telegram filtering;
- MetaTrader connection;
- saved Money Management settings;
- prepared Telegram listening.

Progress is persisted. Users may skip guided setup, resume it later, and subsequently open **Profile status**. The status button becomes red, orange, or green according to progress.

---

## 📡 Telegram Integration

- Telethon connectivity;
- assisted bot and private-group creation;
- confirmation that the bot was added as administrator;
- multi-group and multi-channel selection;
- manual transfer or automatic listening;
- configurable filtering with rejection reasons;
- edited- and deleted-message detection;
- correction of an already-processed Telegram signal without recreating its whole batch;
- manual retrieval of an earlier signal;
- durable notifications with retry delivery;
- mandatory correlation between a Smart command and its parent message.

---

## 📈 MetaTrader 5 and MetaTrader 4 Trading

- explicit platform selection followed by installation selection;
- automatic MT5 and MT4 terminal discovery;
- server catalog grouped by broker entity;
- multi-broker connection;
- symbol resolution and broker-alias generation;
- MARKET, LIMIT, and STOP order execution;
- multi-TP and multi-position management;
- automatic MARKET → LIMIT conversion according to profile rules;
- clearly separated MT4/MT5 execution engines and lifecycles;
- one common batch for MARKET and LIMIT branches from the same signal;
- position, pending-order, modification, and close monitoring;
- assisted MT5 algorithmic-trading activation;
- optimized startup without systematic symbol recollection.

### MT5 Path

NovaBOT uses the MetaTrader 5 Python connection, actual symbol metadata, terminal retcodes, and MT5 history to execute and characterize operations.

### MT4 Path

MT4 uses the `NovaBot_MT4_Slave_ZMQ` EA, mql-zmq, and ZeroMQ DLLs. Connection is confirmed only after `READY` is received and the observed login is verified. The account is started with a temporary profile-scoped INI file; pywinauto is reserved for assisted bridge installation and compilation.

---

## 💰 Money Management

- fixed lot, lot per TP, or percentage risk;
- real capital, virtual capital, and protection vault;
- broker-compliant volume distribution and normalization;
- manual entry tolerance or automatic tolerance by asset family;
- automatic LIMIT creation when the price gap is high;
- duplicate prevention and maximum-trade limit;
- optional pending-order counting in that limit;
- unconditional execution configurable by group;
- Break Even, automatic protection, and progressive partial closes;
- TP offset for all groups or per group;
- safeguarded TP adjustment to the actual executed entry;
- split entry for all groups or configured per group;
- cancellation or preservation of sibling LIMIT orders after TP1;
- moving first-entry TPs to entry when the second entry triggers;
- restoration of default values after confirmation.

The profile’s Money Management configuration is the source of truth for admission and position sizing.

---

## ⚖️ Signal Validation

Validation operates on the resolved broker symbol and data available from the active platform. It checks SL/TP geometry, volume, risk, and available market conditions.

Possible decisions:

- `ALLOW`: execution at the planned volume;
- `REDUCE`: execution with an effective reduction;
- `BLOCK`: no order is sent.

Market thresholds, including spread limits, account for the main asset families: Forex, metals, indices, cryptocurrencies, and energies.

---

## 🤖 Smart Automations

NovaBOT recognizes Telegram commands sent as replies to the parent signal:

- Break Even;
- Secure;
- Close;
- Close Half;
- Modify Stop Loss;
- Modify Take Profit;
- Edit.

Each family has its own dictionary and can be enabled, disabled, or simulated according to available settings. Actions pass through the active platform gateway and must be confirmed by the terminal.

---

## 📊 Lifecycle and Notifications

NovaBOT reconstructs the batch lifecycle from evidence supplied by the active platform:

- pending-order trigger;
- Take Profit and Stop Loss;
- Break Even and secured SL;
- manual close;
- Smart Close and Close Half;
- progressive partial close;
- pending-order modification or deletion;
- mixed causes and trade completion.

MARKET and LIMIT branches from the same signal remain attached to one batch. The final notification is produced only when no active operation remains.

---

## 📊 Analytics Dashboard

The Dashboard is a passive consumer of archived evidence. It never sends an order.

It displays, among other metrics:

- signals received and executed;
- confirmed success rate and net result;
- Profit Factor and realized drawdown;
- TP1 through TP4, Break Even, and Smart Close;
- average duration and streaks;
- evidence coverage;
- a 100-point score and star rating;
- independent statistics reset per source.

A result that cannot be attributed with sufficient confidence remains unknown instead of being estimated.

---

## 🔄 Copy Trader

The Copy Trader supports the following routes:

| Source | Target | Supported |
|---|---|---|
| MT5 | MT5 | Yes |
| MT5 | MT4 | Yes |
| Primary-module MT4 account | MT5 | Yes |
| MT4 | MT4 | No |

Main features:

- direct source-account entry or reuse of the MetaTrader module account;
- position and pending-order copying;
- lot multiplier, equity ratio, or fixed lot;
- target-volume normalization;
- optional SL/TP copying;
- symbol aliases and learning of target-confirmed broker variants;
- persistent mappings with target-existence checks;
- initial synchronization safety: ignore, copy after double confirmation, or cancel;
- opening-time duplicate protection remains active;
- open, modify, close, and delete monitoring.

When several NovaBOT instances use MT4, a free port pair is allocated without closing the bridge of another visible instance.

---

## 👤 Isolated Profiles

Each profile has its own:

- Telegram credentials and sessions;
- MetaTrader platform and account;
- selected Telegram sources;
- filtering rules and Smart dictionaries;
- Money Management settings;
- aliases, mappings, histories, logs, and analytics databases;
- monitoring settings.

NovaBOT supports ZIP import/export, active-profile locking, `--profile` launch, and simultaneous use of multiple profiles.

---

## 📱 Remote Supervision

- read-only HTTP transport;
- compatible with NovaBOT Companion;
- private LAN or private VPN;
- mandatory Bearer authentication in private-network mode;
- QR code pairing;
- sensitive-data masking;
- no remote commands.

---

## 🌍 User Interface

- PyQt5;
- Light, Dark, and Light Blue themes;
- 10 languages;
- 6 display modes;
- 3 font sizes;
- common adaptation for modules and dialogs;
- independent scroll bars for Telegram, MetaTrader, Dashboard, and Copy Trader;
- global console limited to two visible lines with scrollable history;
- Settings and About icons in the modules.

---

# 🛠 Main Technologies

- Python;
- PyQt5;
- MetaTrader5;
- MQL4;
- Telethon;
- ZeroMQ / mql-zmq;
- SQLite;
- AsyncIO;
- pywinauto, only for assisted MT4 bridge installation.

---

# 📂 Functional Architecture

```text
Telegram
   ↓
Filtering and correlation
   ↓
Signal parser
   ↓
Broker-symbol resolution
   ↓
Admission + Money Management + Validation
   ↓
MT5 engine or MT4 engine
   ↓
MetaTrader terminal
   ↓
Platform lifecycle
   ├──→ Durable Telegram notifications
   ├──→ Analytics Dashboard
   └──→ Read-only supervision
```

Common rules remain separated from platform-specific transports. MT5 uses its native Python connection; MT4 communicates with the EA through the ZeroMQ bridge.

---

# 📌 Project Status

NovaBOT is actively developed with a focus on stability, performance, terminal-evidence traceability, and consistent behavior across MT4 and MT5.

Actual behavior always depends on the active profile, its saved settings, the selected platform, and information returned by the broker.

---

## 🤝 Third-party Components

NovaBOT notably uses:

- mql-zmq — Apache License 2.0;
- ZeroMQ;
- Telethon;
- PyQt5.

---

# 📄 License

No redistribution license is currently defined. Until an official license is published, **all rights are reserved**.

---

# ⚠️ Disclaimer

NovaBOT is provided **“as is”**, without any warranty of functionality, reliability, or fitness for a particular purpose.

Use of the software is entirely at the user’s own risk. The author shall not be liable for financial loss, data loss, business interruption, or any other direct or indirect consequence.

Trading involves significant risk. NovaBOT is a software tool and does not constitute financial or investment advice. All trading decisions remain the sole responsibility of the user.
