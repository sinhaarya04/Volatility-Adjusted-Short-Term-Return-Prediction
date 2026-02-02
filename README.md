# Volatility-Adjusted-Short-Term-Return-Prediction

[![Repo](https://img.shields.io/badge/GitHub-Volatility-Adjusted-Short-Term-Return-Prediction-181717?logo=github)](https://github.com/sinhaarya04/Volatility-Adjusted-Short-Term-Return-Prediction)

## Overview

A supervised machine learning trading strategy that predicts 5-day forward volatility-adjusted returns for stocks and indices using technical indicators and options-based features.

## Features

- Exploratory analysis in Jupyter notebooks

## Tech Stack

- Jupyter

## Getting Started

### Prerequisites

- Git
- A recent runtime for the stack above (e.g., Python 3.10+ or Node 18+)

### Installation

```bash
# No install steps documented yet
```

### Run / Usage

```bash
# No run steps documented yet
```

## Project Structure

- `README.md`
- `volatility_adj_prediction.ipynb`

## Roadmap

- [ ] Add clearer usage examples and expected outputs
- [ ] Add tests / CI (if applicable)
- [ ] Document data sources and assumptions (if applicable)

## License

No license file found in this repository.


---

## Notes / Original README

The content below is preserved from the previous README for reference.

# Volatility-Adjusted Short-Term Return Prediction

A supervised machine learning trading strategy that predicts 5-day forward volatility-adjusted returns for stocks and indices using technical indicators and options-based features.

## Overview

This project implements a comprehensive trading strategy that combines:
- Technical analysis indicators (RSI, Bollinger Bands, momentum, moving averages)
- Options market data (implied volatility, skew, put/call ratios, gamma exposure)
- Machine learning (LightGBM ensemble with time-series cross-validation)
- Volatility-targeted position sizing
- Comprehensive backtesting against multiple benchmarks

The strategy predicts whether the 5-day forward volatility-adjusted return will be positive, enabling informed trading decisions with risk-adjusted position sizing.

## Features

### Technical Indicators

- **Momentum Indicators**: RSI (14-day), momentum (5, 10, 20-day), rate of change
- **Trend Indicators**: Simple Moving Averages (5, 10, 20, 50, 200-day), Exponential Moving Averages (12, 26-day), MACD
- **Volatility Indicators**: Bollinger Bands, ATR (Average True Range), realized volatility (5, 10, 20, 60-day)
- **Volume Indicators**: Volume ratios, volume-price trends
- **Price Structure**: High-low ranges, price position in daily range, candlestick patterns

### Options-Based Features

- **Implied Volatility**: ATM IV, IV term structure, IV percentiles
- **IV Skew**: Put-call IV differences (25-delta)
- **Put/Call Ratios**: Open interest and volume ratios
- **Gamma Exposure (GEX)**: Market maker positioning indicators
- **VIX Proxy**: Volatility index approximation

### Model Architecture

- **Algorithm**: LightGBM Gradient Boosting
- **Cross-Validation**: 5-fold time-series split (prevents lookahead bias)
- **Class Imbalance Handling**: Automatic class weighting
- **Ensemble**: Average predictions across all folds
- **Feature Count**: 65 features (40 technical, 17 options-based, 8 additional)

### Trading Strategy

- **Entry Signal**: Long position when predicted probability >= 60%
- **Position Sizing**: Volatility targeting to maintain 10% annualized portfolio volatility
- **Hold Period**: 5 days or exit if signal weakens (probability drops below 48%)
- **Risk Management**: Dynamic position sizing based on realized volatility

### Benchmark Strategies

1. **Buy & Hold**: Simple long position in the underlying asset
2. **MA Crossover**: 50-day/200-day moving average crossover strategy
3. **Logistic Regression**: Baseline linear model with same features
4. **Skew-only**: Strategy based solely on IV skew signals
5. **Gamma-only**: Strategy based solely on gamma exposure signals

## Performance Metrics

The strategy is evaluated using:
- **Total Return**: Cumulative return over the test period
- **Annualized Return**: Return adjusted for time period
- **Sharpe Ratio**: Risk-adjusted return metric
- **Sortino Ratio**: Downside risk-adjusted return
- **Maximum Drawdown**: Largest peak-to-trough decline
- **Win Rate**: Percentage of profitable trades

## Installation

### Requirements

```bash
pip install yfinance pandas numpy matplotlib seaborn scikit-learn lightgbm tqdm
```

### System Dependencies

For macOS users, LightGBM requires OpenMP:
```bash
brew install libomp
```

## Usage

1. **Open the notebook**: `volatility_adj_prediction.ipynb`

2. **Configure parameters** (in Cell 2):
   ```python
   TICKER = "SPY"  # Change to any stock/index symbol
   START_DATE = "2015-01-01"
   HOLD_DAYS = 5
   VOL_TARGET = 0.10  # 10% annualized volatility
   SIGNAL_THRESHOLD = 0.60  # 60% probability threshold
   ```

3. **Run all cells**: Execute cells sequentially from top to bottom

4. **View results**: The notebook generates:
   - Performance comparison tables
   - Cumulative returns charts
   - Drawdown analysis
   - Feature importance visualizations
   - Signal distribution plots

## Data Sources

- **OHLCV Data**: Yahoo Finance (yfinance library)
- **Options Data**: Yahoo Finance options chain API
- **Default Asset**: SPY (S&P 500 ETF) from 2015-01-01 to present

## Methodology

### Label Creation

The target variable is created as:
```
vol_adjusted_return = forward_5day_return / forward_5day_realized_volatility
label = 1 if vol_adjusted_return > 0, else 0
```

This approach normalizes returns by volatility, focusing on risk-adjusted performance rather than raw returns.

### Feature Engineering

1. **Technical Indicators**: Computed from OHLCV data using rolling windows
2. **Options Features**: Extracted from options chain data including:
   - Nearest expiry options
   - ATM (at-the-money) options
   - 25-delta put and call options
   - Open interest and volume data

### Model Training

- **Time-Series Split**: 80% training, 20% testing (chronological)
- **Cross-Validation**: 5-fold time-series CV on training data
- **Early Stopping**: Prevents overfitting with validation monitoring
- **Feature Selection**: Automatic removal of low-information features

### Backtesting

- **Walk-Forward**: Time-series aware, no lookahead bias
- **Transaction Costs**: Can be added in the backtest function
- **Slippage**: Not included (can be added for realistic simulation)

## Results

The strategy demonstrates:
- Superior risk-adjusted returns compared to benchmarks
- Lower maximum drawdown
- Higher Sharpe and Sortino ratios
- Controlled volatility through dynamic position sizing

Performance metrics are displayed in comparison tables and visualizations within the notebook.

## File Structure

```
.
└── volatility_adj_prediction.ipynb  # Main notebook with complete implementation
```

## Key Functions

- `calculate_technical_indicators()`: Computes all technical indicators
- `calculate_options_features()`: Extracts options-based features
- `create_labels()`: Generates volatility-adjusted return labels
- `backtest_strategy_v2()`: Implements the trading strategy with volatility targeting
- `buy_and_hold()`: Buy and hold benchmark
- `ma_crossover_strategy()`: Moving average crossover benchmark
- `calculate_performance_metrics()`: Computes performance statistics

## Configuration

Key parameters can be adjusted in the configuration section:

- `TICKER`: Asset symbol (default: "SPY")
- `START_DATE`: Historical data start date
- `HOLD_DAYS`: Trading horizon (default: 5 days)
- `VOL_TARGET`: Target annualized volatility (default: 0.10 = 10%)
- `SIGNAL_THRESHOLD`: Minimum probability for entry (default: 0.60 = 60%)

## Limitations

- Options data availability depends on Yahoo Finance API
- Simulated options features when real data unavailable
- No transaction costs or slippage in default backtest
- Single-asset strategy (not portfolio-level)

## Future Enhancements

- Multi-asset portfolio optimization
- Transaction cost modeling
- Real-time data integration
- Additional options features (term structure, smile analysis)
- Alternative ML models (neural networks, ensemble methods)

## License

This project is open source and available for educational and research purposes.

## Author

Aryan Sinha

## Repository

[GitHub Repository](https://github.com/sinhaarya04/Volatility-Adjusted-Short-Term-Return-Prediction)
