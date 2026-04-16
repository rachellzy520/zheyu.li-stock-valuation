```python
import wrds
import pandas as pd
import numpy as np

def analyze_stock_investment(ticker, start_date="2020-01-01"):
    """
    Analyze whether a stock is worth buying based on
    annualized return and annualized volatility.
    """

    # Step 1: Connect to WRDS
    # Replace "your_username" with your own WRDS username
    db = wrds.Connection(wrds_username="rachellzy")

    # Step 2: Match ticker with company name
    ticker_to_company = {
        "AAPL": "Apple Inc.",
        "MSFT": "Microsoft Corporation",
        "GOOG": "Alphabet Inc."
    }

    company_name = ticker_to_company.get(ticker, ticker)

    # Step 3: Select required columns
    selected_columns = """
        b.permno AS stock_id,
        b.htsymbol,
        b.hcomnam,
        a.prc,
        a.ret,
        a.date
    """

    # Step 4: Write SQL query
    sql_query = f"""
        SELECT {selected_columns}
        FROM crsp.msf AS a
        LEFT JOIN crsp.msfhdr AS b
        ON a.permno = b.permno
        WHERE b.htsymbol = '{ticker}'
        AND a.date >= '{start_date}'
    """

    # Step 5: Extract data from WRDS
    stock_info = db.raw_sql(sql_query, date_cols=["date"])

    # Step 6: Close WRDS connection
    db.close()

    # Step 7: Check whether data exists
    if stock_info.empty:
        print(f"No data found for {ticker}.")
        return None

    # Step 8: Rename columns
    stock_info = stock_info.rename(columns={
        "htsymbol": "ticker",
        "hcomnam": "company_name",
        "prc": "stock_price",
        "ret": "monthly_return"
    })

    # Step 9: Define calculation functions
    def calculate_annual_return(monthly_return):
        valid_return = monthly_return.dropna()
        return valid_return.mean() * 12

    def calculate_annual_volatility(monthly_return):
        valid_return = monthly_return.dropna()
        return valid_return.std() * np.sqrt(12)

    # Step 10: Calculate indicators
    annual_return = calculate_annual_return(stock_info["monthly_return"])
    annual_volatility = calculate_annual_volatility(stock_info["monthly_return"])

    # Step 11: Classify return level
    if annual_return > 0.10:
        return_level = "high return"
    elif annual_return > 0.05:
        return_level = "medium return"
    else:
        return_level = "low return"

    # Step 12: Classify risk level
    if annual_volatility > 0.25:
        risk_level = "high risk"
    elif annual_volatility > 0.15:
        risk_level = "medium risk"
    else:
        risk_level = "low risk"

    # Step 13: Generate investment suggestion
    if return_level == "high return" and risk_level in ["low risk", "medium risk"]:
        suggestion = "Buy: good investment choice!"
    elif return_level == "medium return" and risk_level == "medium risk":
        suggestion = "Hold/Wait: pay more attention to market conditions!"
    else:
        suggestion = "Not recommended: low investment value."

    # Step 14: Print result
    print("=" * 70)
    print(f"{company_name} ({ticker}) - INVESTMENT DECISION")
    print("=" * 70)
    print(f"Annual Return: {annual_return:.2%}")
    print(f"Annual Volatility: {annual_volatility:.2%}")
    print(f"Return Level: {return_level}")
    print(f"Risk Level: {risk_level}")
    print(f"Final Suggestion: {suggestion}")
    print("=" * 70)

    # Step 15: Return result
    return {
        "company_name": company_name,
        "ticker": ticker,
        "annual_return": annual_return,
        "annual_volatility": annual_volatility,
        "return_level": return_level,
        "risk_level": risk_level,
        "suggestion": suggestion,
        "stock_data": stock_info
    }
    
# Call the function here 
result = analyze_stock_investment("AAPL", "2020-01-01")
print(result)
print(result["suggestion"])
```

    Loading library list...
    Done
    ======================================================================
    Apple Inc. (AAPL) - INVESTMENT DECISION
    ======================================================================
    Annual Return: 29.44%
    Annual Volatility: 28.93%
    Return Level: high return
    Risk Level: high risk
    Final Suggestion: Not recommended: low investment value.
    ======================================================================
    {'company_name': 'Apple Inc.', 'ticker': 'AAPL', 'annual_return': np.float64(0.2944261999999999), 'annual_volatility': np.float64(0.2893427222407564), 'return_level': 'high return', 'risk_level': 'high risk', 'suggestion': 'Not recommended: low investment value.', 'stock_data':     stock_id ticker company_name  stock_price  monthly_return       date
    0      14593   AAPL    APPLE INC    309.51001         0.05401 2020-01-31
    1      14593   AAPL    APPLE INC    273.35999        -0.11431 2020-02-28
    2      14593   AAPL    APPLE INC    254.28999       -0.069762 2020-03-31
    3      14593   AAPL    APPLE INC    293.79999        0.155374 2020-04-30
    4      14593   AAPL    APPLE INC       317.94        0.084956 2020-05-29
    5      14593   AAPL    APPLE INC    364.79999        0.147386 2020-06-30
    6      14593   AAPL    APPLE INC    425.04001        0.165132 2020-07-31
    7      14593   AAPL    APPLE INC    129.03999        0.216309 2020-08-31
    8      14593   AAPL    APPLE INC       115.81       -0.102526 2020-09-30
    9      14593   AAPL    APPLE INC       108.86       -0.060012 2020-10-30
    10     14593   AAPL    APPLE INC       119.05         0.09549 2020-11-30
    11     14593   AAPL    APPLE INC       132.69        0.114574 2020-12-31
    12     14593   AAPL    APPLE INC    131.96001       -0.005502 2021-01-29
    13     14593   AAPL    APPLE INC       121.26       -0.079532 2021-02-26
    14     14593   AAPL    APPLE INC       122.15         0.00734 2021-03-31
    15     14593   AAPL    APPLE INC    131.46001        0.076218 2021-04-30
    16     14593   AAPL    APPLE INC       124.61       -0.050434 2021-05-28
    17     14593   AAPL    APPLE INC    136.96001        0.099109 2021-06-30
    18     14593   AAPL    APPLE INC       145.86        0.064982 2021-07-30
    19     14593   AAPL    APPLE INC       151.83        0.042438 2021-08-31
    20     14593   AAPL    APPLE INC        141.5       -0.068037 2021-09-30
    21     14593   AAPL    APPLE INC        149.8        0.058657 2021-10-29
    22     14593   AAPL    APPLE INC        165.3         0.10494 2021-11-30
    23     14593   AAPL    APPLE INC    177.57001        0.074229 2021-12-31
    24     14593   AAPL    APPLE INC       174.78       -0.015712 2022-01-31
    25     14593   AAPL    APPLE INC       165.12       -0.054011 2022-02-28
    26     14593   AAPL    APPLE INC       174.61        0.057473 2022-03-31
    27     14593   AAPL    APPLE INC    157.64999       -0.097131 2022-04-29
    28     14593   AAPL    APPLE INC       148.84       -0.054424 2022-05-31
    29     14593   AAPL    APPLE INC       136.72        -0.08143 2022-06-30
    30     14593   AAPL    APPLE INC    162.50999        0.188634 2022-07-29
    31     14593   AAPL    APPLE INC       157.22       -0.031137 2022-08-31
    32     14593   AAPL    APPLE INC        138.2       -0.120977 2022-09-30
    33     14593   AAPL    APPLE INC       153.34        0.109551 2022-10-31
    34     14593   AAPL    APPLE INC       148.03       -0.033129 2022-11-30
    35     14593   AAPL    APPLE INC    129.92999       -0.122273 2022-12-30
    36     14593   AAPL    APPLE INC    144.28999        0.110521 2023-01-31
    37     14593   AAPL    APPLE INC       147.41        0.023217 2023-02-28
    38     14593   AAPL    APPLE INC    164.89999        0.118649 2023-03-31
    39     14593   AAPL    APPLE INC    169.67999        0.028987 2023-04-28
    40     14593   AAPL    APPLE INC       177.25        0.046028 2023-05-31
    41     14593   AAPL    APPLE INC       193.97         0.09433 2023-06-30
    42     14593   AAPL    APPLE INC       196.45        0.012786 2023-07-31
    43     14593   AAPL    APPLE INC       187.87       -0.042454 2023-08-31
    44     14593   AAPL    APPLE INC    171.21001       -0.088678 2023-09-29
    45     14593   AAPL    APPLE INC       170.77        -0.00257 2023-10-31
    46     14593   AAPL    APPLE INC       189.95         0.11372 2023-11-30
    47     14593   AAPL    APPLE INC       192.53        0.013583 2023-12-29
    48     14593   AAPL    APPLE INC    184.39999       -0.042227 2024-01-31
    49     14593   AAPL    APPLE INC       180.75       -0.018492 2024-02-29
    50     14593   AAPL    APPLE INC       171.48       -0.051286 2024-03-28
    51     14593   AAPL    APPLE INC       170.33       -0.006706 2024-04-30
    52     14593   AAPL    APPLE INC       192.25        0.130159 2024-05-31
    53     14593   AAPL    APPLE INC       210.62        0.095553 2024-06-28
    54     14593   AAPL    APPLE INC       222.08        0.054411 2024-07-31
    55     14593   AAPL    APPLE INC        229.0        0.032286 2024-08-30
    56     14593   AAPL    APPLE INC        233.0        0.017467 2024-09-30
    57     14593   AAPL    APPLE INC       225.91       -0.030429 2024-10-31
    58     14593   AAPL    APPLE INC       237.33        0.051658 2024-11-29
    59     14593   AAPL    APPLE INC       250.42        0.055155 2024-12-31}
    Not recommended: low investment value.
    


```python

```
