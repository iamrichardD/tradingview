# AI - Williams Alligator Strategy with ATR Stop-Loss

## Strategy Overview

This Pine Script v6 strategy implements the Williams Alligator indicator for trend identification combined with ATR-based risk management. The strategy focuses on trend-following signals with dynamic stop-loss protection.

## Trading Logic

### Entry Conditions
- **Long Entry**: Opens when the Lips line (green, 5-period SMMA) crosses above the Jaw line (blue, 13-period SMMA)
- **Signal Interpretation**: Indicates potential trend start or continuation to the upside

### Exit Conditions
- **Signal Exit**: Closes when Lips line crosses below either Jaw or Teeth lines
- **Stop-Loss Exit**: ATR-based stop-loss at `entry_price - (2.0 × ATR_14)`

## Risk Management Features

- **ATR Stop-Loss**: Dynamic stop-loss using 14-period ATR with 2.0 multiplier
- **Date Window**: Configurable backtesting period (default: Jan 2018 - Dec 2069)
- **Single Direction**: Long-only strategy (no short positions)
- **No Pyramiding**: Maximum 1 position at a time

## Strategy Configuration

### Alligator Settings
- **Jaw**: 13-period SMMA (blue line)
- **Teeth**: 8-period SMMA (red line)  
- **Lips**: 5-period SMMA (green line)

### Risk Management
- **ATR Period**: 14 bars
- **ATR Multiplier**: 2.0 (configurable)

## Visual Elements

- Alligator lines plotted with distinct colors
- Entry signals marked with green triangles below bars
- Exit signals marked with red triangles above bars
- All plot offsets set to 0 for real-time visualization

## Installation & Usage

1. Copy the code from `strategy.pine`
2. Open TradingView chart
3. Access Pine Editor
4. Paste code and click "Add to Chart"
5. Configure parameters in strategy settings
6. Review performance in Strategy Tester tab

## Testing Lab Status

- **Project Type**: Experimental strategy development
- **Testing Phase**: Initial implementation
- **Paper Trading**: Recommended before live deployment
- **Graduation Criteria**: ≥90% test pass rate required for promotion to main strategies