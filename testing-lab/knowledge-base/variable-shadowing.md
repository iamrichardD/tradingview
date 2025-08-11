# Variable Shadowing in TradingView Pine Script: Complete Knowledge Base

## Table of Contents
1. [Introduction](#introduction)
2. [What is Variable Shadowing?](#what-is-variable-shadowing)
3. [Types of Shadowing in Pine Script](#types-of-shadowing-in-pine-script)
4. [Common Shadowing Scenarios](#common-shadowing-scenarios)
5. [Code Examples and Solutions](#code-examples-and-solutions)
6. [Best Practices](#best-practices)
7. [Debugging and Prevention](#debugging-and-prevention)
8. [Advanced Concepts](#advanced-concepts)

## Introduction

Variable shadowing is a common source of confusion and bugs in Pine Script development. Understanding how Pine Script handles variable scope, declaration, and reassignment is crucial for writing robust trading indicators and strategies. This guide provides comprehensive coverage of shadowing issues and their solutions.

## What is Variable Shadowing?

**Definition:** Variable shadowing occurs when a variable declared within a certain scope (function, conditional block, or loop) has the same name as a variable declared in an outer scope. The inner variable "shadows" or "masks" the outer variable within its local scope.

**In Pine Script Context:** Shadowing typically happens when developers accidentally use the assignment operator (`=`) instead of the reassignment operator (`:=`) when trying to update an existing variable, or when they unknowingly reuse built-in variable names.

### Key Concepts in Pine Script

**Variable Declaration vs. Reassignment:**
- **Declaration (`=`):** Creates a new variable and assigns its initial value
- **Reassignment (`:=`):** Updates the value of an existing variable

**Scope Hierarchy:**
- **Global Scope:** Variables declared at the script level
- **Local Scope:** Variables declared within functions, conditional blocks, or loops
- **Built-in Scope:** Pine Script's reserved variables and functions

## Types of Shadowing in Pine Script

### 1. Local Variable Shadowing Global Variable
When a local variable has the same name as a global variable.

### 2. Built-in Variable Shadowing
When user-defined variables use names that conflict with Pine Script's built-in variables.

### 3. Loop Variable Shadowing
When variables inside loops conflict with outer scope variables.

### 4. Function Parameter Shadowing
When function parameters have the same names as global variables.

## Common Shadowing Scenarios

### Scenario 1: Assignment vs. Reassignment Confusion

**Common Error Message:**
```
Shadowing variable 'variableName' which exists in parent scope. 
Did you want to use the ':=' operator instead of '=' ?
```

### Scenario 2: Built-in Variable Conflicts

**Common Error Messages:**
```
Shadowing built-in variable 'high'
Shadowing built-in variable 'low' 
Shadowing built-in variable 'nvi'
Shadowing built-in variable 'pvi'
```

### Scenario 3: Loop Counter Conflicts

**Common Error Message:**
```
Shadowing variable 'n' which exists in parent scope.
```

## Code Examples and Solutions

### ❌ Problem: Assignment vs. Reassignment Confusion

```pinescript
//@version=5
indicator("BAD: Shadowing Example", overlay=true)

// Global variable declarations
var pos = 0
var entry_price = 0.0
var short_update = false

// Entry conditions
longCondition = ta.crossover(ta.sma(close, 10), ta.sma(close, 20))
shortCondition = ta.crossunder(ta.sma(close, 10), ta.sma(close, 20))

if longCondition
    // ❌ WRONG: This creates new local variables that shadow the globals
    pos = 1                    // Shadowing warning!
    entry_price = close        // Shadowing warning!
    short_update = false       // Shadowing warning!

if shortCondition
    // ❌ WRONG: These are also local variables, not updating globals
    pos = -1                   // Shadowing warning!
    entry_price = close        // Shadowing warning!
    short_update = true        // Shadowing warning!

// The global variables remain unchanged!
plot(pos, title="Position")
```

### ✅ Solution: Proper Reassignment

```pinescript
//@version=5
indicator("GOOD: Proper Reassignment", overlay=true)

// Global variable declarations
var pos = 0
var entry_price = 0.0
var short_update = false

// Entry conditions
longCondition = ta.crossover(ta.sma(close, 10), ta.sma(close, 20))
shortCondition = ta.crossunder(ta.sma(close, 10), ta.sma(close, 20))

if longCondition
    // ✅ CORRECT: Using := to reassign existing variables
    pos := 1
    entry_price := close
    short_update := false

if shortCondition
    // ✅ CORRECT: Properly updating the global variables
    pos := -1
    entry_price := close
    short_update := true

// Now the global variables are properly updated
plot(pos, title="Position")
plot(entry_price, title="Entry Price")

// Alert example using properly updated variables
if math.abs(close - entry_price) > 20 * syminfo.mintick
    alert("Price moved 20 pips from entry: " + str.tostring(pos))
```

### ❌ Problem: Built-in Variable Shadowing

```pinescript
//@version=4
study(title="BAD: Built-in Shadowing")

calc_pvi() =>
    sval = volume
    // ❌ WRONG: 'pvi' is a built-in variable in Pine Script
    pvi = 0.0  // Shadowing built-in variable 'pvi'
    pvi := volume > volume[1] ? 
           nz(pvi[1]) + (close - close[1]) / close[1] * sval : 
           nz(pvi[1])
    pvi

calc_nvi() =>
    sval = volume
    // ❌ WRONG: 'nvi' is also a built-in variable
    nvi = 0.01  // Shadowing built-in variable 'nvi'
    nvi := volume < volume[1] ? 
           nz(nvi[1]) + (close - close[1]) / close[1] * sval : 
           nz(nvi[1])
    nvi

myPvi = calc_pvi()
myNvi = calc_nvi()
plot(myPvi)
plot(myNvi)
```

### ✅ Solution: Renamed Variables

```pinescript
//@version=4
study(title="GOOD: No Built-in Conflicts")

calc_pvi() =>
    sval = volume
    // ✅ CORRECT: Using descriptive, non-conflicting names
    my_pvi = 0.0
    my_pvi := volume > volume[1] ? 
              nz(my_pvi[1]) + (close - close[1]) / close[1] * sval : 
              nz(my_pvi[1])
    my_pvi

calc_nvi() =>
    sval = volume
    // ✅ CORRECT: Clear, non-shadowing variable name
    my_nvi = 0.01
    my_nvi := volume < volume[1] ? 
              nz(my_nvi[1]) + (close - close[1]) / close[1] * sval : 
              nz(my_nvi[1])
    my_nvi

// ✅ CORRECT: Can also use more descriptive names
positive_volume_index = calc_pvi()
negative_volume_index = calc_nvi()

plot(positive_volume_index, title="PVI", color=color.green)
plot(negative_volume_index, title="NVI", color=color.red)
```

### ❌ Problem: Loop Variable Shadowing

```pinescript
//@version=5
strategy("BAD: Loop Shadowing", overlay=true)

// Global counter
var n = 0

if strategy.opentrades < 6
    for i = 0 to 5
        // ❌ WRONG: Reassigning the loop counter variable
        n = strategy.opentrades  // Shadowing variable 'n'
        strategy.entry("Long #" + str.tostring(n + 1), strategy.long)
```

### ✅ Solution: Separate Variable Names

```pinescript
//@version=5
strategy("GOOD: No Loop Shadowing", overlay=true)

// Global counter
var tradeCount = 0

if strategy.opentrades < 6
    for i = 0 to 5
        // ✅ CORRECT: Using different variable names
        currentTrades = strategy.opentrades
        tradeId = currentTrades + 1
        
        if currentTrades < 6
            strategy.entry("Long #" + str.tostring(tradeId), strategy.long)
            tradeCount := tradeCount + 1
```

### ✅ Advanced: Proper Scope Management

```pinescript
//@version=6
indicator("GOOD: Advanced Scope Management", overlay=true)

// Global variables with clear naming
var float globalStopLoss = na
var int globalPosition = 0
var bool globalTradeActive = false

// Function with properly scoped parameters
calculateStopLoss(entryPrice, atrValue, multiplier) =>
    // Local variables with descriptive names
    float localStopDistance = atrValue * multiplier
    float localStopLevel = entryPrice - localStopDistance
    localStopLevel

// Main logic with clear variable management
entryCondition = ta.crossover(ta.sma(close, 10), ta.sma(close, 20))
exitCondition = ta.crossunder(ta.sma(close, 10), ta.sma(close, 20))

if entryCondition and not globalTradeActive
    // ✅ CORRECT: Proper reassignment of global variables
    globalPosition := 1
    globalTradeActive := true
    
    // Calculate stop loss using function (no shadowing)
    atr14 = ta.atr(14)
    globalStopLoss := calculateStopLoss(close, atr14, 2.0)

if exitCondition and globalTradeActive
    // ✅ CORRECT: Clean exit logic
    globalPosition := 0
    globalTradeActive := false
    globalStopLoss := na

// Visualization
plotshape(entryCondition, style=shape.triangleup, location=location.belowbar, 
          color=color.green, size=size.normal, title="Entry")
plotshape(exitCondition, style=shape.triangledown, location=location.abovebar, 
          color=color.red, size=size.normal, title="Exit")
plot(globalStopLoss, color=color.red, linewidth=2, title="Stop Loss")

// Table for debugging (shows actual global values)
if barstate.islast
    var debugTable = table.new(position.top_right, 3, 4, bgcolor=color.white, border_width=1)
    table.cell(debugTable, 0, 0, "Variable", text_color=color.black, bgcolor=color.gray)
    table.cell(debugTable, 1, 0, "Value", text_color=color.black, bgcolor=color.gray)
    table.cell(debugTable, 0, 1, "Position", text_color=color.black)
    table.cell(debugTable, 1, 1, str.tostring(globalPosition), text_color=color.black)
    table.cell(debugTable, 0, 2, "Trade Active", text_color=color.black)
    table.cell(debugTable, 1, 2, str.tostring(globalTradeActive), text_color=color.black)
    table.cell(debugTable, 0, 3, "Stop Loss", text_color=color.black)
    table.cell(debugTable, 1, 3, str.tostring(globalStopLoss), text_color=color.black)
```

## Best Practices

### 1. Naming Conventions

```pinescript
// ✅ GOOD: Clear, descriptive names that avoid conflicts
var float userStopLoss = na        // Instead of 'stopLoss'
var int userPosition = 0           // Instead of 'position' 
var bool isTradeActive = false     // Instead of 'active'

// ✅ GOOD: Prefixes to avoid built-in conflicts
var float myHigh = na              // Instead of 'high'
var float myLow = na               // Instead of 'low'
var float myVolume = na            // Instead of 'volume'
```

### 2. Variable Declaration Checklist

```pinescript
//@version=6
indicator("Variable Declaration Checklist")

// ✅ 1. Declare all global variables at the top
var float stopLossLevel = na
var int positionSize = 0
var bool tradeInProgress = false

// ✅ 2. Use explicit type declarations when helpful
float explicitPrice = close
int explicitPeriod = 20
bool explicitCondition = close > open

// ✅ 3. Initialize variables appropriately
var float runningSum = 0.0        // Initialize to zero
var array<float> priceArray = array.new<float>()  // Initialize collection

// ✅ 4. Use meaningful names
entrySignal = ta.crossover(ta.ema(close, 10), ta.ema(close, 20))
exitSignal = ta.crossunder(ta.ema(close, 10), ta.ema(close, 20))

// ✅ 5. Group related variables
// Risk management variables
var float accountRisk = 0.02
var float positionRisk = na
var float riskReward = 2.0

// Indicator variables  
emaFast = ta.ema(close, 10)
emaSlow = ta.ema(close, 20)
atrValue = ta.atr(14)
```

### 3. Function Parameter Guidelines

```pinescript
// ✅ GOOD: Function parameters with clear, non-conflicting names
calculateMA(sourcePrices, periodLength, maType) =>
    switch maType
        "EMA" => ta.ema(sourcePrices, periodLength)
        "SMA" => ta.sma(sourcePrices, periodLength)
        "WMA" => ta.wma(sourcePrices, periodLength)
        => ta.sma(sourcePrices, periodLength)  // default

// ✅ GOOD: Use parameter names that won't shadow globals
calculateRisk(entryLevel, stopLevel, accountBalance) =>
    riskAmount = math.abs(entryLevel - stopLevel)
    riskPercent = riskAmount / accountBalance
    riskPercent
```

## Debugging and Prevention

### 1. Common Built-in Variables to Avoid

```pinescript
// ❌ AVOID: These are Pine Script built-in variables
// Price-related: open, high, low, close, hl2, hlc3, ohlc4
// Volume-related: volume, nvi, pvi
// Time-related: time, year, month, dayofmonth, hour, minute, second
// Chart-related: bar_index, barstate, session, syminfo
// Math-related: na, math.pi, math.e
// Color-related: color.red, color.green, etc.

// ✅ USE: Alternative naming conventions
var float myOpen = na
var float myHigh = na  
var float myLow = na
var float myClose = na
var float currentVolume = na
var int currentTime = na
```

### 2. Scope Debugging Template

```pinescript
//@version=6
indicator("Scope Debugging Template")

// Global variables for debugging
var int debugGlobalVar = 0
var string debugMessage = ""

debugFunction(inputValue) =>
    // Local variable for function scope
    localVar = inputValue * 2
    debugMessage := "Function local var: " + str.tostring(localVar)
    localVar

if barstate.islast
    // Test different scopes
    functionResult = debugFunction(5)
    
    // Conditional scope test
    if close > open
        conditionalVar = close - open
        debugGlobalVar := int(conditionalVar * 100)
    
    // Loop scope test
    for i = 0 to 2
        loopVar = i * 10
        // Note: loopVar only exists within this loop
    
    // Debug output
    label.new(bar_index, high, 
              "Global: " + str.tostring(debugGlobalVar) + "\n" + 
              "Function result: " + str.tostring(functionResult) + "\n" + 
              debugMessage,
              style=label.style_label_down, color=color.yellow, 
              textcolor=color.black, size=size.large)
```

### 3. Error Prevention Checklist

**Before Writing Code:**
- [ ] Check if variable names conflict with built-ins
- [ ] Plan variable scope (global vs. local)
- [ ] Choose descriptive, unique names

**During Development:**
- [ ] Use `=` only for initial declaration
- [ ] Use `:=` for all reassignments
- [ ] Test scope by printing variable values
- [ ] Watch for compiler warnings

**Code Review:**
- [ ] Verify no shadowing warnings
- [ ] Check variable names are descriptive
- [ ] Ensure proper operator usage (= vs :=)
- [ ] Validate variable behavior matches intent

## Advanced Concepts

### 1. Variable Declaration Modes

```pinescript
//@version=6
indicator("Variable Declaration Modes")

// Standard declaration (resets each bar)
standardVar = close

// var declaration (persists across bars)
var persistentVar = 0.0
persistentVar := persistentVar + close

// varip declaration (persists in real-time)
varip realtimeVar = 0.0
if barstate.isrealtime
    realtimeVar := realtimeVar + 1

plot(standardVar, title="Standard", color=color.blue)
plot(persistentVar, title="Persistent", color=color.green)
plot(realtimeVar, title="Real-time", color=color.red)
```

### 2. Complex Scope Scenarios

```pinescript
//@version=6
indicator("Complex Scope Management")

// Global state management
var state = "waiting"
var float entryPrice = na
var int barsSinceEntry = 0

// Function with proper scope handling
managePosition(currentState, price) =>
    var localCounter = 0
    localCounter := localCounter + 1
    
    newState = currentState
    newPrice = entryPrice
    
    if currentState == "waiting" and ta.crossover(ta.sma(close, 10), ta.sma(close, 20))
        newState := "entered"
        newPrice := price
        
    else if currentState == "entered" and ta.crossunder(ta.sma(close, 10), ta.sma(close, 20))
        newState := "exited"
        newPrice := na
        
    [newState, newPrice, localCounter]

// Proper state management without shadowing
[newState, newEntryPrice, functionCounter] = managePosition(state, close)
state := newState
entryPrice := newEntryPrice

if state == "entered"
    barsSinceEntry := barsSinceEntry + 1
else
    barsSinceEntry := 0

// Visualization
bgcolor(state == "entered" ? color.new(color.green, 90) : na)
plot(entryPrice, color=color.red, linewidth=2, title="Entry Price")
```

### 3. User-Defined Types (UDTs) and Scope

```pinescript
//@version=6
indicator("UDT Scope Management")

// Define custom type to organize related variables
type TradeState
    bool isActive
    float entryPrice
    int entryBar
    string direction

// Global trade state object
var TradeState currentTrade = TradeState.new(false, na, na, "none")

// Function to update trade state (no shadowing issues)
updateTradeState(tradeObj, signal, price, direction) =>
    if signal and not tradeObj.isActive
        tradeObj.isActive := true
        tradeObj.entryPrice := price
        tradeObj.entryBar := bar_index
        tradeObj.direction := direction
    tradeObj

// Entry conditions
longSignal = ta.crossover(ta.sma(close, 10), ta.sma(close, 20))
shortSignal = ta.crossunder(ta.sma(close, 10), ta.sma(close, 20))

// Update trade state
if longSignal
    currentTrade := updateTradeState(currentTrade, true, close, "long")
if shortSignal  
    currentTrade := updateTradeState(currentTrade, true, close, "short")

// Exit condition
exitSignal = currentTrade.isActive and bar_index - currentTrade.entryBar > 10
if exitSignal
    currentTrade.isActive := false
    currentTrade.entryPrice := na
    currentTrade.entryBar := na
    currentTrade.direction := "none"

// Visualization
plot(currentTrade.entryPrice, color=color.yellow, linewidth=2, title="Trade Entry")
bgcolor(currentTrade.isActive ? color.new(color.blue, 95) : na, title="Active Trade")
```

## Summary

Variable shadowing in Pine Script occurs when:

1. **Assignment Confusion:** Using `=` instead of `:=` for variable updates
2. **Built-in Conflicts:** Using reserved Pine Script variable names
3. **Scope Issues:** Declaring variables with same names in different scopes
4. **Loop Problems:** Reassigning loop counters or outer variables

**Key Solutions:**
- Use `:=` for reassignment, `=` only for declaration
- Choose unique, descriptive variable names
- Avoid built-in variable names
- Understand scope hierarchy (global → local)
- Test variable behavior with debugging output

**Prevention Strategy:**
- Plan variable scope before coding
- Use consistent naming conventions
- Leverage Pine Script's type system
- Organize related variables using UDTs
- Always test with debugging output

Understanding and preventing variable shadowing will make your Pine Script code more reliable, maintainable, and bug-free.
