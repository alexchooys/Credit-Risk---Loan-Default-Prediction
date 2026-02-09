DataExploration Part1

For your variables of interest, report descriptive values (e.g., counts, ranges, missingness, most common category, etc.).
Include at least one screenshot/snippet or copy-paste output from .info() and .describe() (or an equivalent summary).

Target Variable: loan_status (raw target)
Type: Categorical
Most common category: Fully Paid (~80%)
Default-related categories: Charged Off, Default (~20% combined)
Missingness: 0%
Interpretation:
The dataset contains finalized loan outcomes with a realistic default rate, suitable for credit risk modeling after binarization.

Target Variable: default
 Type: Binary (0 = Non-default, 1 = Default)
 Distribution:
 Non-default: ~80%
 Default: ~20%
 Missingness: 0%
 Comment:
 Moderately imbalanced but realistic for credit risk
 Suitable for binary classification with proper evaluation metrics
 

Borrower Financial Capacity
  annual_inc
   Type: Numeric
   Range: Very wide (from very low income to extremely high values)
   Missingness: Moderate (non-trivial % missing)
   Issues observed:
   Strong right skew
   Extreme outliers (very high reported income)
   Interpretation:
   Income is self-reported → may be noisy or overstated

  emp_length
   Type: Categorical
   Common values:
   “10+ years”, “< 1 year”, “2 years”, etc.
   Missingness: Moderate
   Issues:
   Stored as strings
   Inconsistent formatting (“10+ years”, “1 year”)
   Interpretation:
   Requires standardization or numeric mapping

  home_ownership
   Type: Categorical
   Most common categories:
   MORTGAGE
   RENT
   OWN
   Missingness: Low
   Issues:
   Some rare categories (e.g., OTHER)
   Interpretation:
   Stable and informative predictor

  verification_status
   Type: Categorical
   Categories:
   Verified
   Source Verified
   Not Verified
   Missingness: Low
   Issues:
   Slight ambiguity between “Verified” vs “Source Verified”
   Interpretation:
   Reflects underwriting rigor

Credit History & Creditworthiness
  fico_range_low, fico_range_high
   Type: Numeric
   Range: Typically ~300–850
   Missingness: Very low
   Issues:
   Highly correlated with each other
   Interpretation:
   Core predictors of default risk
   Likely to be combined into a single score (e.g., average)

  earliest_cr_line
   Type: Date
   Derived metric:
   Credit history length (years)
   Missingness: Low
   Issues:
   Requires date parsing
   Interpretation:
   Longer credit history generally indicates lower risk

  open_acc, total_acc
   Type: Numeric (counts)
   Missingness: Low
   Issues:
   Potential multicollinearity
   Interpretation:
   Measures credit exposure and experience

  delinq_2yrs, pub_rec
   Type: Numeric (counts)
   Distribution:
   Highly zero-inflated
   Missingness: Low
   Issues:
   Many zeros, few large values
   Interpretation:
   Presence is more important than magnitude

Debt Burden & Utilization
  dti
   Type: Numeric
   Range:
   Mostly between 0–40, with some extreme values
   Distribution: Right-skewed
   Missingness: Low–moderate
   Issues:
   Extreme values (outliers)
   Interpretation:
   Strong indicator of financial stress

  revol_bal
   Type: Numeric
   Distribution:
   Highly right-skewed
   Missingness: Low
   Issues:
   Extreme balances
   Interpretation:
   Often log-transformed

  revol_util
   Type: Numeric (%)
   Range:
   Expected 0–100
   Missingness: Moderate
   Issues:
   Occasional invalid or extreme values
   Interpretation:
   Important but noisy utilization measure


Loan Characteristics
  loan_amnt
   Type: Numeric
   Range: $600 – $40,000
   Median: $12,000
   Missingness: 0%
   Interpretation:
   Loan amounts are discretized and well-bounded, indicating controlled underwriting rules.

  term
   Type: Categorical
   Values:
   36 months
   60 months
   Missingness: 0%
   Issues:
   None
   Interpretation:
   Longer terms generally higher risk

  int_rate
   Type: Numeric
   Range:
   Typically ~5%–30%
   Missingness: 0%
   Issues:
   Correlated with grade/sub_grade
   Interpretation:
   Encodes lender’s risk assessment

  purpose
   Type: Categorical
   Most common:
   Debt consolidation (~58%)
   Credit card refinancing
   Missingness: Low
   Issues:
   High-cardinality categories
   Interpretation:
   Behavioral signal

  grade, sub_grade
   Type: Ordinal categorical
   Most common grade: B
   Most common sub-grade: C1
   Missingness: 0%
   Issues:
   Institution-generated (potential circularity)
   Interpretation:
   Extremely predictive but must be justified

