# Market Rotation Map (Asset Rotation / Relative Strength Cycle Map)

An educational web app that visualizes **relative strength** and **momentum** as a 2D “rotation” map (quadrant model), helping you observe how assets rotate through market cycles over time.

This project is built from first principles using standard math concepts:
- Relative Strength (RS) = Asset / Benchmark
- Trend Component (Ratio) = normalized RS vs moving average
- Momentum Component (Momentum) = normalized Ratio vs moving average

It supports:
- **Crypto** prices from Binance API (daily candles)
- **Stocks/ETFs** prices from Yahoo Finance Chart API **via a public CORS proxy**
- A benchmark of either:
  - **AVG** (portfolio average)
  - **BTC**
  - **USDT** (flat line)

> ⚠️ This project is for learning, research, and visualization only. It is NOT financial advice.

---

## Table of Contents
- [Features](#features)
- [How It Works (Methodology)](#how-it-works-methodology)
- [Quadrants](#quadrants)
- [Data Sources](#data-sources)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [Quick Start (Local)](#quick-start-local)
- [Step-by-Step: Run Locally](#step-by-step-run-locally)
- [Step-by-Step: Upload to GitHub](#step-by-step-upload-to-github)
- [Step-by-Step: Deploy (Vercel)](#step-by-step-deploy-vercel)
- [Configuration & Customization](#configuration--customization)
- [Troubleshooting](#troubleshooting)
- [Disclaimer](#disclaimer)
- [License](#license)
- [Credits](#credits)

---

## Features
- ✅ Real-time style playback across ~1000 days (scrub + play)
- ✅ Portfolio benchmark options:
  - `AVG` (average of loaded assets)
  - `BTC`
  - `USDT`
- ✅ Interactive chart:
  - Zoom in/out
  - Pan (drag)
  - Trails (lookback history points)
- ✅ Quadrant classification:
  - Leading, Weakening, Lagging, Improving
- ✅ Statistical analysis modal per asset:
  - Average duration per quadrant
  - Average return per quadrant
  - Average max drawdown in quadrant
  - Win rate
- ✅ Add/remove assets dynamically (crypto or stock)

---

## How It Works (Methodology)

### 1) Relative Strength (RS)
We compute Relative Strength (RS) as:

```

RS = Price(Asset) / Price(Benchmark)

```

This tells whether an asset is outperforming or underperforming the benchmark.

### 2) Trend Component (Ratio) → X-axis
We normalize RS against its own moving average to create a trend-like component:

```

Ratio = 100 * ( RS / MA(RS, n) )

```

Interpretation:
- `Ratio > 100` means **outperforming** relative to its recent average
- `Ratio < 100` means **underperforming**

### 3) Momentum Component (Momentum) → Y-axis
We normalize the Ratio against its own moving average:

```

Momentum = 100 * ( Ratio / MA(Ratio, n) )

```

Interpretation:
- `Momentum > 100` means **positive acceleration**
- `Momentum < 100` means **deceleration**

### Moving Average Type
This implementation uses **Weighted Moving Average (WMA)**:
- More weight on recent observations
- Common in technical analysis contexts

---

## Quadrants

The chart is split by `Ratio = 100` (vertical center line) and `Momentum = 100` (horizontal center line).

| Quadrant | Condition | Meaning |
|---------|----------|---------|
| **Leading** | Ratio ≥ 100 and Momentum ≥ 100 | Strong trend + strong momentum |
| **Weakening** | Ratio ≥ 100 and Momentum < 100 | Trend still strong, momentum fading |
| **Lagging** | Ratio < 100 and Momentum < 100 | Weak trend + weak momentum |
| **Improving** | Ratio < 100 and Momentum ≥ 100 | Trend still weak, momentum recovering |

---

## Data Sources

### Crypto (Binance)
- Endpoint: `https://api.binance.com/api/v3/klines`
- Interval: `1d`
- Limit: `1000`
- Example pair format: `BTCUSDT`, `ETHUSDT`
- Data used: **close price**

### Stocks/ETFs (Yahoo Finance via Proxy)
Because browsers block Yahoo requests due to CORS, this project uses a public proxy:
- Yahoo endpoint: `https://query1.finance.yahoo.com/v8/finance/chart/{SYMBOL}`
- Proxy: `https://api.allorigins.win/raw?url=...`

Yahoo request used:
- interval: `1d`
- range: `5y`
- Then the app takes the **last ~1000 close prices** (filtered for nulls)

> ⚠️ Public proxies can be rate-limited or blocked. See [Troubleshooting](#troubleshooting).

---

## Project Structure (Typical Vite + React)

Recommended structure:

```

market-rotation-map/
├─ index.html
├─ package.json
├─ vite.config.js
├─ src/
│  ├─ main.jsx
│  ├─ MarketRotationApp.jsx
│  └─ index.css
└─ public/


