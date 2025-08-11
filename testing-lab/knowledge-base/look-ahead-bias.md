# Look-Ahead Bias in TradingView Pine Script: Complete Guide

## Table of Contents
1. [Introduction](#introduction)
2. [What is Look-Ahead Bias?](#what-is-look-ahead-bias)
3. [Common Causes in Pine Script](#common-causes-in-pine-script)
4. [Code Examples](#code-examples)
5. [Prevention Techniques](#prevention-techniques)
6. [Best Practices](#best-practices)
7. [Testing and Validation](#testing-and-validation)

## Introduction

Look-ahead bias is one of the most dangerous pitfalls in algorithmic trading strategy development. It occurs when a trading strategy inadvertently uses future information that wouldn't be available at the time of making trading decisions in real-time markets. This guide provides comprehensive examples and solutions for Pine Script developers.

## What is Look-Ahead Bias?

**Definition:** Look-ahead bias (also called future leak or peeking) happens when a quantitative model incorporates information that would not have been available at the time of making a prediction or investment decision.

**Impact:**
- Artificially inflated backtest performance
- Unrealistic profit expectations
- Poor real-time trading results
- Loss of capital when deployed live

## Common Causes in Pine Script

### 1. Security Function with Lookahead Enabled
### 2. calc_on_order_fills Setting
### 3. Non-Standard Chart Types
### 4. Improper Data Offsetting
### 5. Manual Backtesting Bias

## Code Examples

### ❌ BAD: Look-Ahead Bias Example

This example demonstrates a strategy that "peeks into the future" by using daily high/low data that hasn't occurred yet:

```pinescript
//@version=5
strategy("BAD: Look-Ahead Bias Example", overlay=true)

// ⚠️ DANGEROUS: Using lookahead=barmerge.lookahead_on
dayStart = request.security(syminfo.tickerid, "1D", time, lookahead=barmerge.lookahead_on)
dayHigh = request.security(syminfo.tickerid, "1D", high, lookahead=barmerge.lookahead_on)
dayLow = request.security(syminfo.tickerid, "1D", low, lookahead=barmerge.lookahead_on)

// Entry at first bar of a day
if time == dayStart
    // ⚠️ PROBLEM: This knows the future daily high/low!
    if math.abs(open - dayHigh) > math.abs(open - dayLow)
        strategy.entry("LongEntryId", strategy.long)
        strategy.exit("exitLongId", "LongEntryId", limit=dayHigh)
    else
        strategy.entry("ShortEntryId", strategy.short)
        strategy.exit("exitShortId", "ShortEntryId", limit=dayLow)

plot(dayHigh, color=color.red, title="Daily High")
plot(dayLow, color=color.green, title="Daily Low")
```

**Why This is Bad:**
- The strategy knows the daily high and low before they occur
- It can perfectly choose the best direction based on future price movement
- Real-time performance will be dramatically worse

### ❌ BAD: calc_on_order_fills Bias

```pinescript
//@version=6
strategy("BAD: calc_on_order_fills Bias", 
         overlay=true, 
         calc_on_order_fills=true)  // ⚠️ DANGEROUS SETTING

stopSizeInput = input.float(2.0, "Stop Loss %") / 100.0
profitSizeInput = input.float(4.0, "Take Profit %") / 100.0

// Simple entry condition
buyCondition = ta.crossover(ta.sma(close, 10), ta.sma(close, 20))

if buyCondition
    strategy.entry("Long", strategy.long)
    
// ⚠️ PROBLEM: This executes on the same bar using OHLC data
// that wouldn't be available in real-time
if strategy.position_size > 0
    stopLoss = strategy.position_avg_price * (1.0 - stopSizeInput)
    takeProfit = strategy.position_avg_price * (1.0 + profitSizeInput)
    strategy.exit("Exit", "Long", stop=stopLoss, limit=takeProfit)
```

### ✅ GOOD: Proper Implementation Without Bias

```pinescript
//@version=6
strategy("GOOD: No Look-Ahead Bias", overlay=true)

// Input parameters
stopSizeInput = input.float(2.0, "Stop Loss %") / 100.0
profitSizeInput = input.float(4.0, "Take Profit %") / 100.0

// ✅ CORRECT: Using previous bar's data with historical offset
prevDayHigh = request.security(syminfo.tickerid, "1D", high[1], lookahead=barmerge.lookahead_off)
prevDayLow = request.security(syminfo.tickerid, "1D", low[1], lookahead=barmerge.lookahead_off)

// Entry condition based on available data
sma10 = ta.sma(close, 10)
sma20 = ta.sma(close, 20)
buyCondition = ta.crossover(sma10, sma20)
sellCondition = ta.crossunder(sma10, sma20)

// ✅ CORRECT: Calculate stop/take profit levels BEFORE entry
var float stopLoss = na
var float takeProfit = na

if buyCondition and strategy.position_size == 0
    // Use close price for calculations (available at bar close)
    stopLoss := close * (1.0 - stopSizeInput)
    takeProfit := close * (1.0 + profitSizeInput)
    strategy.entry("Long", strategy.long)

if sellCondition and strategy.position_size == 0
    stopLoss := close * (1.0 + stopSizeInput)
    takeProfit := close * (1.0 - profitSizeInput)
    strategy.entry("Short", strategy.short)

// Exit conditions
if strategy.position_size > 0
    strategy.exit("ExitLong", "Long", stop=stopLoss, limit=takeProfit)
    
if strategy.position_size < 0
    strategy.exit("ExitShort", "Short", stop=stopLoss, limit=takeProfit)

// Plotting
plot(sma10, color=color.blue, title="SMA 10")
plot(sma20, color=color.red, title="SMA 20")
plotshape(buyCondition, style=shape.triangleup, location=location.belowbar, 
          color=color.green, size=size.small)
plotshape(sellCondition, style=shape.triangledown, location=location.abovebar, 
          color=color.red, size=size.small)
```

### ✅ GOOD: Proper Higher Timeframe Analysis

```pinescript
//@version=6
indicator("GOOD: Proper HTF Analysis", overlay=true)

// Input for higher timeframe
htf = input.timeframe("1D", "Higher Timeframe")

// ✅ CORRECT: Using historical offset to avoid lookahead
htfHigh = request.security(syminfo.tickerid, htf, high[1], lookahead=barmerge.lookahead_off)
htfLow = request.security(syminfo.tickerid, htf, low[1], lookahead=barmerge.lookahead_off)
htfClose = request.security(syminfo.tickerid, htf, close[1], lookahead=barmerge.lookahead_off)

// ✅ CORRECT: Calculate support/resistance from confirmed data
resistance = htfHigh
support = htfLow
bias = close > htfClose ? "Bullish" : "Bearish"

// Plotting
plot(resistance, color=color.red, linewidth=2, title="HTF Resistance")
plot(support, color=color.green, linewidth=2, title="HTF Support")

// Label for bias
if barstate.islast
    label.new(bar_index, high, text="Bias: " + bias, 
              color=bias == "Bullish" ? color.green : color.red,
              textcolor=color.white, style=label.style_label_down)
```

### ✅ GOOD: Breakout Strategy Without Bias

```pinescript
//@version=6
strategy("GOOD: Clean Breakout Strategy", overlay=true)

// Parameters
lengthInput = input.int(20, "Lookback Length", minval=1)
atrLength = input.int(14, "ATR Length", minval=1)
atrMultiplier = input.float(2.0, "ATR Multiplier", minval=0.1, step=0.1)

// ✅ CORRECT: Calculate levels using confirmed data only
highestHigh = ta.highest(high, lengthInput)
lowestLow = ta.lowest(low, lengthInput)
atrValue = ta.atr(atrLength)

// Entry conditions (no future data)
breakoutUp = close > highestHigh[1] and volume > ta.sma(volume, 20)
breakoutDown = close < lowestLow[1] and volume > ta.sma(volume, 20)

// ✅ CORRECT: Risk management based on available data
if breakoutUp and strategy.position_size == 0
    stopLevel = close - (atrValue * atrMultiplier)
    strategy.entry("Long", strategy.long)
    strategy.exit("LongExit", "Long", stop=stopLevel)

if breakoutDown and strategy.position_size == 0
    stopLevel = close + (atrValue * atrMultiplier)
    strategy.entry("Short", strategy.short)
    strategy.exit("ShortExit", "Short", stop=stopLevel)

// Visualization
plot(highestHigh[1], color=color.red, title="Resistance")
plot(lowestLow[1], color=color.green, title="Support")
plotshape(breakoutUp, style=shape.triangleup, location=location.belowbar, 
          color=color.lime, size=size.normal)
plotshape(breakoutDown, style=shape.triangledown, location=location.abovebar, 
          color=color.red, size=size.normal)
```

## Prevention Techniques

### 1. Always Use Historical Offsets

```pinescript
// ❌ BAD
currentHTFHigh = request.security(syminfo.tickerid, "1D", high)

// ✅ GOOD
confirmedHTFHigh = request.security(syminfo.tickerid, "1D", high[1])
```

### 2. Disable Lookahead by Default

```pinescript
// ✅ GOOD: Explicitly disable lookahead
data = request.security(syminfo.tickerid, "4H", close, lookahead=barmerge.lookahead_off)
```

### 3. Avoid calc_on_order_fills Unless Necessary

```pinescript
// ✅ GOOD: Default behavior
strategy("My Strategy", overlay=true)  // calc_on_order_fills=false by default

// ⚠️ USE WITH CAUTION: Only when absolutely necessary
strategy("Special Strategy", overlay=true, calc_on_order_fills=true)
```

### 4. Use Standard Chart Types for Backtesting

```pinescript
// Add this to strategy declaration for Heikin Ashi charts
strategy("My Strategy", overlay=true, fill_orders_on_standard_ohlc=true)
```

## Best Practices

### Data Validation Checklist

```pinescript
//@version=6
strategy("Data Validation Example", overlay=true)

// ✅ 1. Confirm data availability
isDataValid = not na(close) and not na(volume) and volume > 0

// ✅ 2. Check for sufficient history
hasEnoughBars = bar_index >= 50

// ✅ 3. Validate timeframe consistency
isCorrectTF = timeframe.period == "5"  // Example for 5-minute strategy

// ✅ 4. Only trade when all conditions are met
canTrade = isDataValid and hasEnoughBars and isCorrectTF

// Strategy logic only executes with valid data
if canTrade
    // Your strategy logic here
    buySignal = ta.crossover(ta.sma(close, 10), ta.sma(close, 20))
    if buySignal
        strategy.entry("Long", strategy.long)
```

### Realistic Transaction Costs

```pinescript
//@version=6
strategy("Realistic Costs Example", 
         overlay=true,
         commission_type=strategy.commission.percent,
         commission_value=0.1,  // 0.1% commission
         slippage=2)  // 2 ticks slippage
```

### Proper Risk Management

```pinescript
// ✅ GOOD: Risk management without bias
//@version=6
strategy("Risk Management Example", overlay=true)

// Risk parameters
riskPerTrade = input.float(1.0, "Risk Per Trade %", minval=0.1, maxval=5.0) / 100.0
maxDrawdown = input.float(10.0, "Max Drawdown %", minval=1.0, maxval=20.0) / 100.0

// Position sizing based on account equity and risk
accountEquity = strategy.equity
maxLoss = accountEquity * riskPerTrade
atrValue = ta.atr(14)

// Calculate position size (no future data)
if atrValue > 0
    positionSize = maxLoss / (atrValue * 2.0)  // 2 ATR stop loss
    
    // Entry conditions
    if ta.crossover(ta.sma(close, 10), ta.sma(close, 50))
        strategy.entry("Long", strategy.long, qty=positionSize)
        strategy.exit("Stop", "Long", stop=close - (atrValue * 2.0))
```

## Testing and Validation

### Out-of-Sample Testing Template

```pinescript
//@version=6
strategy("Out-of-Sample Test", overlay=true)

// Date range inputs
inSampleStart = input.time(timestamp("2020-01-01"), "In-Sample Start")
inSampleEnd = input.time(timestamp("2022-01-01"), "In-Sample End")
outSampleStart = input.time(timestamp("2022-01-01"), "Out-of-Sample Start")
outSampleEnd = input.time(timestamp("2024-01-01"), "Out-of-Sample End")

// Current date
currentTime = time

// Determine which period we're in
inSamplePeriod = currentTime >= inSampleStart and currentTime <= inSampleEnd
outSamplePeriod = currentTime >= outSampleStart and currentTime <= outSampleEnd

// Strategy logic (same for both periods)
buySignal = ta.crossover(ta.sma(close, 10), ta.sma(close, 20))

// Execute trades in both periods
if (inSamplePeriod or outSamplePeriod) and buySignal
    strategy.entry("Long", strategy.long)
    strategy.exit("Exit", "Long", stop=close * 0.98, limit=close * 1.04)

// Background coloring to show periods
bgcolor(inSamplePeriod ? color.new(color.blue, 90) : 
        outSamplePeriod ? color.new(color.orange, 90) : na)
```

### Performance Validation Indicators

```pinescript
//@version=6
indicator("Strategy Validation Metrics", overlay=false)

// Calculate key metrics
equity = strategy.equity
maxEquity = ta.highest(equity, 252)  // 1 year lookback
drawdown = (maxEquity - equity) / maxEquity

// Warning levels
maxAcceptableDD = 0.15  // 15%
warningDD = 0.10        // 10%

// Plot drawdown
plot(drawdown * 100, color=drawdown > maxAcceptableDD ? color.red : 
                           drawdown > warningDD ? color.orange : color.green,
     title="Drawdown %")

// Alert conditions
alertcondition(drawdown > maxAcceptableDD, title="Excessive Drawdown", 
               message="Strategy drawdown exceeds 15%")
```

## Red Flags to Watch For

### 1. Unrealistic Performance Metrics
- Win rates above 80%
- Profit factors above 3.0
- Maximum drawdowns below 5%
- Sharp ratios above 3.0

### 2. Perfect Timing
- Entries at exact lows
- Exits at exact highs
- No losing streaks

### 3. Code Patterns to Avoid
```pinescript
// ❌ RED FLAGS
request.security(symbol, timeframe, high, lookahead=barmerge.lookahead_on)
strategy("Name", calc_on_order_fills=true)  // Without proper justification
if high == ta.highest(high, 20)  // Perfect high detection
```

## Conclusion

Look-ahead bias is a silent strategy killer that can make worthless strategies appear profitable in backtesting. By following the practices outlined in this guide and carefully reviewing your code for the patterns shown, you can develop more robust and realistic trading strategies.

**Key Takeaways:**
1. Always use historical offsets with higher timeframe data
2. Avoid `calc_on_order_fills` unless absolutely necessary
3. Disable lookahead in `request.security()` calls
4. Test strategies out-of-sample
5. Be suspicious of "too good to be true" backtest results
6. Include realistic transaction costs and slippage

Remember: If your backtest results seem too good to be true, they probably are. Rigorous testing and validation are essential for successful algorithmic trading.
