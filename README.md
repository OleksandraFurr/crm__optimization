
# CRM Data Analysis and Optimization

This project analyzes CRM exports for calls, contacts, marketing spend, and deals. It combines data cleaning and descriptive statistics with sales, marketing, geographic, and unit economics analysis. The work is organized as three Jupyter notebooks, with a presentation summarizing the project.

> **Data availability:** The repository currently contains the notebooks and presentation, but not the source spreadsheets or intermediate pickle files that the notebooks load. You will need authorized copies of the input data to reproduce the analyses. Do not commit customer or other sensitive CRM data to a public repository.

## Project contents

| File                                | Purpose                                                                                                                                                                                                                                    |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `D_1_Proj_FIN.ipynb`              | Loads and cleans the four source datasets, reviews missing values and duplicates, standardizes selected fields, calculates descriptive statistics, and saves cleaned datasets as pickle files.                                             |
| `D_2_Proj_FIN.ipynb`              | Uses the cleaned data to explore deal and call trends, deal duration, campaign and source performance, sales-owner performance, product and payment patterns, and geography. Includes interactive Plotly charts and a geographic analysis. |
| `D_3_Proj_FIN.ipynb`              | Explores unit economics and sample-size / uncertainty calculations, and exports deal data in spreadsheet and CSV formats.                                                                                                                  |
| `Final project presentation.pptx` | Presentation accompanying the analysis.                                                                                                                                                                                                    |

## Data flow

The notebooks are intended to be run in sequence:

```text
Source Excel workbooks
        │
        ▼
D_1_Proj_FIN.ipynb ──► Cl_Calls.pkl, Cl_Cont.pkl, Cl_Spend.pkl, Cl_Deals.pkl
                                      │
                                      ├──► D_2_Proj_FIN.ipynb ──► deals_geo.csv
                                      └──► D_3_Proj_FIN.ipynb ──► unit.xlsx, deals_t.csv, deals_tb.csv
```

The first notebook reads these workbooks from its current working directory:

- `Calls (Done).xlsx`
- `Contacts (Done).xlsx`
- `Spend (Done).xlsx`
- A deals workbook loaded by the notebook as `Deals (Done).xlsx`

It writes cleaned datasets to `Cl_Calls.pkl`, `Cl_Cont.pkl`, `Cl_Spend.pkl`, and `Cl_Deals.pkl`. The second notebook expects all four pickle files. The third expects the first three and `2_Deals.pkl` for deals, rather than `Cl_Deals.pkl`; make sure the expected file is available or update that notebook's loading cell to use the appropriate cleaned deals file.

The data is loaded using fixed filenames and relative paths. In Jupyter, the current working directory determines where files are read and where generated outputs are written. Put the input files in the notebook's working directory, or update the paths in the data-loading cells. Confirm that your files have the columns used by the notebooks; these names reflect the supplied analysis and may need adapting for a different CRM export.

## Requirements

Use Python 3 with Jupyter Notebook or JupyterLab. The notebooks use:

- `numpy`
- `pandas`
- `plotly`
- `openpyxl` for Excel workbook access
- `geopandas`, `shapely`, and `folium` for geographic analysis in `D_2_Proj_FIN.ipynb`

Install the packages in a virtual environment (or an equivalent managed environment):

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
python -m pip install --upgrade pip
python -m pip install jupyter numpy pandas plotly openpyxl geopandas shapely folium
```

There is no dependency lockfile in the repository. Geographic packages may have platform-specific installation requirements; use the installation guidance for your Python distribution if `geopandas` cannot be installed with pip.

## Running the analysis

1. Obtain the four source Excel exports and place them beside the notebooks (or edit the paths in the loading cells). Keep the source data in a private location.
2. Start Jupyter from the directory containing the notebooks:

   ```bash
   jupyter lab
   ```
3. Open `D_1_Proj_FIN.ipynb` and run its cells from top to bottom. Review the cleaning steps and resulting summaries, then confirm the four pickle files were created.
4. Open `D_2_Proj_FIN.ipynb` and run its cells from top to bottom to create the interactive analyses and `deals_geo.csv`.
5. Before running `D_3_Proj_FIN.ipynb`, confirm that `2_Deals.pkl` exists in the working directory. If the cleaned deals from step 3 are the intended input, change the notebook's deals loading cell from `2_Deals.pkl` to `Cl_Deals.pkl`. Run the cells to create `unit.xlsx`, `deals_t.csv`, and `deals_tb.csv`.

Charts are rendered interactively in the notebook. Exported files are written to the current working directory. Re-running the notebooks may overwrite outputs with the same names.

## Notebook scope

### 1. Data preparation and descriptive statistics

`D_1_Proj_FIN.ipynb` processes calls, contacts, spend, and deals. The workflow includes initial inspection, duplicate handling, removal of empty or irrelevant fields, treatment and review of missing values, data type conversion, and descriptive summaries of numeric and categorical fields. The notebook includes analysis notes about missing call durations and calls without a linked Contact ID; these represent interpretation of this dataset and should be reassessed for other exports.

### 2. Sales, marketing, and geographic analysis

`D_2_Proj_FIN.ipynb` explores:

- Deal creation and closure over time, alongside call activity
- Deal closing and elapsed time distributions, including potential duration outliers
- Lead volumes and conversion by campaign and marketing source
- Deal owner workload, conversion, and revenue measures
- Product, learning format, payment method, and payment type distributions
- Deal distribution by city and German-language level

Several visualizations are interactive. Observations written in the notebook are hypotheses or descriptions of the analyzed extract, not universal conclusions. Recheck them when the underlying data or date range changes.

### 3. Unit economics and estimation

`D_3_Proj_FIN.ipynb` contains calculations related to unit economics and sample-size estimation, then exports a unit economics workbook and two CSV versions of deal data (comma-separated and semicolon-separated). Review the assumptions and definitions in the notebook before using the outputs for decisions.

## Generated files

The notebooks may generate the following files in their current working directory:

| Output                                                                | Created by             | Description                                                              |
| --------------------------------------------------------------------- | ---------------------- | ------------------------------------------------------------------------ |
| `Cl_Calls.pkl`, `Cl_Cont.pkl`, `Cl_Spend.pkl`, `Cl_Deals.pkl` | `D_1_Proj_FIN.ipynb` | Cleaned intermediate datasets serialized with pandas pickle.             |
| `deals_geo.csv`                                                     | `D_2_Proj_FIN.ipynb` | Deal data prepared for the geographic analysis.                          |
| `unit.xlsx`                                                         | `D_3_Proj_FIN.ipynb` | Spreadsheet output from the unit economics calculations.                 |
| `deals_t.csv`, `deals_tb.csv`                                     | `D_3_Proj_FIN.ipynb` | Deal data exported as comma-separated and semicolon-separated CSV files. |

Pickle files are Python-specific intermediate artifacts; only load pickle files from sources you trust. These outputs may contain personal or commercially sensitive information and should be handled with the same care as the source CRM exports.

## Reproducibility notes and limitations

- The repository does not include a dependency file or the source datasets, so results cannot be reproduced from a fresh checkout alone.
- Notebooks rely on fixed filenames, column names, and sequential execution. Running cells out of order or using a different working directory can cause missing-file or missing-variable errors.
- The third notebook refers to `2_Deals.pkl`, while the first notebook writes `Cl_Deals.pkl`. Resolve this input difference before running it.
- The notebooks contain exploratory cleaning decisions and manual interpretation of unusual values. Validate these decisions against the business definitions and source system before using the results operationally.
- Geographic plots may depend on geospatial libraries or external map resources available in the runtime environment.
- No automated tests or command-line pipeline are included; the notebooks are the executable analysis artifacts.

## Privacy

CRM exports can contain personally identifiable information, contact history, and commercial data. Use only data you are authorized to process, keep it in an appropriately secured environment, and avoid sharing notebooks with embedded data, outputs, or identifiers. Review notebook outputs before publishing or distributing them.
