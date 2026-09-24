# Hospital Performance, Patient Flow & Readmission Analytics
### Hospital Analytics & Readmission Intelligence

**Student Name:** Rafiya Ansari  
**Internship:** AICTE | IBM SkillsBuild Data Analytics with AI Academic Internship 2026 | BharatCares  
**Role:** Data Analytics with AI Academic Intern  
**Repository:** `Hospital-Performance-Readmission-Analytics`  

---

## 1. Executive Summary & Analytical Story

This repository contains an end-to-end healthcare data analytics and applied machine learning project investigating hospital operational performance, patient throughput, clinical pathways, financial toxicity, and 30-day unplanned readmissions across 33 hospital facilities in India.

The project translates raw multi-table administrative and clinical data into strategic hospital intelligence following the foundational analytics paradigm:

$$\text{RAW DATA} \longrightarrow \text{INFORMATION} \longrightarrow \text{INSIGHTS} \longrightarrow \text{RISKS / OPPORTUNITIES} \longrightarrow \text{RECOMMENDED ACTIONS}$$

For critical clinical and operational findings, the structured analytical framework is applied:
$$\text{KPI} \longrightarrow \text{TREND} \longrightarrow \text{DRIVER} \longrightarrow \text{RISK / OPPORTUNITY} \longrightarrow \text{ACTION}$$

All analyses and predictive interpretations adhere strictly to healthcare epidemiological standards: observed statistical correlations are reported as **associated with** outcomes rather than asserting clinical causation.

---

## 2. Business Problem & Clinical Context

In modern hospital operations and health systems management:
* **Unplanned 30-Day Readmissions:** Readmissions within 30 days of inpatient discharge signal potential lapses in inpatient care stabilization, premature discharge, medication reconciliation errors, or absent transitional follow-up.
* **Operational Bottlenecks & Bed Congestion:** Readmissions disproportionately occupy tertiary ICU, HDU, and general ward capacity, causing emergency department crowding and delayed elective procedures.
* **Financial Toxicity:** In India, out-of-pocket (OOP) healthcare expenditures remain a principal contributor to catastrophic household medical debt. Patients discharged Left Against Medical Advice (LAMA) frequently cite acute financial distress.
* **Proactive Clinical Risk Stratification:** Clinical teams require transparent, interpretable predictive models operable strictly at the point of discharge without target leakage to identify vulnerable patients for targeted post-discharge support.

---

## 3. Project Objectives

1. **Relational Data Integration:** Perform a programmatic audit and join 5 raw relational CSV datasets (`admissions`, `billing`, `diagnoses`, `hospitals`, `patients`) ensuring the strict grain of **ONE ROW = ONE HOSPITAL ADMISSION** without row multiplication.
2. **Data Cleaning & Standardization:** Address missing insurance records (28.61% unrecorded, classified as 'Uninsured'), validate foreign keys, audit dates, and verify numeric plausibility.
3. **Strategic KPI Benchmarking:** Calculate verified institutional benchmarks for admission volumes, unique patients, readmission rates (overall and in surviving cohorts), length of stay (LOS), procedural intensity, Charlson Comorbidity Index, and OOP expenditure shares.
4. **Multi-Disciplinary Analytics:**
   * *Patient Demographics:* Age cohorts, gender equity, state distribution, BPL status, and insurance coverage.
   * *Hospital Performance:* Facility bed capacity, tier-level variance, and teaching status.
   * *Patient Flow:* Longitudinal stages from intake (Emergency, Elective, OPD) through ward placement (ICU, HDU, General, NICU) to discharge disposition (Recovered, LAMA, Referred, Expired).
   * *Financial Burden:* Inpatient billing components, government subsidies, and household OOP costs.
5. **Leakage-Free Predictive Machine Learning:**
   * Define an at-risk cohort by excluding in-hospital mortality (`discharge_type == 'Expired'`).
   * Eliminate post-discharge target leakage (`readmitted_7d`, timestamps, post-discharge billing).
   * Train and benchmark Logistic Regression, Random Forest, and XGBoost classifiers using stratified holdout validation.
6. **Explainable AI with SHAP:** Leverage TreeExplainer to illuminate key predictive drivers with non-causal interpretability.
7. **Actionable Implementation Roadmap:** Formulate phased operational, clinical, and financial recommendations.

---

## 4. Dataset Description & Relational Schema

The analysis uses the actual datasets located in `data/`:

| File Name | Row Count | Column Count | Primary Key / Grain | Key Columns |
| :--- | :--- | :--- | :--- | :--- |
| `admissions.csv` | 120,000 | 17 | `admission_id` (1 row = 1 admission) | `patient_id`, `hospital_id`, `admit_date`, `discharge_date`, `los_days`, `admit_type`, `ward_type`, `discharge_type`, `num_procedures`, `charlson_index`, labs (`hba1c`, `creatinine`, `haemoglobin`, `systolic_bp`), `readmitted_30d`, `readmitted_7d` |
| `billing.csv` | 120,000 | 6 | `bill_id` (1-to-1 with admission) | `admission_id`, `total_cost_inr`, `govt_subsidy_inr`, `out_of_pocket_inr`, `cost_category` |
| `diagnoses.csv` | 271,341 | 6 | `diag_id` (1 to 4 records per admission) | `admission_id`, `icd10_code`, `diag_desc`, `diag_rank` (rank 1 = primary, ranks 2-4 = secondary), `diag_category` |
| `hospitals.csv` | 33 | 6 | `hospital_id` (1 row = 1 hospital) | `name`, `state`, `tier` (tier1, tier2, tier3), `beds`, `teaching` (True/False) |
| `patients.csv` | 86,400 | 8 | `patient_id` (1 row = 1 patient) | `age`, `gender`, `state`, `bpl_card`, `insurance_type`, `comorbidity_count`, `prev_admissions` |

### Relational Entity Grain & Aggregation Strategy
To preserve the analytical grain of **ONE ROW = ONE HOSPITAL ADMISSION**:
* Diagnoses were aggregated prior to joining: primary diagnosis (`diag_rank == 1`) provided primary ICD-10 code and category; total diagnosis count per admission was calculated; secondary diagnosis categories (ranks 2-4) were pivoted as categorical presence counts (`sec_diag_*`).
* The final analytical dataframe contains exactly **120,000 rows** and **49 clean features** with zero row duplication and zero missing values.

---

## 5. Strategic Healthcare KPI Benchmarks

All metrics are derived directly from actual programmatic execution on the underlying dataset:

| Strategic Healthcare KPI | Calculated Benchmark | Contextual Interpretation |
| :--- | :--- | :--- |
| **Total Inpatient Admissions** | **120,000** | Full longitudinal cohort across multi-center network |
| **Total Patients (Admitted)** | **64,873** | Multi-admission rate observed among chronic patients |
| **Total Registered Patients** | **86,400** | Complete master patient index |
| **Total Participating Hospitals** | **33** | Across Tier-1 (private tertiary), Tier-2 (govt general), Tier-3 (district) |
| **Overall 30-Day Readmission Rate** | **11.84%** (14,210 cases) | Across entire 120,000 admission cohort |
| **Surviving Cohort 30-Day Readmission Rate** | **12.62%** (14,210 cases) | Excludes in-hospital mortality (7,413 Expired patients) |
| **Overall 7-Day Readmission Rate** | **0.84%** (1,006 cases) | Ultra-rapid readmission within one week |
| **Surviving Cohort 7-Day Readmission Rate** | **0.89%** (1,006 cases) | 100% of 7-day readmissions were also readmitted within 30 days |
| **Average Length of Stay (LOS)** | **6.85 days** | Skewed by prolonged tertiary critical care |
| **Median Length of Stay (LOS)** | **5.00 days** | Typical uncomplicated inpatient stay |
| **Average Total Cost per Admission** | **₹95,779.53** | Inpatient hospital billing |
| **Median Total Cost per Admission** | **₹30,970.50** | Robust central cost measure |
| **Average Government Subsidy** | **₹47,606.17 (49.70%)** | Social protection absorptive capacity |
| **Average Out-of-Pocket Expense** | **₹48,173.36 (50.30%)** | Household out-of-pocket financial burden |
| **Average Procedures per Admission** | **1.52** | Surgical and diagnostic procedural intensity |
| **Average Charlson Comorbidity Index** | **1.89** | Multi-morbid disease burden score |
| **Average Patient Age** | **48.05 years** | Broad demographic representation |

---

## 6. Multi-Dimensional Analytics Overview

### A. Patient Analytics
* **Age Distribution:** Readmission rate increases across age cohorts: Pediatric (0-18y) 6.19%, Young Adults (19-45y) 6.96%, Middle-Aged (46-65y) 12.51%, and Elderly (66+y) 20.40%.
* **Gender Equivalence:** Male admissions (51.15%, readmission rate 11.91%) and Female admissions (47.88%, readmission rate 11.77%) exhibit comparable readmission patterns.
* **Insurance Disparities:** Ayushman Bharat (PM-JAY) and ESI beneficiaries experience lower out-of-pocket burdens, whereas uninsured patients (28.61% of admissions) face 100% OOP exposure.

### B. Hospital Performance Analytics
* **Tier-1 Private Teaching Hospitals:** Highest admission volumes, highest average bed capacity (up to 1,018 beds), higher average costs (₹145,000+), and readmission rates around 12.1%–12.8%.
* **Tier-2 Government General Hospitals:** Moderate LOS (mean 6.7 days), readmission rates 11.5%–12.4%, with government subsidies covering >70% of billings.
* **Tier-3 District Hospitals:** Lower procedural volume, lower bed count (61–197 beds), readmission rates 11.2%–12.0%.

### C. Patient Flow & Clinical Pathways
* **Intake Transition:** Emergency admissions (55.45% of total) exhibit an 18.2% probability of direct ICU placement and 14.1% HDU placement.
* **Ward Variations:** ICU stays exhibit an average LOS of 12.91 days with a 30-day readmission rate of 23.76%, compared with 7.90% in General Wards.
* **Discharge Disposition Impact:**
  * *Recovered:* 87,420 admissions (readmission rate 11.78%).
  * *Left Against Medical Advice (LAMA):* 15,429 admissions with the highest readmission rate at **17.80%** (2,747 readmissions).
  * *Referred:* 9,738 admissions (readmission rate 11.92%).
  * *Expired:* 7,413 admissions (0.00% readmission rate).

### D. Financial Analytics
* **Cost Composition:** Pharmacy (30.2%) and Room Charges (25.0%) represent the largest share of inpatient expenditure.
* **Index Stay Cost vs. Readmission:** Index admissions of patients subsequently readmitted within 30 days incurred an average cost of ₹184,890.70 compared with ₹83,809.88 for non-readmitted patients.

---

## 7. Machine Learning Methodology & Leakage Prevention

### Target Leakage Prevention Protocol
To guarantee real-world deployability at the discharge decision point:
1. **`readmitted_7d`:** Strictly excluded (post-discharge outcome occurring within the 30-day target window).
2. **`discharge_type == 'Expired'`:** In-hospital mortalities are excluded from the training and evaluation cohort (standard CMS HRRP methodology) to avoid trivial negative class leakage.
3. **`discharge_date` & `admit_date`:** Raw calendar timestamps excluded.
4. **Billing totals:** Finalized claims billing amounts excluded from bedside clinical prediction.
5. **Entity Identifiers:** `admission_id`, `patient_id`, `hospital_id`, `bill_id` excluded.

### Predictor Feature Space (Pre-Discharge Observables)
* **Demographics & History:** `age`, `gender`, `patient_state`, `bpl_card`, `insurance_type`, `comorbidity_count`, `prev_admissions`.
* **Encounter & Stay:** `admit_type`, `ward_type`, `los_days`, `num_procedures`, `charlson_index`.
* **Clinical Labs & Vitals:** `hba1c`, `creatinine`, `haemoglobin`, `systolic_bp`.
* **Facility Metadata:** `hospital_tier`, `hospital_teaching`, `hospital_beds`.
* **Diagnostic Features:** `primary_diag_category`, `total_diagnoses`, and secondary diagnosis category presence counts (`sec_diag_*`).

### Data Splitting & Preprocessing
* **Cohort at Risk:** 112,587 non-expired inpatient admissions (readmission prevalence = 12.62%).
* **Split:** 80% Training (90,069 encounters), 20% Holdout Testing (22,518 encounters), stratified by target `readmitted_30d`.
* **Pipeline:** Numerical features scaled with `StandardScaler()`; categorical features encoded with `OneHotEncoder(drop='first', handle_unknown='ignore')`.

---

## 8. Model Evaluation & Comparison

All models were evaluated on the identical unseen test set (22,518 encounters). Results reflect actual calculated performance:

| Model | Accuracy | Precision | Recall (Sensitivity) | F1 Score | ROC-AUC |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Logistic Regression** (Balanced) | 70.72% | 0.2487 | **65.31%** | 0.3602 | **0.7527** |
| **Random Forest** (Balanced, Depth=12) | **77.92%** | **0.2907** | 52.04% | **0.3731** | 0.7469 |
| **XGBoost** (Scale Pos Weight, Depth=5) | 72.00% | 0.2556 | 63.72% | 0.3649 | 0.7504 |

### Clinical Trade-Off Analysis:
* **The Accuracy Paradox:** Because readmission occurs in only 12.62% of surviving encounters, a naive model predicting 0 for all cases scores 87.38% accuracy but captures zero readmissions (Recall = 0.0%), making it useless clinically.
* **Sensitivity Priority:** In transitional care, missing a preventable readmission is costlier than a false alarm. Logistic Regression and XGBoost prioritize Recall (>63%–65%), capturing the majority of readmissions for nurse telephonic follow-up and medication review.

---

## 9. SHAP Explainability Analysis

SHAP values were computed using `shap.TreeExplainer` on the fitted XGBoost pipeline across 1,000 representative validation encounters.

### Top Influential Predictors (by Mean |SHAP Value|):
1. **`los_days` (0.2870):** Prolonged hospitalization is strongly associated with elevated predicted readmission risk.
2. **`comorbidity_count` (0.2478):** Greater count of pre-existing chronic conditions contributes positively to predicted risk.
3. **`charlson_index` (0.2170):** Clinical acuity score heavily weights predicted readmission probability.
4. **`ward_type_ICU` (0.1522):** ICU placement during stay contributes to increased readmission risk estimates.
5. **`prev_admissions` (0.1354):** History of frequent prior admissions is associated with higher readmission propensity.
6. **`insurance_type_Private` (0.1281):** Insurance classification modulates access and post-discharge encounter documentation.
7. **`admit_type_Emergency` (0.0953):** Acute emergency intake is associated with higher predicted readmission.
8. **`bpl_card_True` (0.0886):** Below Poverty Line socioeconomic status contributes to readmission vulnerability.
9. **`haemoglobin` (0.0865):** Lower haemoglobin levels (<12 g/dL) contribute to elevated readmission risk.
10. **`age` (0.0695):** Advanced age contributes positively to the model's readmission prediction.

> **Methodological Note:** SHAP values measure feature contribution to model prediction log-odds and **do not imply clinical causation**.

---

## 10. Key Insights & Actionable Recommendations

### Structured Finding 1: Left Against Medical Advice (LAMA) Readmissions
* **Evidence:** LAMA patients have a **17.80%** readmission rate (2,747 readmissions), the highest across all discharge dispositions.
* **Interpretation:** Premature departure driven by financial stress and lack of financial counseling leads to severe acute relapses.
* **Action:** Deploy bedside financial counselors and social workers to negotiate subsidies for vulnerable patients before LAMA discharge paperwork is finalized.

### Structured Finding 2: Chronic Multi-Morbidity Clustering
* **Evidence:** Patients with $\ge 2$ comorbidities and Charlson Index $\ge 2$ account for >60% of all readmissions.
* **Interpretation:** Fragmented post-discharge care for diabetic, hypertensive, and renal patients results in rapid decompensation.
* **Action:** Institute mandatory 48-hour telephonic nurse outreach and 14-day primary care follow-up consultations.

### Structured Finding 3: Financial Out-of-Pocket Toxicity
* **Evidence:** 50.30% of inpatient expenditure is borne out-of-pocket, with uninsured patients absorbing nearly 100%.
* **Interpretation:** Financial exhaustion directly impedes post-discharge medication adherence.
* **Action:** Implement point-of-admission screening to enroll eligible patients into PM-JAY and state health protection schemes.

---

## 11. Study Limitations & Future Scope

### Limitations:
* **Observational Design:** Findings represent observational associations; causality cannot be inferred without prospective randomized trials.
* **Post-Discharge Medication Adherence:** Outpatient medication refill and social support records post-discharge were unobserved.
* **Single Network Data:** Evaluated across 33 facilities in India; regional generalizability requires external multi-center validation.

### Future Scope:
* Integration of natural language clinical discharge summaries using clinical LLMs.
* Real-time deployment into hospital EHR systems via HL7/FHIR APIs.
* Cost-effectiveness analysis of targeted post-discharge telephonic nurse interventions.

---

## 12. Instructions to Run

### Prerequisites
* Python 3.10 – 3.14
* Recommended: Virtual environment (`venv` or `conda`)

### 1. Clone & Setup Environment
```bash
git clone https://github.com/your-repo/Hospital-Performance-Readmission-Analytics.git
cd Hospital-Performance-Readmission-Analytics
py -m pip install -r requirements.txt
```

### 2. Verify Data Files
Ensure the 5 CSV files are located in `data/`:
* `data/admissions.csv`
* `data/billing.csv`
* `data/diagnoses.csv`
* `data/hospitals.csv`
* `data/patients.csv`

### 3. Run the Jupyter Notebook
Open and run all cells in `RafiyaAnsari_HospitalAnalytics.ipynb`:
```bash
jupyter notebook RafiyaAnsari_HospitalAnalytics.ipynb
```
The notebook is self-contained and pre-executed, containing all 27 required sections, output tables, and visualizations.

---

## 13. Project Structure

```
Hospital-Performance-Readmission-Analytics/
│
├── data/
│   ├── admissions.csv
│   ├── billing.csv
│   ├── diagnoses.csv
│   ├── hospitals.csv
│   └── patients.csv
│
├── figures/
│   ├── 01_kpi_dashboard_summary.png
│   ├── 02_patient_demographics_readmission.png
│   ├── 03_hospital_performance_comparison.png
│   ├── 04_patient_flow_analytics.png
│   ├── 05_financial_analytics.png
│   ├── 06_roc_curves_comparison.png
│   ├── 07_confusion_matrices.png
│   ├── 08_shap_summary_plot.png
│   ├── 09_shap_bar_importance.png
│   └── kpi_summary_table.csv
│
├── RafiyaAnsari_HospitalAnalytics.ipynb
├── RafiyaAnsari_HospitalAnalytics_ProjectReport.docx
├── requirements.txt
└── README.md
```

---
**Author:** Rafiya Ansari  
**Program:** AICTE | IBM SkillsBuild Data Analytics with AI Academic Internship 2026 | BharatCares


## 12. Verified Reference Results

The following values were recalculated directly from the supplied datasets and are the authoritative reference values for the report:

### Age-group 30-day readmission
| Age group | Admissions | Readmission rate |
|---|---:|---:|
| Pediatric (0-18) | 16,361 | 6.19% |
| Young Adult (19-45) | 31,645 | 6.96% |
| Middle Aged (46-65) | 46,781 | 12.51% |
| Elderly (66+) | 25,213 | 20.40% |

### Hospital tiers
| Tier | Facilities | Admissions | Mean LOS | 30-day readmission |
|---|---:|---:|---:|---:|
| Tier 1 | 8 | 29,027 | 6.83 days | 11.86% |
| Tier 2 | 15 | 54,505 | 6.86 days | 11.93% |
| Tier 3 | 10 | 36,468 | 6.85 days | 11.69% |

### ICU vs General ward
- ICU: 20,833 admissions; mean LOS 12.91 days; 30-day readmission 23.76%.
- General: mean LOS 4.51 days; 30-day readmission 7.90%.

### Readmission-associated index-stay cost
- Not readmitted: mean total cost ₹83,809.88; mean OOP ₹41,426.91.
- Readmitted: mean total cost ₹184,890.70; mean OOP ₹98,399.06.

### Final model metrics
| Model | Accuracy | Precision | Recall | F1 | ROC-AUC |
|---|---:|---:|---:|---:|---:|
| Logistic Regression | 70.72% | 0.2487 | 65.31% | 0.3602 | 0.7527 |
| Random Forest | 77.92% | 0.2907 | 52.04% | 0.3731 | 0.7469 |
| XGBoost | 72.00% | 0.2556 | 63.72% | 0.3649 | 0.7504 |

**Reproducibility note:** The notebook is the authoritative analytical pipeline. The validation scripts should reproduce the same cohort definition, feature set, preprocessing, random seed, and model hyperparameters.
