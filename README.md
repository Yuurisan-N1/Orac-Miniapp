<div align="center">

<img width="100%" alt="header" src="https://capsule-render.vercel.app/api?type=waving&height=210&text=Orac%20AI%20Bot&fontAlign=50&fontAlignY=36&fontSize=56&desc=Human%20Check%20%7C%20Daily%20Check-In%20%7C%20Lucky%20Wheel&descAlign=50&descAlignY=58"/>

<img alt="typing" src="https://readme-typing-svg.demolab.com?font=Inter&size=18&duration=3000&pause=650&center=true&vCenter=true&width=900&lines=Telegram+initData+Login;Human+Verification;Daily+Check-In+%7C+Lucky+Wheel;Mining+Claim+%7C+Task+Board;Multi-Account+%7C+Sequential+Processing"/>

<p>
  <img alt="elixir" src="https://img.shields.io/badge/Elixir-1.14%2B-4B275F?logo=elixir&logoColor=white"/>
  <img alt="platform" src="https://img.shields.io/badge/Platform-ORAC%20AI%20Miniapp-111111"/>
  <img alt="multi-account" src="https://img.shields.io/badge/Multi--Account-Supported-111111"/>
  <img alt="proxy" src="https://img.shields.io/badge/Proxy-Supported-111111"/>
  <img alt="author" src="https://img.shields.io/badge/by-Yuurisandesu-111111"/>
</p>

<p>
  <b>ORAC AI Bot</b> is a full automation bot for the ORAC AI Telegram Miniapp.<br/>
  It logs every account in with the Telegram <code>initData</code> from <code>data.txt</code>, confirms the human verification, rechecks the community membership, collects the daily check-in, drains the lucky wheel, claims the pending mining balance, walks the task board, reads the referral tree and reports the withdrawal state.<br/>
  Built and distributed by <b>Yuurisandesu</b>.
</p>

</div>

---

## Table of Contents

- [Requirements](#requirements)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Bot](#running-the-bot)
- [Features](#features)
- [File Structure](#file-structure)
- [Disclaimer](#disclaimer)

---

## Requirements

- Elixir `1.14+` with Erlang/OTP `25+`
- Git

---

## Installation

### Install Elixir

Elixir runs on the Erlang virtual machine, so **Erlang/OTP is required** - Elixir cannot run without
it. Every package manager below installs Erlang/OTP for you as a dependency. The only path that needs
a separate Erlang step is the Windows manual installer (see below).

**Windows:**

*Option A - package manager (recommended, installs Erlang automatically):*

```cmd
winget install Elixir.Elixir
```

*Option B - manual installer (two installers, in this order):*

1. Download and run the **Erlang/OTP installer** from https://www.erlang.org/downloads
2. Run `erl -s halt` to check which OTP version you got
3. Download the **Elixir installer built for that exact OTP** from https://elixir-lang.org/install.html (`elixir-otp-<N>.exe`) and run it

Then open Command Prompt and verify:

```cmd
elixir --version
```

The output prints both versions, for example `Elixir 1.14.0 (compiled with Erlang/OTP 25)`.

**Linux (Ubuntu / Debian):**

```bash
sudo apt-get update
sudo apt-get install -y elixir
elixir --version
```

**macOS:**

```bash
brew install elixir
elixir --version
```

> If you do not have Homebrew, install it first from https://brew.sh

**Termux (Android):**

```bash
pkg update && pkg upgrade -y
pkg install elixir -y
elixir --version
```

> If the `elixir` package is not available in your Termux repository, install an Ubuntu environment first with `pkg install proot-distro`, then `proot-distro install ubuntu`, then `proot-distro login ubuntu` and run the Linux command above inside it.

---

### Clone and Install

**Clone the repository:**

```bash
git clone https://github.com/Yuurisan-N1/ORAC-Miniapp.git
cd ORAC-Miniapp
```

**Install dependencies:**

This bot uses only the Elixir standard library, so there is nothing to install. Erlang/OTP was already installed by the step above.

---

## Configuration

### 1. Accounts (data.txt)

Fill `data.txt` with the Telegram WebApp `initData` of each account, one per line. An optional wallet address can be appended after a `|` separator when the account should withdraw:

```
user=%7B%22id%22...&hash=abc123
user=%7B%22id%22...&hash=def456|0x0000000000000000000000000000000000000000
```

> `initData` can be obtained from the browser DevTools when opening the ORAC AI Miniapp on Telegram Web.

### 2. Proxy (proxy.txt)

Fill `proxy.txt` with proxies, one per line (optional, leave empty to run without proxy):

```
host:port
host:port:user:pass
http://user:pass@host:port
```

Proxies are assigned to accounts by index in round-robin order. Proxy credentials are masked in the log.

### 3. Bot Settings (config.json)

`sleep_seconds` controls how many seconds the bot waits between cycles. If `config.json` is missing, it is created automatically with a default of `3600` seconds.

```json
{
  "settings": {
    "sleep_seconds": 3600
  }
}
```

---

## Running the Bot

**Linux / macOS:**

```bash
elixir --erl "+Bd" bot.exs
```

**Windows:**

```cmd
elixir --erl "+Bc" bot.exs
```

Or just double click `run_pc.bat`, which runs the Windows command above.

---

## Features

### Account Login
Every account is opened with the Telegram `initData` from `data.txt`. The bot exchanges it for a session token, reads the profile and logs the account name together with the current ORAC balance.

### Human Verification
The human verification state is confirmed with the server on every pass, so the account stays eligible for the reward flow.

### Channel Membership
The community membership is rechecked before the reward phases run, so the account does not lose its eligibility silently.

### Daily Check-In
The check-in state is read before it is claimed. When the day is still open the claim is sent and the credited ORAC is logged; an already collected day is reported once and skipped.

### Lucky Wheel
The wheel configuration is read for the number of available spins. Every available spin is used one by one, and each prize is logged as it lands.

### Mining Claim
The pending mining balance is read first. A balance below the minimum claim size is skipped for that cycle, otherwise the claim is sent and the credited amount is logged.

### Task Board
Every task that is not completed yet is claimed one by one. Each claimed task is logged with its title and its reward, and refused tasks are skipped instead of stopping the cycle.

### Referral Tree
The referral tree is read and the number of confirmed friends is logged. An account without referrals is reported once and skipped.

### Withdrawal Report
The wallet summary is read before anything is requested. An account without a wallet address is skipped, a balance below the minimum stays locked, and a valid request is submitted for the reported amount.

### Multi Account
All accounts in `data.txt` are processed sequentially within every cycle. Each account is logged with its index, its name and its balance. The cycle number is tracked and logged at the start of each round.

### Proxy Support
Proxies are loaded from `proxy.txt` and assigned to accounts by position in round-robin order. Running without proxies is fully supported.

### Auto Countdown
After all accounts complete a cycle the bot shows a live `HH:MM:SS` countdown until the next cycle starts.

---

## File Structure

```text
ORAC-Miniapp-Elixir/
├── bot.exs         # Main bot, full ORAC AI cycle automation
├── config.json     # Sleep duration between cycles
├── data.txt        # Account initData, one per line
├── proxy.txt       # Proxy list, one per line (optional)
├── run_pc.bat      # Launcher for Windows
├── LICENSE         # License file
└── utils/
    ├── banner.ex   # Banner display on startup
    └── json.ex     # JSON encoder and decoder
```

---

## Disclaimer

This tool is built for educational and technical exploration purposes. Use it wisely and at your own responsibility.

---

<div align="center">
<img width="100%" alt="footer" src="https://capsule-render.vercel.app/api?type=waving&height=120&section=footer"/>
</div>
