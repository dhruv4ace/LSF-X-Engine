# 📘 LSF-X Engine  
**A Speed-Based, Least-Squares-Driven, Kalman-Enhanced Momentum EA for MetaTrader 5**

---

## 🔧 Strategy Overview

**LSF-X Engine** is a high-performance Expert Advisor for MetaTrader 5 that trades based on **momentum bursts**, **least-squares trend fitting**, and **Kalman-filtered signal smoothing**.

It integrates:

- 📈 **Rate Speed Analysis** – Captures rapid price accelerations
- 📉 **Least Squares Fitting (LSF)** – Derives trend direction with minimal noise
- 🔮 **Kalman Filter** – Smooths signals and filters false entries
- ⏱ **Multi-Timeframe Trend Confirmation** – Aligns signals across timeframes
- ⚙️ **Robust Trade Management** – SL, TP, Trailing Stop, and signal dashboard

Designed for fast-moving markets and clean directional setups.

---

## 📐 Math Behind the Strategy

### 1. Rate of Change Speed

Measures price change intensity over a fixed lookback window `n`:

Speedₜ = |Priceₜ - Priceₜ₋ₙ| / n

- Detects impulsive price moves
- Filters out sideways/ranging behavior

---

### 2. Least Squares Fitting (LSF) Slope

Fits a line to recent prices using least squares regression to find slope `m`:

#### Slope Formula:

m = [W * Σ(i × Pᵢ) - Σi × ΣPᵢ] / [W * Σ(i²) - (Σi)²]

Where:
- `W` is the window length
- `i` is the index (1, 2, ..., W)
- `Pᵢ` is the price at index `i`
A positive slope → uptrend
A negative slope → downtrend

---

### 3. Kalman Filter

A recursive estimator that smooths price series by balancing model trust and market noise.

#### Prediction:

x̂x̂ₖ⁻ = x̂ₖ₋₁
Pₖ⁻ = Pₖ₋₁ + Q

#### Update:

Kₖ   = Pₖ⁻ / (Pₖ⁻ + R)
x̂ₖ  = x̂ₖ⁻ + Kₖ × (zₖ - x̂ₖ⁻)
Pₖ  = (1 - Kₖ) × Pₖ⁻

Where:
- `x̂ₖ ` = Estimated state (filtered price)
- `zₖ ` = Observed price
- `Kₖ ` = Kalman gain
- `Q` = Process noise (model trust)
- `R` = Measurement noise (price trust)

This improves entry quality and reduces whipsaws.

---

### 4. Multi-Timeframe Confirmation

Before placing a trade, the EA verifies that a **higher timeframe** agrees with the current signal:

- Uses LSF + Kalman filter on MTF
- Confirms higher trend direction
- Prevents trading against large trend structure

---

## 📊 Strategy Logic

### ✅ Entry Criteria (Buy Example):

- `Speed` > `SpeedThreshold`
- `LSF Slope` > 0
- `Kalman Filter` is sloping up
- `MTF Trend` confirms long bias

### ❌ Exit Criteria:

- Stop Loss hit
- Take Profit hit
- Trailing Stop triggered
- Optional exit on signal flip (slope or Kalman reversal)

---

## ⚙️ Parameters

| Parameter        | Description                                    |
|------------------|------------------------------------------------|
| `SpeedPeriod`    | Bars used to calculate speed                   |
| `SpeedThreshold` | Minimum speed to trigger signal                |
| `LSF_Window`     | Lookback bars for trend slope via LSF         |
| `MTF_ConfirmTF`  | Higher timeframe to check trend confirmation   |
| `LotSize`        | Position size                                  |
| `StopLossPips`   | Stop loss distance in pips                     |
| `TakeProfitPips` | Take profit distance in pips                   |
| `TrailStopPips`  | Trailing SL distance (optional)                |
| `EnableKalman`   | Toggle Kalman filter usage                     |

---

## 📦 Architecture

- **Signal Engine**: Tick-level, with per-bar logic update
- **Kalman + LSF Calculation**: Updated once per new candle
- **MTF Analysis**: Optional, based on setting
- **Dashboard**: Displays signal state, LSF slope, speed, Kalman trend
- **Execution**: Single trade at a time, customizable risk and trade logic
