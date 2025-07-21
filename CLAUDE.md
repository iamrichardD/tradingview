# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a TradingView trading strategies repository focused on Pine Script v6 development. It contains educational trading strategies with comprehensive testing frameworks, primarily for cryptocurrency and forex markets. The repository emphasizes institutional-grade code quality, professional testing methodologies, and experimental strategy development.

## Core Commands

### Development Workflow Commands
```bash
# Project Navigation
cd /home/rdelgado/Development/tradingview/testing-lab/ema-ribbon-macd-hybrid/  # Active development
cd /home/rdelgado/Development/tradingview/4h-swing-trading/                    # Production strategies
cd /home/rdelgado/Development/tradingview/5m-scalping-strategy/                # Production strategies

# File Management
cp strategy-v1.pine strategy-v2.pine                                          # Version control
git add . && git commit -m "feat: implement new feature"                      # Version tracking
git status && git diff                                                        # Check changes

# Code Review Workflow (with Gemini integration)
# 1. Develop strategy using Claude guidance
# 2. Review code using GEMINI.md checklists 
# 3. Validate Pine Script v6 compliance
# 4. Ensure ≥90% test pass rate before deployment
```

### Testing & Validation Commands
```bash
# Pine Script Validation (TradingView Platform)
# 1. Copy Pine Script code to TradingView editor
# 2. Compile and check for errors/warnings (must be zero)
# 3. Run backtest on multiple timeframes and markets
# 4. Validate test suites within strategy code
# 5. Ensure all quality gates pass before production

# Quality Assurance Checklist
# - [ ] Zero Pine Script v6 compilation errors
# - [ ] ≥90% test pass rate on both test suites
# - [ ] Performance benchmarks met
# - [ ] Risk management controls validated
# - [ ] Documentation complete and professional
```

### Development Commands
- **Strategy Development**: Follow modular architecture pattern with Pine Script v6 compliance
- **Code Review**: Use GEMINI.md guidelines for quality assurance and compliance validation (see GEMINI.md for detailed checklists)
- **Testing**: Implement dual test suite framework (basic + enhanced) with ≥90% pass rate requirement
- **Validation**: Ensure institutional-grade quality before production deployment

### AI Collaboration Workflow
- **Claude Role**: Strategy development, implementation, technical architecture
- **Gemini Role**: Code review, quality assurance, Pine Script v6 compliance validation (see GEMINI.md)
- **Integration**: Use both AIs collaboratively for institutional-grade development standards

## Architecture & Structure

### High-Level Architecture
The repository follows a **modular strategy architecture** with these key components:

1. **Strategy Collection** (`/4h-swing-trading/`, `/5m-scalping-strategy/`)
   - Production-ready Pine Script v6 strategies
   - Each strategy is self-contained with embedded testing framework
   - Market Structure + MACD based approaches with advanced risk management

2. **Testing Lab** (`/testing-lab/`)
   - Experimental strategy development environment
   - Active development of ultra-fast scalping strategies and advanced signal systems
   - **Active Projects**: EMA Ribbon + MACD Hybrid v1.1 (in development)
   - **Completed Projects**: BTC 5M True Scalping v1.0 (graduation ready)
   - Paper trading only - graduation to main strategies after validation

3. **Dual Testing Framework**
   - **Basic Test Suite**: Rapid development validation (25+ tests)
   - **Enhanced Test Suite**: Production readiness validation (40+ tests)
   - Quality gates require ≥90% pass rates for deployment

### Strategy Architecture Pattern
Each strategy follows this consistent architecture:
```pinescript
//=====================================================================================================================
// STRATEGY CONFIGURATION - Input parameters grouped by function
//=====================================================================================================================

//=====================================================================================================================
// TECHNICAL INDICATORS & CALCULATIONS - All indicator calculations
//=====================================================================================================================

//=====================================================================================================================
// SIGNAL LOGIC - Market structure, MACD, filters
//=====================================================================================================================

//=====================================================================================================================
// RISK MANAGEMENT - ATR-based positioning, trailing stops
//=====================================================================================================================

//=====================================================================================================================
// STRATEGY EXECUTION - Entry/exit logic with comprehensive validation
//=====================================================================================================================

//=====================================================================================================================
// TESTING FRAMEWORK - Dual test suites with quality gates
//=====================================================================================================================

//=====================================================================================================================
// VISUALIZATION & MONITORING - Performance dashboards and alerts
//=====================================================================================================================
```

## Pine Script v6 Critical Requirements

### Mandatory Syntax Rules
- **Single Line Statements**: No line continuation (`\`) - all statements must be single line
- **Function Call Pre-calculation**: All `ta.*`, `math.*` calls must be pre-calculated outside conditionals
- **Type History Syntax**: Use `(object[1]).field` NOT `object.field[1]` for user-defined types
- **Const Strings**: Plot functions require const strings, not series strings
- **Runtime Initialization**: Initialize all user-defined type fields on first bar (`bar_index == 0`)
- **Ternary Operator Usage**: Complex multi-line ternary operators must be single line - no line breaks

### Common Compilation Errors to Avoid
- Line continuation errors (break long conditions into multiple statements)
- Function calls inside conditionals (pre-calculate all values)
- Incorrect type history syntax on custom types
- Series strings in plot functions (use const strings only)
- Uninitialized type field access (add first-bar initialization)
- Multi-line ternary operators (consolidate to single line)
- Function calls in conditional expressions causing inconsistent calculations

## Development Workflow

### Strategy Development Process
1. **Analysis**: Review existing strategies for patterns and conventions
2. **Implementation**: Follow Pine Script v6 compliance checklist from `claude-instructions.md`
3. **Testing**: Implement dual test suite (basic + enhanced)
4. **Validation**: Achieve ≥90% test pass rate
5. **Documentation**: Create comprehensive implementation guides

### Testing Lab Development
1. **Experimental Implementation**: Develop in `/testing-lab/`
2. **Paper Trading Only**: No live trading until graduation
3. **Comprehensive Validation**: Specialized test suites for experimental features
4. **Performance Analysis**: Document missed opportunities and system improvements
5. **Iterative Enhancement**: Multi-path signal systems, advanced filtering, smart exits
6. **Graduation Criteria**: 90%+ test pass rate + performance targets met + risk validation
7. **Promotion**: Move to main repository after validation

## Market-Specific Configurations

### BTC/Crypto Strategies (5M Scalping)
- **MACD Parameters**: 8,21,5 (crypto-optimized vs traditional 12,26,9)
- **EMA Ribbon**: 8,21,34 system for enhanced trend filtering
- **Risk Management**: 1.5-1.8% stops, 2.5-2.7% targets
- **Advanced Filtering**: Multi-path signal detection, adaptive cooldowns, reversal exits
- **Filters**: Volume (1.3-1.5x avg) + EMA alignment + SMA context + signal strength
- **Performance Target**: 70-85% win rate with enhanced opportunity capture

### Multi-Asset Strategies (4H Swing)
- **Market Structure**: CHoCH, BOS, IDM detection with LuxAlgo methodology
- **Risk Management**: ATR-based dynamic positioning (1.5-3.0 multipliers)
- **MACD Filter**: Traditional 12,26,9 with bullish/bearish confirmation
- **Performance Target**: 60-70% win rate with trailing stops

## Quality Standards

### Deployment Criteria
```
Deployment Ready = 
    Overall Test Pass Rate ≥90% AND
    Core Systems ≥90% AND
    Performance Validated AND
    Pine Script v6 Compliant AND
    Zero Compilation Errors
```

### Testing Lab Graduation
```
Graduation Ready = 
    Experimental Validation ≥90% AND
    Transformation Goals Met AND
    Paper Trading Validated AND
    Safety Protocols Complete
```

## Important Development Guidelines

### Pine Script v6 Compliance (CRITICAL)
- Always reference `claude-instructions.md` for detailed Pine Script v6 requirements
- Validate ALL syntax rules simultaneously (cascading errors common)
- Test compilation on TradingView platform before considering complete
- Implement runtime safety patterns for user-defined types

### Code Quality Standards
- Follow existing naming conventions and architectural patterns
- Implement comprehensive error handling and edge case validation
- Maintain professional documentation standards
- Use modular, testable component design

### Risk Management Implementation
- ATR-based dynamic position sizing required
- Multi-layer risk controls (daily, weekly, position limits)
- Emergency stop procedures documented and tested
- Real-time performance monitoring integrated

## Educational Focus

This repository serves as both a production trading system and educational resource demonstrating:
- Professional Pine Script v6 development practices
- Institutional-grade testing methodologies
- Advanced risk management system implementation
- Experimental strategy development workflows
- Transformation from complex to optimized trading systems

The emphasis is on learning through systematic development, comprehensive testing, and continuous improvement of trading strategy implementations.