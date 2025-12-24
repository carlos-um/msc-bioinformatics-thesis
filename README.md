# MSc Bioinformatics Thesis – Lewy Body Disease DNA Methylation Analysis

This repository contains the scripts and results associated with my Master's thesis titled:

**"Application of Supervised Machine Learning for Predictive Modeling of Lewy Body Disorders Using DNA Methylation Profiles"**  
by **Carlos C. Ureña Mateo** (University of Murcia, MSc Bioinformatics, 2025).

## Overview

Lewy body diseases — including Parkinson’s disease (PD), Parkinson’s disease dementia (PDD), and dementia with Lewy bodies (DLB) — are neurodegenerative disorders for which disease-specific epigenetic alterations remain incompletely characterized. DNA methylation provides a high-resolution molecular layer to investigate whether these conditions can be distinguished from neurologically healthy controls based on epigenetic profiles.

In this thesis, a reproducible and modular bioinformatics pipeline was developed to analyze DNA methylation data using a hypothesis-driven strategy. Clinically curated gene panels were incorporated at the initial stages of the pipeline as prior biological knowledge to guide probe selection and reduce dimensionality before any statistical modeling.

Following this biologically informed filtering step, supervised machine learning models were applied to evaluate the discriminative capacity of panel-specific methylation profiles. Models were trained both at a global panel level and after stratification by functional genomic regions, enabling the detection of localized epigenetic signals associated with disease status rather than assuming shared methylation patterns across disorders.

## Modules

### 1. `train_test_split.ipynb`

Splits normalized DNA methylation data and corresponding clinical metadata into independent training and testing sets.

**Input**
- Normalized methylation matrix (samples × probes)
- Clinical metadata table

**Output**
- Training methylation matrix
- Testing methylation matrix
- Corresponding clinical metadata files

---

### 2. `extract_gene_panels.R`

Extracts high-confidence disease-associated gene panels for targeted probe filtering.

**Input**
- PanelApp TSV files (Genomics England)
- Curated CSV gene panel files

**Output**
- Gene symbol vectors containing high-evidence genes

---

### 3. `filter_methylation_by_genes.R`

Filters DNA methylation probes based on overlap with clinically relevant gene panels.

**Input**
- Methylation matrix
- Probe annotation file
- Gene panel list

**Output**
- Filtered methylation matrix containing panel-associated probes

---

### 4. `prepare_for_ml_global.R`

Prepares datasets for binary machine learning classification at the global gene level.

**Input**
- Filtered methylation matrix
- Clinical metadata

**Output**
- ML-ready training and testing files per contrast

---

### 5. `train_models_all()`

Trains and evaluates binary classification models using supervised machine learning.

**Implementation details**
- Implemented using the **caret** package
- Five-fold cross-validation
- Automatic hyperparameter tuning (`tuneLength = 5`)
- Algorithms:
  - Support Vector Machine (linear kernel)
  - Support Vector Machine (radial kernel)
  - Random Forest

**Evaluation metrics**
- Accuracy
- Cohen’s Kappa
- No Information Rate (NIR)
- McNemar’s test p-value
- Model p-value

**Input**
- Training datasets
- Model configuration parameters

**Output**
- Trained models
- Cross-validated performance metrics

---

### 6. `split_methylation_by_loci.R`

Splits filtered probes into biologically meaningful genomic loci: Promoter, TSS200, TSS1500, Body, 1stExon, 5’UTR, 3’UTR y ExonBnd.

**Input**
- Filtered methylation matrix
- Probe genomic annotations

**Output**
- Separate methylation matrices per genomic locus

---

### 7. `prepare_for_ml_loci()`

Prepares ML datasets independently for each genomic locus.

**Input**
- Locus-specific methylation matrices
- Clinical metadata

**Output**
- Training and testing datasets per locus and contrast

---
### 8. `train_models_loci()`

Trains and evaluates binary classification models for each genomic locus and clinical contrast.

**Function**
- Fits independent models per locus to assess the predictive contribution of specific genomic regions.

**Implementation details**
- Implemented using the **caret** package
- Five-fold cross-validation
- Automatic hyperparameter tuning (`tuneLength = 5`)
- Same training strategy and evaluation framework as `train_models_all()`

**Algorithms**
- Support Vector Machine (linear kernel)
- Support Vector Machine (radial kernel)
- Random Forest

**Evaluation metrics**
- Accuracy
- Cohen’s Kappa
- No Information Rate (NIR)
- McNemar’s test p-value
- Model p-value

**Input**
- Locus-specific training datasets

**Output**
- Trained models
- Cross-validated performance metrics per locus and clinical contrast


## Methodology (As Used in This Study)

In the original application of this pipeline:
- Models were trained both at the **gene level** and **locus level**
- Locus-based modeling was preferred due to the non-uniform distribution of DNA methylation across genes
- Multiple clinical contrasts were evaluated independently
- Model selection was based on cross-validated performance metrics

This section describes **one possible use case**, not a constraint of the pipeline.

---

## Requirements

- R (caret, biomaRt, tidyverse)
- bedtools
- Python (for data splitting notebook)

---

## Key Findings

- Global models did not yield statistically significant results after multiple testing correction.  
- Locus-specific analyses revealed significant signals in promoter and TSS200 regions of the Adult Neurodegenerative Disorders panel, particularly for Parkinson’s disease vs Control (PD vs CTRL) using radial SVM.  
- Random Forest models including covariates detected significant signals only in negative control panels, suggesting that demographic imbalances may drive associations rather than disease-specific methylation differences.  
- These findings highlight the importance of incorporating biological prior knowledge and functional genomic stratification when modeling high-dimensional DNA methylation data.


