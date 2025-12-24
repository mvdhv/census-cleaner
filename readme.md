# Census Population Data Cleaning Pipeline
*A cleaning script for noisy Census population and area data, decentralized by state, district, and sub-districts.*

---

## Overview

This repository documents an advanced, reproducible data cleaning and validation pipeline for Indian census population data at the state, district, and subdistrict (tehsil / taluka) levels. The raw census data, sourced from an Excel workbook, contains:

- Mixed datatypes
- Inconsistent naming conventions
- Hierarchical aggregation rows (e.g. TOTAL)
- Redundant and malformed administrative codes
- Internal arithmetic inconsistencies in summary statistics
- Structural ambiguity between inhabited and uninhabited settlements

The objective of this project is to transform that raw administrative dataset into a fully standardised, internally consistent, and analysis-ready CSV suitable for academic research, statistical modelling, and linkage with other administrative datasets. The core logic of the cleaning process is implemented in a single, exhaustively documented Jupyter notebook, with every step explicitly validated through sanity checks.

---

## Repository Structure

```text
.
├── Census_Codebook.pdf         # Codebook for the cleaned dataset (pdf)
├── census_clean.csv            # Fully cleaned and validated dataset (output)
├── census_cleaner.ipynb        # Primary data cleaning and validation notebook
├── census_codebook.tex         # Codebook frame for the cleaned dataset (LaTeX)
├── codebook.tex                # Codebook info for the cleaned dataset (LaTeX)
├── data_dictionary.ipynb       # Codebook generation notebook
├── README.md                   # Project documentation (this file)
├── statedist_censusinfo.xlsx   # Raw census Excel file (input)

```

### Data Inputs and Outputs

### Raw Input (`statedist_censusinfo.xlsx`)
- Multi-sheet Excel workbook
- Mixed numeric and string columns
- Includes administrative hierarchy rows and summary totals

### Final Output (`census_clean.csv`)
- One row per unique administrative unit
- Cleaned names and identifiers
- Standardised datatypes
- Verified internal population totals
- Safe for direct statistical analysis 

---

## Cleaning and Validation Pipeline
(Full sequence is available in `census_cleaner.ipynb`)

### I. Environment Setup and Data Ingestion
1. **Dependency Handling**
- Ensures the availability of openpyxl for Excel ingestion.
- Explicitly loads all required Python libraries (pandas, numpy, etc.).
2. **Importing the Raw Excel Datasheet**
- Reads the census Excel file into a Pandas DataFrame.
- Preserves all original rows and columns at this stage (no early drops).

### II. Cleaning Tasks – Part 1: Structural and Typological Consistency
1. **Datatype Normalisation**
- Inspects column datatypes for:
    - Numeric columns incorrectly stored as strings
    - Categorical identifiers mis-typed as numeric
- Converts datatypes where necessary to ensure:
    - Population variables are numeric
    - Administrative names remain string-typed

2. **String and Category Consistency Checks**
- *Trimming and Normalisation*
    - Removes leading/trailing whitespace in:
        - State names
        - District names
        -   Subdistrict names
    - Ensures uniform casing and formatting across all administrative labels.
- *Name Standardisation*
    - Standardises spelling variants and formatting inconsistencies.
    - Ensures the same administrative unit is not duplicated due to cosmetic differences.

### III. Cleaning Tasks – Part 2: Administrative Codes and Sanity Checks
1. **Identification Code Integrity**
- Validates state, district, and subdistrict codes for:
    - Missing values
    - Duplicates
    - Structural violations (e.g. incorrect code lengths)

2. **Population and Density Variable Validation**
- *Negative Value Detection*
    - Scans all population and summary statistic columns.
    - Flags and validates the absence of:
        - Negative population counts
        - Invalid density values
- *Standardising pop_per_sqkm*
    - Converts population density values to a consistent numeric datatype.
    - Handles coercion failures explicitly.

3. **Unique Identification and Row Integrity**
- Generates unique identifiers using combinations of:
    - State code
    - District code
    - Subdistrict code
- Confirms that each row corresponds to a single administrative unit.

### IV. Validation of Summary Rows and Aggregates
1. **“TOTAL” Category Validation**
- *Sanity Checking Summary Statistics*
    - Ensures rows labelled as TOTAL:
        - Correctly aggregate subordinate administrative units
        - Do not contain contradictory population values
- *Missingness Checks for Inhabited Settlements*
    - Verifies that population values are only missing where:
        - Settlements are explicitly marked as uninhabited
        - Ensures inhabited units always report population counts.

2. **Arithmetic Verification of Population Totals**
- *Internal Consistency Checks*
    - Recomputing Summary Statistics
    - Independently recomputes:
        - Subdistrict totals
        - District totals
    - Compares computed values against reported totals.
- *Validating the total_pop Column*
    - Confirms that total_pop equals the sum of:
        - Male population
        - Female population (where applicable)
    - Flags and resolves discrepancies.

### V. Final Dataset Construction
- After all checks pass, the cleaned DataFrame is finalised.
- The dataset is exported as census_clean.csv.

---

## Reproducibility and Transparency

All transformations are:
- Deterministic
- Explicitly documented
- Executable top-to-bottom

The notebook doubles as:
- Cleaning script
- Validation report
- Audit trail

---

## Intended Use Cases
This cleaned dataset is suitable for:
- Academic demographic analysis
- District-level political economy research
- Linking census data to:
    - Electoral data
    - Administrative boundaries
    - GIS shapefiles
    - Longitudinal subnational analysis

---

## Credits and Acknowledgments

### Developed by  
**Mr. Madhav Singh**  
Senior Research Associate  
Centre for Legislative Education and Research (CLER)  
FLAME University   

This data cleaning and validation pipeline was designed and implemented as part of a broader research workflow on Indian subnational political and demographic analysis.

---

## License and Rights

**All rights reserved.**  
This repository is intended for research and academic use. Redistribution or reuse requires appropriate attribution to the original author and institution.

---
