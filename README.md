# Regulatory Forecast Model: Taiwan's Article 72-2 Real Estate Loan Cap

**Are Taiwanese banks approaching the 30% legal ceiling on real estate lending?**

A data science analysis of Article 72-2 of Taiwan's Banking Act, combining regulatory ratio tracking with demand-side modeling (linear regression and LSTM) on 24+ years of monthly data.

---

## Background

Article 72-2 of the Banking Act limits a commercial bank's residential and business construction loans to **30% of its total deposits plus issued bank debentures**. The rule exists to stop banks from over-concentrating in real estate, a lesson from past property bubbles. With mortgage demand rising and public banks tightening credit conditions in late 2024, the question is how much headroom is actually left.

## Headline Finding

| Metric (Oct 2024) | Value |
|---|---|
| Housing + construction loans / total deposits | **23.06%** |
| Headroom to the 30% cap | **6.94 percentage points** |
| FSC's own estimate (Jul 2024, broader loan scope) | ~26.47% |

**Banks are not at the ceiling, but the buffer is narrowing.** The ratio has climbed steadily since 2000. The FSC treats the rule as a dynamic ratio control: the denominator (deposits) keeps growing, so headroom is continually replenished.

> The gap between 23.06% and the FSC's 26.47% comes from scope. The FSC figure includes commercial real estate loans and debentures in its denominator; this analysis uses housing purchase loans plus construction loans against total deposits.

## Approach

1. **Data**: 298 monthly observations (Jan 2000 to Oct 2024) covering consumer loan categories, construction loans, households, population, income, and total bank deposits (sources: Taiwan FSC, DGBAS).
2. **Ratio analysis**: `Housing Loan Sum = House Purchasing Loans + Construction Loans`, divided by total deposits and compared to the 30% cap.
3. **Demand drivers**: Linear regressions of loan volume on **Households** vs **Population**.
4. **Sequence model**: An LSTM using household count as the feature, to capture time dependence that linear regression ignores.

## Model Results

| Model | Feature | R² |
|---|---|---|
| Linear Regression | Households | **0.92** |
| Linear Regression | Population | 0.65 |

Households is the stronger predictor. Taiwan's population has been flat or declining in recent years while household count keeps rising (smaller households, urbanization, more single-person homes), so housing demand tracks household formation rather than headcount.

## Limitations

Stating these up front, because they matter:

- **Random train/test split on time-series data.** The regression and LSTM use `train_test_split` with shuffling, which leaks future information into training. A chronological split (e.g., train on 2000-2020, test on 2021-2024) would give a more honest estimate.
- **Single-feature models.** Loan volume is driven by rates, income, and policy as well, not only households. The models describe a relationship; they are not causal.
- **Extrapolation risk.** Linear regression fit on a trending series will extrapolate the trend regardless of regulatory or macro shocks.
- **The cap is a ratio.** Forecasting loan volume alone doesn't say when the ratio hits 30%; that requires projecting deposits as well.
- **Ratio scope differs from the regulator's.** See the note above on 23.06% vs 26.47%.

## Next Steps

- Switch to a chronological split and walk-forward validation
- Forecast the **ratio** directly (loans and deposits jointly) and estimate when, if ever, it reaches 30%
- Add interest rates and income as features
- Scenario analysis: deposit growth slowdown, rate cuts

## Repository Contents

| File | Description |
|---|---|
| `Taiwanese_Banking_Law_72-2.ipynb` | Full analysis notebook |
| `Presentation Data V1.csv` | Loan, household, population, income data |
| `LSTM Data Set.csv` | Modeling dataset |
| `2000 Total Deposit.csv` | Monthly total deposits (in 100M TWD) |

## Run It

```bash
pip install pandas numpy matplotlib seaborn scikit-learn tensorflow keras
jupyter notebook Taiwanese_Banking_Law_72-2.ipynb
```

Keep the CSV files in the same folder as the notebook.

## Tech Stack

Python, pandas, NumPy, scikit-learn, Keras/TensorFlow, Matplotlib, Seaborn

---

*Author: Bill Tang*
