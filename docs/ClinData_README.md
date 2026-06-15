# GP2 Clinical Data — Release 12

**Using and Interpreting GP2 Clinical Data**  
*GP2 Clinical Data Team · Global Parkinson's Genetics Program*

---

## Table of Contents

1. [Overview](#1-overview)
2. [File Contents](#2-file-contents)
3. [Master Key: Phenotypes and Demographics](#3-master-key-phenotypes-and-demographics)
   - [Study Arms and Types](#31-study-arms-and-types)
   - [Phenotype Columns](#32-phenotype-columns)
4. [How Phenotypes Are Assigned](#4-how-phenotypes-are-assigned)
   - [Mapping Variables](#41-mapping-variables)
   - [Mapping Reference Table](#42-mapping-reference-table)
5. [Common Analysis Scenarios](#5-common-analysis-scenarios)
6. [Extended Clinical Dataset](#6-extended-clinical-dataset)
   - [Key Structural Variables](#61-key-structural-variables)
   - [Available Data Domains](#62-available-data-domains)
7. [Using the Clinical Summary Report](#7-using-the-clinical-summary-report)
8. [Using the Data Dictionary](#8-using-the-data-dictionary)
9. [Best Practices](#9-best-practices)
10. [Known Caveats and Limitations](#10-known-caveats-and-limitations)
11. [Contact and Support](#11-contact-and-support)

---

## 1. Overview

This README covers two core data files released as part of GP2 Release 12: the **master key** and the **extended clinical dataset**. Together, these files support genetics-linked clinical analyses across a large, multi-study cohort.

**Master key** — Contains all participants with genetics data across studies. Provides core demographics (age, sex, race), genetics data availability flags, and harmonized phenotype and diagnosis information. This is the authoritative source for participant-level phenotype and demographic information.

**Extended clinical dataset** — Contains harmonized clinical data from multiple studies, including clinical scales, questionnaires, and longitudinal visit records. Each row represents one observation per participant per visit. Not all participants in the master key have extended clinical data, but all participants in the extended dataset have accompanying genetics data.

> The data dictionary lists all captured items and scales. Some variables may not appear in the dataset because they have not yet been collected from any of the included studies.

---

## 2. File Contents

| File | Description |
|------|-------------|
| `master_key_release12_final_vwb.csv` | Master key with core clinical data for all samples |
| `r12_extended_clinical_data.csv` | Detailed clinical data: scales, questionnaires, longitudinal measures |
| `master_key_release_data_dictionary.csv` | Variable definitions for the master key |
| `R12_clinical_report.html` | Overall and per-study breakdown of clinical modalities |
| `README` | This document |

---

## 3. Master Key: Phenotypes and Demographics

The master key is the recommended starting point for any analysis. It contains participant-level variables including age, sex, age of onset and diagnosis, basic family history, diagnosis, study type, and standardized phenotype columns.

### 3.1 Study Arms and Types

The `study_arm` variable refers to the arm based on cohort-specific enrollment criteria. The `study_type` variable classifies each study arm into one of the following GP2-standardized categories:

| Study Type | Description |
|------------|-------------|
| **Case/Control** | Recruited as standard PD case and healthy control |
| **Prodromal** | Recruited as participants at risk for PD (e.g., RBD cohorts); not all have converted to PD |
| **Genetically Enriched** | Recruited based on known genetic risk variants (e.g., LRRK2 carriers) |
| **Monogenic** | Recruited PD families or individuals suspected of a monogenic cause (strong family history and/or young age of onset) |
| **Population Cohort** | Population-based cohort studies (not PD-focused recruitment) |
| **Brain Bank** | Post-mortem brain donation cohorts |

> **Note:** Case/Control study type is most suitable for a Case/Control GWAS. Identify them using `study_type == 'Case/Control'`.

### 3.2 Phenotype Columns

Because each contributing study uses its own terminology for study arms and diagnoses, GP2 has created harmonized phenotype columns to standardize participant classification across the entire dataset. The phenotype is not strictly determined by diagnosis alone, but also accounts for recruitment criteria for any given cohort.

| Column | Values | Purpose / When to Use |
|--------|--------|-----------------------|
| `GP2_phenotype` | PD, Control, MSA, PSP, DLB, CBD/CBS, FTD, etc. | Provided by site. Most granular; use for diagnosis-specific analyses. |
| `GP2_phenotype_for_qc` | PD, Control, Other | Simplified for genetic quality control. Flags samples to exclude from certain analyses (e.g., prodromal, population controls). |
| `GP2_PHENO` | Same as `GP2_phenotype`, with study-type modifications | Combines `study_type` and `GP2_phenotype`. **Recommended for pooled analyses**; correctly labels genetically enriched/monogenic cases. |

> **Important:** `GP2_PHENO` is standardized to distinguish study types alongside the phenotype. For example, monogenic and genetically enriched PD cases are labeled `'Affected_PD'` (not `'PD'`) and controls are labeled `'Unaffected'` in `GP2_PHENO`. Using the `PD`/`Control` labels for these study types will exclude valid cases. It is recommended that you check `study_type` if using `GP2_phenotype` for sample selection, or reference `GP2_PHENO` for your analyses.

---

## 4. How Phenotypes Are Assigned

[Phenotype harmonization](https://github.com/GP2code/GP2-Data-Dictionary/blob/main/docs/gp2_phenotype.md) is a two-stage process. First, contributing study teams map their free-text study arm and diagnosis labels to standardized GP2 variables when uploading sample manifests. Second, GP2 derives additional mapping variables for QC and analysis purposes.

### 4.1 Mapping Variables

- `study_arm` (free text) → `study_type` (standardized): Assigned by contributors
- `diagnosis` (free text) → `GP2_phenotype` (standardized): Assigned by contributors
- `GP2_phenotype_for_qc`: Derived from `GP2_phenotype`; used to exclude samples from analyses where study type creates ambiguity
- `GP2_PHENO`: Derived from `study_type` and `GP2_phenotype`; recommended column for defining analysis sets

### 4.2 Mapping Reference Table

The table below illustrates how free-text study arm and diagnosis values are mapped to the three GP2 phenotype columns across representative study types. Use this table to determine the correct case definition for your analysis.

| `study_arm` (text) | `study_type` (assigned) | `diagnosis` (text) | `GP2_phenotype` (assigned) | `GP2_phenotype_for_qc` (assigned) | `GP2_PHENO` (assigned) |
|--------------------|-------------------------|--------------------|---------------------------|----------------------------------|------------------------|
| PD arm | Case/Control | Parkinson's Disease | PD | PD | PD |
| Control | Case/Control | No NDD | Control | Control | Control |
| PSP arm | Case/Control | PSP | PSP | Other | PSP |
| CBS / CBD likely | Case/Control | CBD | CBS/CBD | Other | CBS/CBD |
| RBD arm | Prodromal | Prodromal | Prodromal | Other | Prodromal |
| RBD arm | Prodromal | PD | PD | PD | PD |
| RBD arm | Prodromal | MSA | MSA | Other | MSA |
| ABC Town study | Population Study | Control* | Population Control | Control | Population Control |
| ABC Town study | Population Study | MCI | Undetermined-MCI | Other | Undetermined-MCI |
| ABC Town study | Population Study | Dementia | Undetermined-Dementia | Other | Undetermined-Dementia |
| ABC Town study | Population Study | AD | AD | Other | AD |
| ABC Town study | Population Study | PD | PD | PD | PD |
| EFG Brain bank | Brain Bank | PD | PD | PD | PD |
| EFG Brain bank | Brain Bank | AD | AD | Other | AD |
| EFG Brain bank | Brain Bank | AD and PD | Mix | Other | Mix |
| LRRK2 - Control | Genetic Enrichment | Control | Control | Other | Unaffected |
| LRRK2 - Control | Genetic Enrichment | Prodromal (Anosmia) | Control | Other | Unaffected |
| LRRK2 - PD | Genetic Enrichment | PD | PD | Other | Affected_PD |
| Family Based Recruit | Monogenic | PD | PD | Other | Affected_PD |
| Family Based Recruit | Monogenic | Control** | Control | Other | Unaffected |

\* No NDD comorbidities reported; not MCI or Dementia  
\*\* No NDD diagnosis (Unaffected)

---

## 5. Common Analysis Scenarios

The table below provides guidance on which variables and filters to use for common analytical use cases. Always confirm case counts and modality coverage using the clinical summary report before finalizing an analysis plan.

| Analysis Goal | Recommended Filter | Notes |
|---------------|--------------------|-------|
| **GWAS / case-control (PD vs Control)** | `GP2_phenotype_for_qc == 'PD'` or `'Control'` | Excludes prodromal, population controls, brain banks, and `'Other'` phenotypes. Use `GP2_PHENO` or check `study_type` to correctly handle monogenic/enriched. |
| **Monogenic or enriched cohort analysis** | `GP2_PHENO == 'Affected_PD'` or `'Unaffected'` | Standard `'PD'`/`'Control'` labels do not apply to these study types. |
| **Longitudinal progression** | `visit_month >= 0`, grouped by `GP2ID` | Drop rows without visit information; use `visit_month = 0` for baseline. |
| **Brain bank / post-mortem** | `study_type == 'Brain Bank'` | Identify via `study_type`; not suitable for longitudinal analyses. |
| **Cross-diagnosis comparison (PD vs PSP vs DLB) or including population controls in GWAS** | `GP2_phenotype` + `study_type` or `GP2_PHENO` | Use the most granular phenotype column; confirm case counts via the clinical summary report. |

> For any pooled multi-study analysis, review the per-study breakdown in the clinical summary report to understand differences in study design, follow-up duration, and available modalities before combining data.

---

## 6. Extended Clinical Dataset

The extended clinical dataset provides longitudinal clinical data at the visit level. It is structured so that each row represents a single observation for a participant at a specific visit.

### 6.1 Key Structural Variables

| Variable | Description |
|----------|-------------|
| `study` | Name of the contributing study |
| `GP2ID` | Unique participant identifier — use this to link to the master key |
| `visit_month` | Months elapsed since baseline visit. `0` = baseline. Negative values are possible for screening visits. |
| `visit_name` | Human-readable visit label (e.g., `'Year 1'`). Not always provided; use `visit_month` for programmatic analysis. |

> **Recommendation:** Drop rows where `visit_month` is missing and use `visit_month = 0` to define baseline, rather than relying on `visit_name`, which is not standardized across studies.

### 6.2 Available Data Domains

The extended dataset includes variables across the following clinical domains. Coverage varies by study — use the data dictionary and summary report to identify complete variables for your analysis.

- **Demographics** (also available in master key; master key preferred for age, sex, race)
- **Family and medical history**
- **PD diagnosis history and motor onset**
- **Motor scales:** MDS-UPDRS (Parts I–IV), Hoehn & Yahr
- **Cognitive scales:** MoCA, MMSE, and neuropsychological batteries
- **Non-motor scales:** RBD screening, autonomic measures, depression/anxiety instruments
- **Follow-up and visit metadata**

> Some studies provide only summary scores for questionnaires (e.g., total MDS-UPDRS score) rather than individual item responses. Check variable availability per study in the clinical summary report before building item-level analyses.

---

## 7. Using the Clinical Summary Report

Open `R12_clinical_report.html` in a web browser before beginning analysis.

The report includes:

- Counts of studies and unique participants
- Summary statistics: median follow-up time, IQR, mean ± SD, frequency counts for categorical variables
- Group summary: key modalities relevant to clinical-genetics analysis
- Per-study breakdown: number of observations available for each modality

Use the report to:

- Identify variables with sufficient completeness for analysis
- Understand the cross-sectional and longitudinal depth of the dataset
- Detect missingness patterns that may affect analytic choices
- Explore distributions before committing to a variable

---

## 8. Using the Data Dictionary

The data dictionary is maintained as a [GP2code GitHub repository](https://github.com/GP2code/GP2-Data-Dictionary). The `.csv` file can be browsed online or downloaded for local use. It is searchable by variable name, modality, or keyword.

| Column | Description |
|--------|-------------|
| `Modality` | Classification of the variable (e.g., Demographics, Motor, Cognitive) |
| `Item` | Variable name as it appears in the dataset |
| `Description` | Plain-language explanation of what the variable captures |
| `ItemType` | Data type: `string` or `numeric` |
| `Required` | Whether the variable is nullable (i.e., may have missing values) |
| `Values` | Value mapping, e.g., `1 = Yes`, `0 = No` |

> Search the dictionary by `Modality` to find all variables in a clinical domain (e.g., all cognitive scale items). Cross-reference with the summary report to confirm which variables are populated in this release.

---

## 9. Best Practices

- Review the data dictionary and clinical summary report before writing analysis code
- Use `visit_month = 0` to define the baseline visit; do not rely on `visit_name`
- Group by `GP2ID` + `visit_month` for longitudinal analysis; each combination is a unique observation (with the exception of ON/OFF visits for MDS-UPDRS assessments)
- Use `GP2_PHENO` — do not rely on `GP2_phenotype` alone — when defining cases and controls in pooled analyses
- Use the master key as the source of truth for age, sex, race, diagnosis, and phenotype; prefer it over corresponding variables in the clinical file
- Verify monogenic and genetically enriched cases are labeled `'Affected_PD'` / `'Unaffected'` in `GP2_PHENO` before case-control analyses
- Use categorized or derived variables when available rather than constructing them from raw items
- Consult the [phenotype mapping table](#42-mapping-reference-table) before defining inclusion/exclusion criteria

---

## 10. Known Caveats and Limitations

- **Visit intervals** vary across studies and participants due to loss to follow-up and differences in study protocol. Treat `visit_month` as the primary time variable.
- **Missing `visit_month`:** Rows where `visit_month` is missing cannot be reliably placed in a longitudinal sequence and should be dropped.
- **Duplicate demographic variables:** Age, sex, race, diagnosis, and phenotype exist in both the master key and the clinical file. The **master key** should be used for the most complete and up-to-date values.
- **Monogenic and genetically enriched labels:** These cases are labeled differently in `GP2_PHENO` (e.g., `'Affected_PD'` instead of `'PD'`). Failure to account for this will exclude valid cases in pooled analyses.
- **Brain bank cases** are post-mortem and not suitable for longitudinal progression analyses. Identify them using `study_type`.
- **Summary-only questionnaire scores:** Some studies provide only total scores (e.g., total MDS-UPDRS) rather than individual item responses. Check the summary report for item-level availability.
- **Data dictionary scope:** The dictionary enumerates all variables ever collected across GP2 studies. Not all variables will be present in the Release 12 dataset.
- **Population controls** from population cohort studies may have different characteristics than clinic-recruited controls. Use `GP2_phenotype_for_qc` to manage this distinction.

---

## 11. Contact and Support

| Resource | Link |
|----------|------|
| Weekly GP2 office hours | [Google Meet](http://meet.google.com/jdg-rmav-pwt) |
| Clinical data management team | [clinicaldata@gp2.org](mailto:clinicaldata@gp2.org) |
| Data dictionary & variable documentation | [GP2code GitHub](https://github.com/GP2code/GP2-Data-Dictionary) |
| Phenotype assignment documentation | [GP2 Phenotype Assignment](https://github.com/GP2code/GP2-Data-Dictionary/blob/main/docs/gp2_phenotype.md) |

---

*GP2 Clinical Data Team · Release 12 · Global Parkinson's Genetics Program*
