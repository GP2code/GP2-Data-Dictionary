# GP2-Phenotype assignment

Each study has its own study arm and diagnosis definitions. To harmonize phenotypes across studies, we have created two mapping variables:
* study_arm (free text) --> `study_type`
* diagnosis (free text) --> `GP2_phenotype`

These assignments are made by our contributors when their study sample manifests were uploaded. 

Based on these two variables, we also created two additional mapping variables for quality control and analysis purposes:

* `GP2_phenotype_for_qc`: used for QC purposes to identify samples that need to be excluded from certain analyses (e.g., prodromal samples, population controls, etc.)
* `GP2_PHENO`: study_type and gp2_phenotypes are combined in one column. Could be used for analysis purposes to define study-specific analysis sets.

The example of mapping for study_arm and diagnosis to the three GP2 phenotype variables is provided in the table below.

| study_arm (text) | `study_type` (assigned) | diagnosis (text) | `GP2_phenotype` (assigned) | `GP2_phenotype_for_qc` (assigned) | `GP2_PHENO` (assigned) |
|---|---|---|---|---|---|
| PD arm | Case/(Control) study | Parkinson's Disease | PD | PD | PD |
| Control | Case/(Control) study | No NDD | Control | Control | Control |
| PSP arm | Case/(Control) study | PSP | PSP | Other | PSP |
| CBS / CBD likely | Case/(Control) study | CBD | CBS/CBD | Other | CBS/CBD |
| RBD arm | Prodromal | Prodromal | Prodromal | Other | Prodromal |
| RBD arm | Prodromal | PD | PD | PD | PD |
| RBD arm | Prodromal | MSA | MSA | Other | MSA |
| ABC Town study | Population Study | Control* | Population Control | Other | Population Control |
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


\* No NDD comorbidities reported as well as not MCI, Dementia   
** No NDD diagnosis (Unaffected)
