# Stock Quant Analysis:Quantitative Analysis Tool For Overseas Stocks
zheyu.li-stock-valuation-----acc 102 individual assignment

## Overview
This Python-based tool conducts quantitative evaluation on overseas listed stocks using the CRSP database from the WRDS platform. It supports single-stock analysis and two-stock comparison, calculates key financial metrics, and generates clear investment suggestions based on built-in risk-return rules.

- Target Users: Novice investors in overseas stock markets, junior financial professionals
- Core Value: Simple interface, standardized output, objective data-driven decisions to reduce behavioral bias in investment

## Environment & Dependencies
- Python 3.8 or above
- Valid WRDS account with access to CRSP

Install required libraries:
```bash
pip install wrds pandas numpy matplotlib
```
```

## Quick Start
### 1. Configure WRDS Username
Change the username to your own WRDS account:
```python
username = "add your username"
```

### 2. Single Stock Analysis
```python
result = analyze_stock_investment("ticker", "date")
print(result["suggestion"])
```

### 3. Two-Stock Comparison
```python
comparison = compare_two_stocks("ticker1", "ticker2", "date")
print(comparison)
```

## Input & Output
### Inputs
- Stock ticker (e.g., AAPL, MSFT, GOOG)
- Start date (format: YYYY-MM-DD)

### Outputs
- Key metrics: Annual Return, Annual Volatility, Sharpe Ratio, Max Drawdown
- Investment suggestion: Buy / Wait / Not Buy
- Stock price trend chart (single or dual)
- Comparative table of two stocks

## Key Functions
- `get_stock_data()`: Pull monthly stock data from WRDS-CRSP
- `analyze_stock_investment()`: Full analysis for one stock with decision output
- `compare_two_stocks()`: Side-by-side comparison of two stocks
- `plot_stock_price()`: Plot single stock price trend
- `plot_two_stock_prices()`: Plot dual stock price comparison

## Metric Rules
- Annual Return: >10% High | 5%–10% Medium | <5% Low
- Volatility: >25% High | 15%–25% Medium | <15% Low
- Sharpe Ratio: >1.0 Good | 0.5–1.0 Fair | <0.5 Poor
- Max Drawdown: <10% Low loss risk | 10%–20% Medium | >20% High

## Notes
- This tool relies on historical data from CRSP; results are for reference only.
- Ensure your WRDS account has valid access permissions.
- If no data is returned, check the ticker symbol and start date.
- Charts require a properly configured matplotlib environment.

## Limitations & Future Improvements
- Manual ticker input only; no automatic matching or batch analysis
- Analysis limited to price and return data; no macro or industry factors
- Supports only pairwise comparison

Future versions may add financial ratios, automated ticker search, and multi-stock ranking.

## Disclaimer
This program is for educational and research purposes only. It does not constitute financial advice or investment recommendations. Users are fully responsible for their investment decisions.
```
