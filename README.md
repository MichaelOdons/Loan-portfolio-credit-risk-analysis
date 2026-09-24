# Credit Risk Analysis: What Drives Loan Defaults?

An end-to-end analysis of 32,581 consumer loans to identify which borrowers
default, whether loan pricing reflects actual risk, and where a lender
should tighten its approval rules.

**Tools:** Excel (Power Query, PivotTables) · SQL · Python · Power BI

---

## Business questions

1. What is the overall default rate, and how much of the loan book is at risk?
2. Does the lender's risk grade (A–G) actually predict default, and is the
   interest rate on each grade high enough for the risk it carries?
3. How much can a borrower safely borrow relative to their income?
4. Which loan purposes, housing situations and borrower profiles are riskiest?
5. Does a previous default predict a future one?

---

## Data

- **Source:** [Credit Risk Dataset (Kaggle)](https://www.kaggle.com/datasets/laotse/credit-risk-dataset)
- **Size:** 32,581 loans, 12 variables
- **Target:** `loan_status` (1 = defaulted, 0 = did not default)
- **Note:** This is simulated data, not from a real lender.

---

## Data quality issues found and fixed

Profiling the raw data surfaced several problems that would have distorted
the results:

| Issue | Rows | How it was handled |
|---|---|---|
| Exact duplicate records | 165 | Removed |
| Impossible ages (123 and 144 years) | 5 | Removed as data entry errors |
| Impossible employment length (123 years) | 2 | Removed as data entry errors |
| Missing interest rate | 3,094 | Filled with the **median rate for that loan grade**, and flagged in `rate_was_missing` |
| Missing employment length | 887 | Kept and shown as "Unknown" rather than guessed |
| Extreme income (6,000,000) | 1 | Kept, but medians used instead of averages for income |

Interest rates were filled by loan grade rather than with one overall median,
because rate depends heavily on loan grade: filling a Grade G loan with the overall
portfolio median would have understated its true cost.

**Final clean dataset: 32,409 loans.**

---

## Key findings

**1. The portfolio default rate is 21.87%, and 24.7% of all money lent is in default**


**2. Risk jumps sharply at Grade D, but pricing does not follow.**

| Grade | Default rate | Median interest rate |
|---|---|---|
| A | 10.0% | 7.49% |
| B | 16.3% | 10.99% |
| C | 20.8% | 13.48% |
| D | 59.0% | 15.31% |
| E | 64.5% | 16.82% |
| F | 70.5% | 18.54% |
| G | 98.4% | 20.16% |

The default rate nearly triples from Grade C to Grade D, while the interest
rate rises less than 2 percentage points. Grades D–G are underpriced for
the risk they carry.

**3. Loan size relative to income is the strongest single predictor.**

| Loan as % of income | Default rate |
|---|---|
| 0–10% | 11.8% |
| 10–20% | 15.1% |
| 20–30% | 22.0% |
| 30–40% | 68.9% |
| Over 40% | 74.2% |

Above 30% of income, most loans fail.

**4. Renters default at 31.6%; homeowners at 7.5%.**

**5. Borrowers with a prior default fail again at 37.9%, versus 18.4% for
those without.**

---

## Dashboard

![Excel dashboard](images/excel-dashboard.png)

Risk concentration by grade and loan-to-income ratio:

![Risk heatmap](images/excel-risk-heatmap.png)

---

## Recommendations

1. **Cap loan-to-income at 30%**, or require additional verification above it.
   This band alone accounts for a large share of defaults.
2. **Review pricing and approval criteria for Grades D–G.** The current rate
   spread does not compensate for a default rate three to nine times higher
   than Grade C.
3. **Apply stricter checks to applicants with a prior default**, particularly
   renters, who combine the two strongest risk signals.

---

## Process

**1. Excel — profiling, cleaning and dashboard**
- Profiled all 12 columns for missing values, ranges and distinct values
- Cleaned the data in Power Query (repeatable, fully documented steps)
- Filled missing interest rates using grade medians via XLOOKUP
- Built banded columns for income, age, employment length and loan-to-income
- Built a KPI summary and an interactive PivotTable dashboard with slicers

**2. SQL — business questions** *(in progress)*

**3. Python — deeper analysis** *(in progress)*

**4. Power BI — final dashboard** *(in progress)*

---

## Limitations

- 3,094 interest rates were imputed; these rows are flagged so any analysis
  using interest rate can exclude them.
- There are no dates, loan terms or repayment amounts, so trends over time
  and actual financial losses cannot be measured.

