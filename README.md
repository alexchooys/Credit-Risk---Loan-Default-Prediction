# Credit Risk & Loan Default Prediction

## Table of Contents

- [Project Overview](#project-overview)
- [Repository Structure](#repository-structure)
- [Problem Formulation](#problem-formulation)
- [Data Source](#data-source)
- [Database Design](#database-design)
- [Data Exploration Highlights](#data-exploration-highlights)
- [Diary Documentation](#diary-documentation)
- [Conclusion](#conclusion)
- [Authors](#authors)

## Project Overview

This project investigates whether borrower financial and credit information can accurately predict loan default outcomes.

The central question guiding this analysis is:

- Can we accurately predict whether a loan applicant is likely to default based on financial and credit characteristics?

Loan defaults generate significant financial losses for lenders. By identifying high-risk borrowers early, institutions can improve underwriting decisions, reduce bad debt, and enhance risk management practices.

The project uses the publicly available Lending Club Loan Data dataset from Kaggle and focuses on structured financial and credit variables known at loan origination.

## Repository Structure

### Credit-Risk-Default-Prediction

#### -README.md

#### -diary/
    ProblemFormulation.txt
    DataAcquisition_Part1.txt
    DataAcquisition_Part2_DataBaseManagement.txt
    DataAcquisition_Part2_DataDictionary.txt
    DataExploration_Part1.md
    info_describe.png
    DataExploration_Part2_Diary.txt
    DataExploration_Part3_Diary.txt

#### -database/
    lending_club_sample_10pct.db

#### -docs/
    DB Schema.png
    data_dictionary.pdf

#### -notebooks/
    CleaningDataset_Loan.ipynb

#### -10% Loan Dataset
    lending_club_sample_10pct.csv

## Problem Formulation

The objective is to predict binary loan default outcomes (Default vs Non-default) using borrower financial, credit history, and loan characteristics.

### Success in this project means:
- Accurately identifying high-risk borrowers
- Reducing default rates
- Maintaining acceptance of low-risk borrowers 

### The target variable is derived from loan_status, where:
- Fully Paid → Non-default (0)
- Charged Off / Default → Default (1) 

## Data Source
Dataset: Lending Club Loan Data

Source: Kaggle – wordsforthewise 

### The dataset includes:
- Borrower financial information
- Credit bureau data
- Loan contract characteristics
- Loan performance outcomes

### Known Limitations
- Sample selection bias (only approved loans are observed)
- Self-reported income may contain noise 
- Missing values in some financial variables 
- Public dataset lacks institutional underwriting variables

## Database Design
The project uses a normalized relational database design to separate borrower, loan, credit, and debt information 

### Tables:
#### Loan (Central Table)
- loan_id (Primary Key)
- loan_status (target)
- loan_amnt
- term
- int_rate
- installment
- purpose
- grade
- sub_grade
- issue_d

#### Borrower
- borrower_id (PK)
- loan_id (FK)
- annual_inc
- emp_length
- home_ownership
- verification_status
- addr_state 

#### Credit History
- fico_range_low
- fico_range_high
- earliest_cr_line
- open_acc
- total_acc
- delinq_2yrs
- pub_rec

#### Debt Profile
- dti
- revol_bal
- revol_util

#### Database schema diagram:
    lending_club_sample_10pct.db

## Data Exploration Highlights

Extensive exploratory analysis was conducted and documented across three diary files.

### Target Distribution
- Non-default: ~80%
- Default: ~20%
- Moderately imbalanced but realistic for credit risk modeling

### Strong Predictors Identified
#### FICO Score
- Strong inverse relationship with default
- Nonlinear increase in default risk below ~650

#### Interest Rate
- Very strong positive relationship with default
- Nearly monotonic trend

#### Loan Grade
- Extremely clear ordinal separation (A lowest risk → G highest risk)

### Moderate Predictors
- Debt-to-Income Ratio (DTI) 
- Loan Term (60-month loans show higher default)

### Weaker Predictors
- Annual income was less predictive than expected after controlling for credit score

### Data Quality Issues Identified
- Extensive missingness in hardship-related columns 
- Moderate missingness in annual income and employment length 
- Multicollinearity among:
    - fico_range_low & fico_range_high
    - grade, sub_grade, int_rate 
- Outliers in income and revolving balance 

These findings informed feature selection and preprocessing decisions.

## Diary Documentation
The diary/ folder documents the full five-stage workflow:
1. Problem formulation
2. Data acquisition (sources and relevance)
3. Data acquisition II (database storage)
4. Data exploration
5. Reflection and next steps

## Conclusion
This project demonstrates a full end-to-end data science workflow:
- Problem formulation
- Data acquisition
- Database design
- Data exploration
- Risk interpretation

The findings confirm that credit quality variables (FICO, grade, interest rate) are the strongest predictors of loan default, while income and employment variables provide weaker incremental signal

## Authors
Yuanseng Choo -ychoo@ufl.edu

Project Link: https://github.com/alexchooys/CAP5771-Ychoo.git

# Thank You