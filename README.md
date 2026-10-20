# Data Analytics — Hospital Readmission Rates

A data analytics project focused on identifying factors associated with hospital readmissions among patients with diabetes, using historical hospital encounter data from the United States.

## 1. Project Overview

### Context

Healthcare systems face challenges related to hospital readmissions, particularly when patients return to the hospital shortly after discharge. Early readmissions can increase healthcare costs, occupy hospital resources, and indicate potential difficulties in treatment or continuity of care.

This project investigates demographic, clinical, and hospital utilization factors associated with readmission within 30 days after discharge among patients with diabetes.

### Research Questions

**Central Question:**
- Which demographic, clinical, and hospitalization factors are associated with hospital readmissions within 30 days after discharge among patients with diabetes?

**Specific Analytical Questions:**
1. How does the length of hospital stay (`time_in_hospital`) relate to early readmission rates (<30 days)?
2. To what extent does a patient's prior healthcare utilization history (`number_inpatient`, `number_emergency`, `number_outpatient`) indicate a higher likelihood of readmission?
3. Does modification of medication regimens during hospitalization (`change`, `diabetesMed`, `insulin`) correlate with readmission risk?
4. Are there significant disparities in readmission patterns across different age brackets (`age`) and primary diagnostic categories (`diag_1`)?

### Objectives

- Understand the structure and characteristics of the hospital encounter dataset.
- Ingest the dataset reproducibly from external repositories.
- Assess data quality systematically (missing value representation, duplicate records, inappropriate types, categorical inconsistencies).
- Clean, standardize, and prepare a trustworthy dataset for downstream transformations and modeling.
- Export the resulting prepared dataset with documented quality metrics, keeping analytical aggregations and modeling for subsequent stages as required by the course guidelines.
- Demonstrate real-time stream ingestion and temporal window aggregations using Apache Spark Structured Streaming.

## 2. Dataset

### Source

The project uses the **Diabetes 130-US Hospitals for Years 1999–2008** dataset, obtained through Kaggle.

- **Kaggle dataset:** [Diabetes Hospital Readmission Dataset](https://www.kaggle.com/datasets/razanihababdellatif/diabetes-hospital-readmission-dataset)
- **Original source:** [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/296/diabetes+130-us-hospitals+for+years+1999-2008)
- **Main file:** `diabetic_data.csv`
- **Auxiliary file:** `IDS_mapping.csv`
- **Format:** CSV
- **Initial size:** 101,766 hospital encounters and 50 attributes.

The dataset covers hospital encounters recorded across 130 hospitals in the United States between 1999 and 2008.

Each row represents a hospital encounter, identified by `encounter_id`. A single patient may appear in multiple records.

### Main Variables

| Category | Variables | Description |
|---|---|---|
| Patient demographics | `age`, `gender`, `race` | Patient characteristics. |
| Hospital encounters | `encounter_id`, `patient_nbr` | Encounter and patient identifiers. |
| Hospital utilization | `time_in_hospital`, `number_inpatient`, `number_outpatient`, `number_emergency` | Length of stay and previous healthcare utilization. |
| Diagnoses | `diag_1`, `diag_2`, `diag_3`, `number_diagnoses` | Recorded diagnoses and diagnosis count. |
| Medication | `insulin`, `metformin`, `change`, `diabetesMed` | Medication information and changes during hospitalization. |
| Readmission outcome | `readmitted` | Readmission status: `<30`, `>30`, or `NO`. |

## 3. Data Requirements and Classification

The project considers the following information relevant to investigating hospital readmissions:

- Patient demographics, including age and gender.
- Previous hospitalizations and healthcare utilization.
- Primary and secondary diagnoses.
- Length of hospital stay.
- Medications and medication changes.
- Chronic conditions and related clinical information.
- Treatment received.
- Follow-up appointments, when available.
- Whether the patient was readmitted.

### Data Classification

| Data | Classification |
|---|---|
| Age | Structured |
| Gender | Structured |
| Diagnoses | Structured |
| Hospitalization information | Structured |
| Medications | Structured |
| Medical reports | Unstructured |
| Doctors' notes | Unstructured |

The selected dataset primarily contains structured data in tabular format. Medical reports and doctors' notes are part of the broader information requirements but are not identified as separate fields in the current dataset.

## 4. Type of Analytics

**Primary approach: Predictive Analytics**

The project aims to investigate factors associated with hospital readmissions and establish a prepared dataset for future predictive modeling.

Potential future approaches include classification algorithms such as Logistic Regression, Decision Trees, and XGBoost. Model development and evaluation are outside the scope of RA1.

## 5. Project Scope — RA1

The first stage focuses on data ingestion, understanding, quality assessment, cleaning, and preparation.

### Workflow

1. **Data ingestion:** Obtain the dataset from Kaggle using KaggleHub.
2. **Initial inspection:** Examine the dataset dimensions, columns, data types, and missing values.
3. **Data quality assessment:** Identify missing values, duplicated records, and inappropriate data types.
4. **Data cleaning:** Standardize missing-value representations, assess sparse columns, and correct identifier data types.
5. **Validation:** Verify the results of the cleaning procedures and document the prepared dataset.

### Initial Data Quality Findings

The initial inspection identified the following issues:

- Missing values represented by `?`.
- High proportions of missing values in `weight`, `payer_code`, and `medical_specialty`.
- Missing values in diagnostic and laboratory-result fields.
- Numeric data types used for encounter and patient identifiers.
- Age represented as categorical intervals.
- No duplicated encounter identifiers or completely duplicated rows in the initial dataset.

The cleaning decisions and validation results are documented in the RA1 notebook.

## 6. Technologies and Dependencies

The project uses Python and the following libraries:

- **Pandas:** Data loading, inspection, and manipulation.
- **NumPy:** Missing-value handling.
- **KaggleHub:** Dataset retrieval and loading from Kaggle.

Dependencies are listed in `requirements.txt`.

### Installation

Clone the repository:

```bash
git clone https://github.com/HelenLauren/Data-analytics.git
cd Data-analytics
```

Install the required dependencies:

```bash
python -m pip install -r requirements.txt
```

### Dataset Ingestion

The dataset can be retrieved programmatically using KaggleHub:

```python
import kagglehub
from kagglehub import KaggleDatasetAdapter
import pandas as pd
import numpy as np

file_path = "diabetic_data.csv"

df = kagglehub.dataset_load(
    KaggleDatasetAdapter.PANDAS,
    "razanihababdellatif/diabetes-hospital-readmission-dataset",
    file_path,
)

print("Dataset dimensions:", df.shape)
display(df.head())
```

The expected initial dimensions are 101,766 rows and 50 columns.

KaggleHub may require internet access and appropriate access to the dataset. The local cache directory is managed by the library.

## 7. Repository Structure

The repository is organized around the project documentation, notebook, and dependency list.

```text
Data-analytics/
├── README.md
├── requirements.txt
├── HealthData.ipynb
└── data/
    └── processed/
        └── diabetic_data_clean.csv
```

The prepared dataset `diabetic_data_clean.csv` is exported after the cleaning and validation steps in `HealthData.ipynb`, ensuring that the resulting data is ready for downstream analytical pipelines.

## 8. Project Deliverables

The RA1 deliverables include:

- Jupyter Notebook (`.ipynb`) with the implemented and executed data ingestion and preparation workflow.
- Documentation of the analytical problem and research questions.
- Dataset source and ingestion procedure.
- Data quality assessment and cleaning decisions.
- Validation of the prepared dataset.
- Instructions for reproducing the workflow.

Subsequent project stages will build upon the prepared dataset to perform transformations, analysis, and predictive modeling.

## 9. Academic Project

- **Course:** Data Analytics — Problem-Based Learning (PjBL)
- **Project:** Readmission Rates
- **Institution:** Pontifical Catholic University of Paraná (PUCPR)
- **Team:** Bruno Viccenzo Mazetto, Helen Lauren Bonato, and Letícia Bastias Fabri.

## 10. References

- [Kaggle — Diabetes Hospital Readmission Dataset](https://www.kaggle.com/datasets/razanihababdellatif/diabetes-hospital-readmission-dataset)
- [UCI Machine Learning Repository — Diabetes 130-US Hospitals for Years 1999–2008](https://archive.ics.uci.edu/dataset/296/diabetes+130-us-hospitals+for+years+1999-2008)
- [KaggleHub Documentation](https://github.com/Kaggle/kagglehub)
