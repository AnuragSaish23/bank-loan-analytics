# Preprocessing Decisions Log

> This document records every data cleaning decision made during preprocessing, with business justifications.

## Overview

| Column | Issue Found | Decision | Justification |
|--------|-------------|----------|---------------|
| `person_age` | Max = 144 (impossible) | Filter ≤ 70 | Reasonable max age for loan applicants |
| `person_emp_exp` | Max = 125 (impossible) | Filter ≤ 50 | Max working career ~50 years |
| `person_income` | Max = $7.2M (extreme outlier) | Filter ≤ $300,000 | Focus on typical borrowers |

---

## Detailed Decision Log

### 1. Data Type Fixes
- **Finding:** All data types were already correct
- **Action:** No changes needed
- **Columns checked:** 14 columns (7 numerical, 5 categorical, 2 binary)

### 2. Missing Values
- **Finding:** No missing values in any column
- **Action:** No imputation needed

### 3. Outlier Handling

#### Age Filter (≤ 70)
- **Issue:** Maximum age was 144 years (impossible)
- **Decision:** Remove rows where `person_age > 70`
- **Justification:** 70 is a reasonable retirement age; banks typically don't issue loans to very elderly applicants
- **Impact:** Minimal rows removed

#### Employment Experience Filter (≤ 50)
- **Issue:** Maximum experience was 125 years (impossible)
- **Decision:** Remove rows where `person_emp_exp > 50`
- **Justification:** If someone starts working at 18-20 and retires at 65-70, max experience is ~50 years
- **Impact:** ~12 rows removed

#### Income Filter (≤ $300,000)
- **Issue:** Maximum income was $7.2 million (extreme outlier)
- **Decision:** Remove rows where `person_income > 300,000`
- **Justification:** Focus on typical borrowers; $300K covers high earners (doctors, executives) without including outliers that skew analysis
- **Impact:** ~200 rows removed

### 4. Derived Columns

| Column | Formula | Purpose |
|--------|---------|---------|
| `age_group` | Bins: 18-25, 26-35, 36-45, 46-55, 56-70 | Demographic segmentation |
| `income_bracket` | Low (<30K), Medium (30-60K), High (60-100K), Very High (>100K) | Financial segmentation |
| `credit_category` | Poor (≤580), Fair (581-670), Good (671-740), Excellent (>740) | Industry-standard credit bands |

### 5. Target Variable Interpretation
- **Validation Method:** Checked `loan_status` distribution by `previous_loan_defaults_on_file`
- **Finding:** 100% of applicants with previous defaults have `loan_status = 0`
- **Interpretation:** 
  - `loan_status = 0` → Defaulted (bad outcome)
  - `loan_status = 1` → Repaid successfully (good outcome)

---

## Summary Statistics

| Metric | Value |
|--------|-------|
| **Original rows** | 45,000 |
| **Final rows** | 44,625 |
| **Rows removed** | 375 (0.83%) |
| **Original columns** | 14 |
| **Final columns** | 17 |
| **Derived columns added** | 3 |
