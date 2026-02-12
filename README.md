# nurse-staffing-hospital-quality
## Nurse Staffing Levels and Hospital Quality: Linking Workforce Capacity to Patient Outcomes and Experience

Hospital Staffing Intensity and Hospital Quality
SIADS 593 – Applied Data Science Project

Overview

This project examines whether hospital quality outcomes are associated with:
  Internal staffing intensity (hospital-level workforce capacity), and
  External labor market conditions (regional registered nurse supply).
Using national CMS hospital data and Bureau of Labor Statistics workforce data, we evaluate whether variation in CMS Overall Star Ratings reflects hospital-level staffing decisions, broader regional labor constraints, or both.

The unit of analysis is the hospital.

## Research Questions

  Are hospitals with higher staffing intensity associated with higher CMS Overall Star Ratings?

  Does regional registered nurse (RN) workforce supply help explain differences in hospital quality across markets?

By distinguishing internal staffing capacity from external workforce availability, this project assesses whether hospital quality variation is driven more by organizational decisions or labor market structure.

# Data Sources
## 1. CMS Hospital Provider Cost Report
  Source: https://data.cms.gov/provider-compliance/cost-report/hospital-provider-cost-report
  Annual hospital financial and operational filings submitted to CMS.
Key Fields Used
  Provider CCN — hospital identifier (join key)
  FTE - Employees on Payroll — total workforce
  Total Days (V + XVIII + XIX + Unknown) — inpatient utilization
  Number of Beds — hospital capacity
  Medicare CBSA Number — geographic linkage
  ## Constructed Staffing Metrics

      df['fte_per_1000_days'] = (
          df['FTE - Employees on Payroll'] /
          df['Total Days (V + XVIII + XIX + Unknown)']
      ) * 1000
      
      df['fte_per_bed'] = (
          df['FTE - Employees on Payroll'] /
          df['Number of Beds']
      )

These capture staffing intensity relative to utilization and structural capacity.

## Caveats
  FTE includes clinical and non-clinical staff.
  Fiscal years vary by hospital.
  Extreme ratios occur for low-volume hospitals.
  Data lag of ~12–18 months.



## 2. CMS Hospital Overall Star Ratings
Source: https://data.cms.gov/provider-data/dataset/xubh-q36u
Key Fields
  Facility ID (= Provider CCN)
  Hospital overall rating (1–5 stars)
  The overall rating combines five measure groups:
  Mortality (22%)
  Safety of Care (22%)
  Readmission (22%)
  Patient Experience (22%)
  Timely & Effective Care (12%)

## Caveats
  Not all hospitals receive a rating.
  Methodology changed in 2021 (peer grouping).
  COVID years may influence performance measures.



## 3. BLS Nursing Workforce Data
Source: https://www.bls.gov/oes/tables.htm
Registered Nurse occupational code: 29-1141
Key Fields
  AREA — MSA or state
  TOT_EMP — RN employment
  JOBS_1000 — RNs per 1,000 jobs
  A_MEAN — mean annual wage
Used to measure regional nursing labor supply.

## Caveats
  Suppressed values common in small MSAs.
  State-level data more complete than MSA-level.
  3-year rolling averages.
  Reflects regional context, not hospital-specific staffing.

## Data Integration
Join Keys
From	To	Key
Cost Report	    Star Ratings	    Provider CCN = Facility ID
Cost Report	    BLS	Medicare      CBSA Number = AREA (fallback: State)

After merging:
  Star ratings converted to numeric.
  Hospitals missing staffing or ratings excluded.
  Top 1% staffing outliers trimmed for robustness checks.
Final analytic dataset: analysis_dataset.csv

## Methods

The analysis includes:
  Correlation analysis
  Distributional assessment of staffing intensity
  Group means by star rating
  Hospital-level OLS regression
  Models including regional RN supply
  State-level aggregation analysis
  Interaction analysis (staffing × RN supply group)

Primary model:
Hospital Overall Rating ~ Staffing Intensity + RN Workforce Supply

## Key Findings
  Staffing intensity is positively associated with hospital star ratings.
  The magnitude of the association is modest but statistically significant.
  Regional RN workforce supply shows little independent association with hospital star ratings.
  Staffing explains only a small portion of total quality variation.

Interpretation:

Hospital quality appears more closely associated with internal staffing intensity than with broader regional RN supply conditions. However, staffing alone does not explain most variation in hospital ratings.

## Limitations
  Staffing measure includes non-clinical employees.
  Observational design (no causal inference).
  Potential fiscal-year misalignment.
  Star rating methodology changes over time.
  Regional RN supply is contextual, not hospital-specific.

## Repository Structure
01_CMS_CostReport_EDA.ipynb
02_CMS_Ratings_EDA.ipynb
03_BLS_Workforce_EDA.ipynb
04_Data_Merge.ipynb
05_Analysis.ipynb

cost_report_clean.csv
ratings_clean.csv
bls_msa_rn_clean.csv
bls_state_rn_clean.csv
analysis_dataset.csv
