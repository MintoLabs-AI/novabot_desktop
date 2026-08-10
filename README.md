# 🚀 NovaBot Desktop

> 🇫🇷 **French:** See the French version here: **[README.fr.md](README.fr.md)**

**NovaBot** is a Python desktop application that automates trading between **Telegram** and **MetaTrader 5**.

It detects trading signals published on Telegram, filters and interprets them, executes them automatically on MetaTrader 5, reconstructs the complete trade lifecycle, powers an analytics dashboard based exclusively on MT5 trading evidence, and includes an integrated MT5/MT4 Copy Trading engine.

---

# ✨ Features

## 📡 Telegram Integration

* Telethon connectivity
* Multi-group and multi-channel support
* Intelligent message filtering
* Automatic trading signal detection
* Signal reply management
* Automatic private bot creation
* Automatic private Telegram group creation
* MT5 notification synchronization

---

## 📈 MetaTrader 5 Trading

* Multi-broker support
* Automatic MT5 terminal discovery
* Automatic server discovery
* MARKET order execution
* LIMIT order execution
* STOP order execution
* Multi Take-Profit management
* Multi-position management
* Automatic Market → Limit conversion
* Duplicate trade prevention
* Optional market validation
* Assisted AutoTrading activation
* Complete trade lifecycle reconstruction
* Intelligent symbol cache
* Optimized startup

---

## 💰 Money Management

* Fixed lot sizing
* Individual lot size per TP
* Risk-based position sizing (%)
* Real account balance
* Virtual capital
* Capital protection vault
* Automatic volume distribution
* Advanced entry zone management
* Slippage tolerance
* Maximum position control

---

## 🤖 Smart Automations

NovaBot automatically recognizes several trading commands received through Telegram.

Including:

* Break Even
* Secure
* Close
* Close Half
* Modify Stop Loss
* Modify Take Profit
* Edit

Each command can be enabled or disabled independently.

---

## 📊 Analytics Dashboard

* Automatic Telegram group analysis
* Statistics based exclusively on MT5 executions and deals
* Confirmed win rate
* Confirmed net profit
* Profit Factor
* Maximum realized drawdown
* TP1 to TP4 tracking
* Break Even statistics
* Smart Close statistics
* Winning and losing streaks
* Average trade duration
* 100-point performance score
* Star ranking
* Independent statistics reset per Telegram group

---

## 📊 Trade Lifecycle Tracking

NovaBot automatically reconstructs the complete lifecycle of every trade.

It detects:

* Take Profit
* Stop Loss
* Break Even
* Secure Stop
* Manual close
* Smart Close
* Smart Close Half
* Progressive partial close
* Mixed closing reasons

All notifications are automatically published to Telegram.

---

## 🔄 Copy Trader

NovaBot includes a built-in Copy Trading engine.

### Source

* MetaTrader 5

### Targets

* MetaTrader 5
* MetaTrader 4

### Features

* Position copying
* LIMIT order copying
* Lot multiplier
* Equity ratio
* Fixed lot sizing
* Automatic symbol mapping

---

## 👤 Profile Management

Each profile runs in its own isolated environment.

A profile includes:

* Settings
* Telegram sessions
* MT5 accounts
* Trade history
* Logs
* Symbol aliases

Multiple NovaBot instances can run simultaneously using different profiles.

Additional features:

* Profile Import / Export
* Profile locking
* Command-line launch (`--profile`)

## 📱 Remote Supervision

* Read-only HTTP supervision
* Compatible with NovaBOT Companion
* Local network and private VPN support
* Bearer authentication
* QR code pairing
* No remote commands

---

## 🌍 User Interface

* PyQt5
* Light, Dark and Light Blue themes
* 10 languages
* 6 display modes
* 3 font sizes
* Adaptive interface
* Unified console
* Independent modules

---

# 🛠 Technologies

* Python
* PyQt5
* MetaTrader5
* Telethon
* ZeroMQ
* SQLite
* pywinauto
* AsyncIO

---

# 📂 Architecture

NovaBot is organized around four main modules:

```text
Telegram
     │
     ▼
Smart Filter
     │
     ▼
Signal Parser
     │
     ▼
MetaTrader 5
     │
     ▼
Trade Lifecycle
     ├────────► Analytics Dashboard
     │
     ▼
Telegram Notifications
```

The Copy Trading engine operates independently.

---

# ⚙️ Core Capabilities

* Intelligent signal parsing
* Automatic symbol aliases
* Multi Take-Profit support
* Multi-profile architecture
* Multi-broker support
* Copy Trading
* Smart Automations
* Automatic Break Even
* Automatic Secure
* Progressive partial closing
* Complete trade lifecycle tracking
* Market validation
* Telegram ↔ MT5 synchronization
* Analytics Dashboard
* Automatic Telegram group rating
* Multi-instance support

---

# 📌 Project Status

NovaBot is a personal Python project designed to fully automate trading between Telegram and MetaTrader 5.

It features an advanced signal parser, complete trade lifecycle tracking, an analytics dashboard based exclusively on MT5 trading evidence, and an integrated MT5/MT4 Copy Trading engine.

The project is actively maintained, with the current focus on stability, performance, reliability, and analytics quality rather than introducing new features.

---

## Optional prerequisite — Automatic Algorithmic Trading activation

The Microsoft Visual C++ Redistributable may be required to use NovaBOT's
Automatic Algorithmic Trading activation feature.

Official Microsoft downloads:

- Visual C++ Redistributable (x64)
- Visual C++ Redistributable (x86)

These components are not required for NovaBOT itself and should only be
downloaded directly from Microsoft.

---

### 🤝 Third-party Components

NovaBOT uses several open-source components:

* mql-zmq (Apache License 2.0)
* ZeroMQ
* Telethon
* PyQt5

---

# 📄 License

No license has been defined yet.

Until an official license is published, **all rights are reserved**.

---

# ⚠️ Disclaimer

This project is publicly available for inspection, learning, and improvement purposes.

NovaBOT is provided **"as is"**, without any warranty of any kind, whether express or implied, including but not limited to warranties of functionality, reliability, or fitness for a particular purpose.

The use of this software is entirely at the user's own risk. The author shall not be held liable for any financial loss, data loss, hardware damage, business interruption, or any direct or indirect damages resulting from the use of NovaBOT.

Trading involves significant financial risk. NovaBOT is a software tool only and does **not** constitute financial or investment advice. All trading decisions remain the sole responsibility of the user.

No support, warranty, or liability is provided by the author.
