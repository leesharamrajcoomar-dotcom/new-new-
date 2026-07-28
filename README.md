# new-Project-regression
Target Revenue Analysis
---
 builds a regression model of Target Corporation's (TGT) quarterly revenue and writes up the findings as a memo. Prepared in the voice of QuantFolio Solutions for a client, Chapman Wealth Management, who is evaluating a Target investment.

Data

qSales_2024.csv, quarterly saleq (revenue) figures pulled from Compustat, filtered to tic == 'TGT'. Covers 93 quarters, 2001 Q4 (fiscal) through fiscal Q4 2023 (calendar date 2024-01-31). The CSV must sit in the same folder as the notebook — it also contains AAPL and NTDOY rows, which the first code cell filters out.

Requirements

Python 3 with pandas, numpy, statsmodels, and matplotlib. No other setup needed; running all cells top to bottom reproduces every number and chart in the notebook.

What the notebook does
Load and visualize — reads the CSV, isolates Target, builds a sequential time index (1, 2, 3, ...), plots revenue over time.
Identify patterns — groups revenue by fiscal quarter and inspects 2019-2021 directly, which surfaces two things: a recurring Q4 (Nov-Jan) holiday spike, and a level shift that starts in mid-2020 and never reverts.
Build two dummy variables:
holiday_dummy = 1 when fqtr == 4 (Target's fiscal Q4).
covid_dummy = 1 from time = 79 onward (quarter ending 2020-07-31).
Train/test split — deliberately not a strict 75/25 split. A 75/25 split would end training in 2018, before covid_dummy ever equals 1, making that coefficient inestimable (confirmed: it comes back as exactly 0 with an undefined p-value). Instead, the last 8 quarters are held out as the test set, so training still includes 7 quarters of COVID-era data.
Fit the model — OLS regression: revenue ~ time + holiday_dummy + covid_dummy (no interaction terms; the assignment specified time plus the two dummies only).
Test it — predicts on the 8-quarter holdout and reports MAPE. Note: sm.add_constant() needs has_constant='add' here, because covid_dummy is 1 for every row in that particular test window, which otherwise gets mistaken for an existing intercept column.
Visualize — plots actual vs. predicted revenue across the full history (not just the test set), with the train/test boundary marked.
Memo — addressed to Chapman Wealth Management, covering what the dummies mean for Target's business, how well the model fits, and what it implies for the investment decision.
Results

Fitted on the training set (85 quarters):

revenue = 9,535.06 + 128.85 × time + 4,675.76 × holiday_dummy + 4,270.42 × covid_dummy
All three coefficients are statistically significant (p < 0.001).
R² = 0.93 (training fit).
Test MAPE = 2.97% (last 8 quarters, held out).

Interpretation: Target's underlying trend adds about $129M in revenue per quarter. Holiday quarters (fiscal Q4) run about $4.68B above trend, every year, reliably. Since mid-2020, every quarter has run an additional ~$4.27B above trend, a shift that has held for 3+ years without reverting, which reads as a durable change in Target's revenue base rather than a temporary pandemic bump.

Known limitations
The model explains revenue level only — it says nothing about margins, costs, or competitive position, so it supports the revenue side of an investment thesis, not the whole thesis.
No interaction terms, so the holiday effect and the COVID effect are each assumed constant in size across the whole sample (an $ amount added, not a growth-rate change).
MAPE is computed on a short 8-quarter holdout; treat it as indicative, not a guarantee of future accuracy.
