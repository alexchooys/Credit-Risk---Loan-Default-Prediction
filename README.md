# Credit Risk & Loan Default Prediction

## Table of Contents

- [Project Overview](#project-overview)
- [Business Impact](#business-impact)
- [Final Tool](#final-tool)
- [Repository Structure](#repository-structure)
- [Problem Formulation](#problem-formulation)
- [Data Source](#data-source)
- [Data Access & Regenerating Large Files](#data-access--regenerating-large-files)
- [How to Reproduce the Full Pipeline](#how-to-reproduce-the-full-pipeline)
- [How to Run the Interactive Predictor](#how-to-run-the-interactive-predictor)
- [Database Design](#database-design)
- [Data Science Cycle Summary](#data-science-cycle-summary)
- [Model Results](#model-results)
- [Key Insights](#key-insights)
- [Diary Documentation](#diary-documentation)
- [Limitations & Future Work](#limitations--future-work)
- [Authors](#authors)

---

## Project Overview

This project investigates whether borrower financial and credit information can accurately predict loan default outcomes using the publicly available Lending Club dataset.

**Central question:**
> Can we accurately predict whether a loan applicant is likely to default based on financial and credit characteristics?

Loan defaults generate significant financial losses for lenders. By identifying high-risk borrowers early, institutions can improve underwriting decisions, reduce bad debt, and enhance risk management practices.

The project covers the full data science cycle — from problem formulation and data acquisition through wrangling, modeling, evaluation, and an interactive prediction tool — using structured financial and credit variables known at loan origination.

---

## Business Impact

This project provides a practical decision-support tool for identifying high-risk loan applicants before approval. By estimating the probability of default at origination, lenders can better prioritize applications for manual review and reduce exposure to bad debt.

Rather than replacing human judgment, the model is designed to support risk analysts by highlighting borrowers with elevated default risk. For example, institutions could use the model to flag the top 15–25% highest-risk applicants for further scrutiny, improving underwriting efficiency while maintaining approval rates for low-risk borrowers.

The tool is especially valuable in high-volume lending environments, where consistent and data-driven screening can improve both short-term portfolio performance and long-term risk management.

---

## Final Tool

The final deliverable is an **interactive loan default predictor** built as a Jupyter notebook with `ipywidgets`.

**File:** `notebooks/data_visualization_static.ipynb`

The tool allows a user to:
- Input applicant and loan details (FICO score, income, DTI, loan amount, term, purpose, etc.)
- Select from three trained models: Logistic Regression (Fundamental), XGB (Fundamental), or XGB (Full)
- Receive a predicted default probability, risk band (Low / Moderate / Elevated / High), and threshold-based classification
- View a risk gauge chart and the model's validation performance metrics

**Live deployment:** [https://latte.rc.ufl.edu/engine/gallery/credit-risk-loan-default-prediction/](https://latte.rc.ufl.edu/engine/gallery/credit-risk-loan-default-prediction/)

To run the tool locally instead, follow the [How to Run the Interactive Predictor](#how-to-run-the-interactive-predictor) section below.

---

## Repository Structure

```
Credit-Risk-Default-Prediction/
│
├── README.md
├── requirements.txt
│
├── diary/
│   ├── ProblemFormulation.txt
│   ├── DataAcquisition_Part1.txt
│   ├── DataAcquisition_Part2_DataBaseManagement.txt
│   ├── DataAcquisition_Part2_DataDictionary.txt
│   ├── DataExploration_Part1_Diary.md
│   ├── DataExploration_Part2_Diary.txt
│   ├── DataExploration_Part3_Diary.txt
│   ├── DataWrangling_Part1_Cleaning_Diary.txt
│   ├── DataWranglingP2_FeatureEngineering_Diary.txt
│   ├── DataModelingP1_Fundamentals_Diary.txt
│   ├── DataModelingP2_Evaluation_Diary.txt
│   ├── DataVisualization_Diary.txt
│   ├── ModelDeployment_Diary.txt
│   ├── ProjectIteration_Diary.txt
│   ├── info_describe.png           # .info() / .describe() screenshot
│   ├── Before cleaning summary.png
│   └── After cleaning summary.png
│
├── database/
│   └── lending_club_sample_10pct.db   # SQLite database (10% sample)
│
├── docs/
│   ├── DB Schema.png
│   └── data_dictionary.pdf
│
├── notebooks/
│   ├── CleaningDataset_Loan.ipynb              # Stage 0: Initial cleaning, DB creation, exploration
│   ├── data_wrangling.ipynb                    # Stage 1: Full pipeline, feature engineering, splits
│   ├── data_modeling.ipynb                     # Stage 2: Model training, evaluation, threshold tuning
│   └── data_visualization_static.ipynb         # Stage 3: Interactive per-loan default predictor
│
├── 10% Loan Dataset/
│   └── lending_club_sample_10pct.csv           # 10% stratified sample (included in repo)
│
└── artifacts/                                  
    ├── cleaning_only/
    │   ├── cleaning_pipelines.joblib            # Fitted sklearn cleaning pipelines
    │   ├── cleaned_outputs.joblib               # Cleaned train/val/test matrices + labels
    │   └── meta.joblib                          # Snapshot date, horizon, split config
    └── models/
        ├── trained_models_bundle.joblib         # Trained models, encoders, thresholds
        └── model_summary.csv                    # Validation and test metrics for all models
```

---

## Problem Formulation

The objective is to predict binary loan default outcomes (Default vs Non-default) using borrower financial, credit history, and loan characteristics available at origination.

**Target variable** derived from `loan_status`:
- `Fully Paid` → Non-default (0)
- `Charged Off` / `Default` → Default (1)
- Active and in-progress loans are excluded (no finalized outcome)

**Success criteria:**
- Accurately rank borrowers by default risk (ROC-AUC)
- Identify a meaningful share of actual defaults (recall)
- Maintain reasonable precision to avoid over-flagging good borrowers

**Intended users:** Loan officers, risk analysts, bank management, and compliance teams.

---

## Data Source

**Dataset:** Lending Club Loan Data
**Source:** [Kaggle – wordsforthewise](https://www.kaggle.com/datasets/wordsforthewise/lending-club)
**File used:** `accepted_2007_to_2018Q4.csv` (~2.5 GB, not included in repo)

The dataset includes borrower financial information, credit bureau data, loan contract characteristics, and loan performance outcomes for loans issued between 2007 and 2018.

**Known limitations:**
- Sample selection bias — only approved loans are included; rejected applicant outcomes are unobserved
- Self-reported income may contain noise or overstatement
- Moderate missingness in employment length and revolving utilization
- Public dataset lacks the full institutional underwriting variables used by real lenders

---

## Data Access & Regenerating Large Files

Several large files are **not included in this repository** due to file size constraints. They must be regenerated locally by running the notebooks in order.

### Files not in repo

| File | Size (approx.) | Generated by |
|------|----------------|--------------|
| `accepted_2007_to_2018Q4.csv` | ~2.5 GB | Download from Kaggle (see below) |
| `artifacts/cleaning_only/cleaning_pipelines.joblib` | ~50 MB | `data_wrangling.ipynb` |
| `artifacts/cleaning_only/cleaned_outputs.joblib` | ~1–2 GB | `data_wrangling.ipynb` |
| `artifacts/cleaning_only/meta.joblib` | < 1 KB | `data_wrangling.ipynb` |
| `artifacts/models/trained_models_bundle.joblib` | ~200 MB | `data_modeling.ipynb` |
| `artifacts/models/model_summary.csv` | < 1 KB | `data_modeling.ipynb` |

### Step 1 — Download the raw dataset

1. Go to: https://www.kaggle.com/datasets/wordsforthewise/lending-club
2. Download `accepted_2007_to_2018Q4.csv`
3. Place it at the path referenced in `data_wrangling.ipynb`
4. Update `DATA_PATH` in the first cell of `data_wrangling.ipynb`:
   ```python
   DATA_PATH = Path("/your/local/path/accepted_2007_to_2018Q4.csv")
   ```
   Or set the environment variable:
   ```bash
   export LENDING_CLUB_DATA_PATH="/your/local/path/accepted_2007_to_2018Q4.csv"
   ```

### Step 2 — Install dependencies

```bash
pip install -r requirements.txt
```

### Step 3 — Run notebooks in order

```bash
# Option A: Interactive Jupyter
jupyter notebook

# Option B: Headless execution
jupyter nbconvert --to notebook --execute notebooks/data_wrangling.ipynb \
    --output notebooks/data_wrangling_executed.ipynb \
    --ExecutePreprocessor.timeout=3600

jupyter nbconvert --to notebook --execute notebooks/data_modeling.ipynb \
    --output notebooks/data_modeling_executed.ipynb \
    --ExecutePreprocessor.timeout=3600
```

> **Timing:** `data_wrangling.ipynb` takes approximately 10–20 minutes on the full dataset. `data_modeling.ipynb` takes approximately 20–40 minutes due to XGBoost training with early stopping.

---

## How to Reproduce the Full Pipeline

### Prerequisites

- Python 3.10+ (developed on Python 3.13)
- ~5 GB free disk space
- Raw Lending Club CSV downloaded (see above)

### Notebook execution order

| Order | Notebook | Purpose |
|-------|----------|---------|
| 0 | `CleaningDataset_Loan.ipynb` | Initial cleaning, 10% stratified sample, SQLite DB, basic EDA |
| 1 | `data_wrangling.ipynb` | Full cleaning pipeline, feature engineering, time-based splits, artifact export |
| 2 | `data_modeling.ipynb` | Model training, encoding, evaluation, threshold tuning, model export |
| 3 | `data_visualization_static.ipynb` | Interactive per-loan default predictor |

### Expected artifacts after running notebooks 1–2

| Output | Location | Description |
|--------|----------|-------------|
| Cleaned feature matrices | `artifacts/cleaning_only/cleaned_outputs.joblib` | Train/val/test splits for all 3 model variants |
| Fitted pipelines | `artifacts/cleaning_only/cleaning_pipelines.joblib` | Sklearn pipelines (logistic, xgb_fund, xgb_full) |
| Pipeline metadata | `artifacts/cleaning_only/meta.joblib` | Snapshot date, horizon, split config |
| Trained models | `artifacts/models/trained_models_bundle.joblib` | 3 models + encoders + best-F1 thresholds |
| Metrics summary | `artifacts/models/model_summary.csv` | ROC-AUC, PR-AUC, Brier, F1 per model |

---

## How to Run the Interactive Predictor

`data_visualization_static.ipynb` is the final tool. It loads the trained artifacts and provides a per-loan default prediction interface.

### Prerequisites

The following files must exist before launching:

```
artifacts/models/trained_models_bundle.joblib
```

### Launch

```bash
pip install -r requirements.txt
jupyter notebook
```

Open `notebooks/data_visualization_static.ipynb` and run all cells (`Kernel → Restart & Run All`).

> If widgets are not rendering, run:
> ```bash
> jupyter nbextension enable --py widgetsnbextension
> ```

### What the tool provides

The interface is organized into **four input tabs**:

| Tab | Fields |
|-----|--------|
| **Borrower/Loan** | Loan amount, term, annual income, employment length, home ownership, income verification, loan purpose, borrower state |
| **Credit** | FICO range (low/high), earliest credit line, active accounts, total accounts, delinquencies in 2 years, public records |
| **Debt & Auto Calc** | Revolving balance, monthly debt payments, gross monthly income, revolving credit limit → DTI and revolving utilization are **auto-calculated** |
| **Full Model** | Interest rate, installment amount, loan grade, sub-grade *(enabled only for XGB Full)* |

> **Note:** Issue date (`issue_d`) is internally fixed to `Jan-2016` and does not require user input.

**Outputs per prediction:**
- Predicted probability of default (e.g., 14.32%)
- Risk band: Low (< 5%) / Moderate (5–15%) / Elevated (15–30%) / High (> 30%)
- Threshold-based verdict: LIKELY DEFAULT or LIKELY NON-DEFAULT
- Auto-calculated DTI and revolving utilization values used in the prediction
- Plotly gauge chart with color-coded risk zones and decision threshold marker
- **Human-in-the-loop disclaimer** showing validation F1, accuracy, recall, and precision at the operating threshold, with a note that predictions are advisory only and must not be used as the sole basis for approval or denial

---

## Database Design

The project uses a normalized relational SQLite database (`lending_club_sample_10pct.db`) based on a 10% stratified sample of the full dataset.

### Tables

| Table | Primary Key | Purpose |
|-------|-------------|---------|
| `loan` | `loan_id` | Loan contract details and target variable |
| `borrower` | `loan_id` (FK) | Borrower demographics and financial capacity |
| `credit_history` | `credit_history_id` | Creditworthiness indicators at origination |
| `debt_profile` | `debt_profile_id` | Debt burden and utilization metrics |

### Relationships

```
loan.loan_id ← borrower.loan_id
loan.loan_id ← credit_history.loan_id
loan.loan_id ← debt_profile.loan_id
```

Schema diagram: `docs/DB Schema.png`

---

## Data Science Cycle Summary

### Stage 0 — Data Acquisition & Database Construction
- Source: Kaggle Lending Club dataset (2007–2018)
- 10% stratified sample created to enable local development
- Normalized SQLite database with 4 tables constructed
- Data dictionary and DB schema documented

### Stage 1 — Data Exploration
- Descriptive statistics computed for all features (`.info()`, `.describe()`)
- Target distribution: ~80% non-default, ~20% default (moderately imbalanced)
- Key findings: FICO score, interest rate, and loan grade are the strongest predictors; annual income weaker than expected; strong multicollinearity among grade, sub-grade, and interest rate

### Stage 2 — Data Wrangling & Feature Engineering
- Custom sklearn transformers built for: percent-string parsing, date parsing, employment length extraction, FICO averaging, credit age derivation, rare-category grouping
- Derived features: `fico`, `credit_age_years`, `revol_to_loan`, `installment_to_monthly_income`, `open_to_total_ratio`, `fico_times_revolutil`, date parts
- Time-based train/val/test splits (no data leakage): train on earlier years, validate on next year, test on most recent year
- Two feature sets: Fundamental (borrower-side only) and Full (includes institutional grade/rate)

### Stage 3 — Modeling & Evaluation
- Three models trained: Logistic Regression (Fundamental), XGBoost (Fundamental), XGBoost (Full)
- Imbalanced classes handled with `class_weight="balanced"` (logistic) and `scale_pos_weight` (XGBoost)
- Evaluation: ROC-AUC, PR-AUC, Brier score, F1 at best-F1 threshold
- All models, encoders, and thresholds saved to `trained_models_bundle.joblib`

### Stage 4 — Visualization & Deployment
- Interactive per-loan default predictor built with `ipywidgets` and Plotly
- Four-tab input layout: Borrower/Loan, Credit, Debt & Auto Calc, Full Model
- DTI and revolving utilization auto-calculated from user-provided cash flow inputs
- Model leaderboard displayed on startup from the embedded summary table
- Risk output: predicted probability, risk band, gauge chart, and threshold-based verdict
- Human-in-the-loop disclaimer with live validation metrics (F1, accuracy, recall, precision) at operating threshold
- Deployed at: https://latte.rc.ufl.edu/engine/gallery/credit-risk-loan-default-prediction/

---

## Model Results

Full metrics from `artifacts/models/model_summary.csv`:

| Model | Val ROC-AUC | Val PR-AUC | Val Brier | Val F1 | Test ROC-AUC | Test PR-AUC | Test Brier | Test F1 |
|-------|------------|------------|-----------|--------|-------------|------------|-----------|---------|
| XGB (Full) | 0.7172 | 0.1558 | 0.2235 | 0.2307 | 0.7125 | 0.1415 | 0.2202 | 0.2128 |
| XGB (Fundamental) | 0.6723 | 0.1266 | 0.2192 | 0.1953 | 0.6669 | 0.1108 | 0.2109 | 0.1751 |
| Logistic (Fundamental) | 0.6250 | 0.1014 | 0.0816 | 0.1652 | 0.6227 | 0.0904 | 0.0777 | 0.1509 |

**Key observations:**
- XGB (Full) achieves the best ranking performance (ROC-AUC 0.717) by leveraging institutional grade and interest rate features
- XGB (Fundamental) is the more conservative model — it uses only borrower-side inputs and avoids relying on lender pricing signals
- Logistic Regression provides a well-calibrated probability baseline (lowest Brier score) with full interpretability
- Low F1 scores across all models reflect the inherent difficulty of the imbalanced credit risk problem and the conservative threshold chosen to manage recall

---

## Key Insights

- **Institutional features dominate predictive power:** Loan grade and interest rate are among the strongest predictors, indicating that lender pricing decisions already encode substantial risk information.

- **Borrower financials alone are less predictive:** Variables such as annual income and employment length provide limited signal compared to credit history and debt-related features.

- **Model tradeoff between performance and independence:** While XGB (Full) achieves the highest ROC-AUC, it relies on lender-assigned features. The Fundamental models offer a more independent assessment of borrower risk without using institutional signals.

- **Imbalanced classification remains challenging:** Low F1 scores across all models reflect the difficulty of identifying relatively rare default events while controlling false positives.

- **Probability estimates are more useful than binary predictions:** The model is most valuable as a ranking and risk-scoring tool rather than a strict approve/deny classifier.

---

## Diary Documentation

The `diary/` folder documents all stages of the project with technical decisions, tradeoffs, and reflections:

| Entry | Contents |
|-------|----------|
| `ProblemFormulation.txt` | Central question, success criteria, stakeholders |
| `DataAcquisition_Part1.txt` | Data source, collection method, known limitations |
| `DataAcquisition_Part2_DataBaseManagement.txt` | Database schema design decisions |
| `DataAcquisition_Part2_DataDictionary.txt` | Variable definitions and target construction |
| `DataExploration_Part1_Diary.md` | Descriptive statistics, missingness, value ranges |
| `DataExploration_Part2_Diary.txt` | Data quality issues identified and stakeholder questions |
| `DataExploration_Part3_Diary.txt` | Bivariate relationships, visual findings, surprises |
| `DataWrangling_Part1_Cleaning_Diary.txt` | Cleaning decisions, before/after comparisons, remaining risks |
| `DataWranglingP2_FeatureEngineering_Diary.txt` | Engineered features, removed features, justifications |
| `DataModelingP1_Fundamentals_Diary.txt` | Model choice rationale, assumptions, split strategy |
| `DataModelingP2_Evaluation_Diary.txt` | Metric selection, model comparison, feature importance |
| `DataVisualization_Diary.txt` | Dashboard story, layout rationale |
| `ModelDeployment_Diary.txt` | joblib vs pickle decision, reproducibility steps |
| `ProjectIteration_Diary.txt` | Feedback received, next steps, identified improvements |

---
## Limitations & Future Work

### Limitations

- **Sample selection bias:** The dataset includes only approved loans, meaning rejected applicants and their outcomes are not observed. This limits the model’s ability to fully represent the applicant population.

- **Self-reported data quality:** Variables such as annual income may contain inaccuracies or overstatements, introducing noise into the model.

- **Limited feature scope:** The dataset does not include macroeconomic factors (e.g., unemployment rate, interest rate environment) that can significantly influence default behavior.

- **Class imbalance:** Default events are relatively rare, making it difficult to achieve high recall without increasing false positives.

- **Potential information leakage in Full model:** Features like loan grade and interest rate may already reflect internal risk assessments, reducing the model’s independence.

---

### Future Work

- Incorporate macroeconomic variables to capture broader economic conditions
- Apply model explainability techniques (e.g., SHAP values) to improve interpretability
- Explore advanced imbalance handling methods such as focal loss or resampling techniques
- Extend the model to include rejected applications (if data becomes available)
- Deploy as a standalone web application (e.g., Streamlit) for improved accessibility

---

## Authors

**Yuanseng Choo** — ychoo@ufl.edu

Project repository: https://github.com/alexchooys/CAP5771-Ychoo.git

Live tool: https://latte.rc.ufl.edu/engine/gallery/credit-risk-loan-default-prediction/
