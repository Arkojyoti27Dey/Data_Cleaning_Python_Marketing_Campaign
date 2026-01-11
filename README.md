
# 🧹 Marketing Campaign Data Cleaning 

A robust data cleaning pipeline built with Python and Pandas designed to transform messy, heterogeneous marketing campaign data into a clean, analysis-ready dataset.

This project demonstrates advanced data wrangling techniques, including logic-based imputation for invalid timelines, mixed-format date parsing, and statistical outlier handling.

## 📂 Repository Structure

| File | Description |
| --- | --- |
| `data_cleaning_with_python.ipynb` | **Core Logic.** Jupyter Notebook containing the full ETL (Extract, Transform, Load) pipeline. |
| `marketing_campaign_data_messy.csv` | **Input.** The raw, dirty dataset containing mixed date formats, invalid values, and duplicate columns. |
| `final_cleaned_version.xlsx` | **Output.** The fully processed, clean dataset ready for SQL/Power BI ingestion. |
| `clicks_dilema.xlsx` | **Analysis.** A side-investigation file focusing on discrepancies in the 'Clicks' data. |

## 🚀 Key Features & Techniques

### 1. Robust Column Normalization

Raw column names were inconsistent (e.g., `Campaign_ID`, `Clicks (%)`, `Spend`). The pipeline standardizes them using Regex:

* Strips whitespace.
* Converts to lowercase.
* Replaces non-alphanumeric characters with underscores.

### 2. Mixed-Format Date Parsing

The source data contained dates in multiple formats (`YYYY-MM-DD`, `DD/MM/YYYY`, `MM/DD/YYYY`).

* **Strategy:** Instead of a single pass that results in `NaT` (Not a Time) errors, the pipeline uses `pd.to_datetime(..., format='mixed')` to correctly infer formats row-by-row.
* **Auditability:** Original columns are preserved alongside new parsed columns for validation before being dropped.

### 3. Logic-Based Timeline Repair

A critical data quality issue was identified where `Start_Date` was sometimes later than `End_Date` (negative duration).

* **Statistical Imputation:** Instead of dropping these valuable rows, the pipeline calculates the **Interquartile Range (IQR)** of valid campaign durations to find a robust average (approx. 15 days).
* **Repair:** Invalid timelines are repaired by resetting the `End_Date` to `Start_Date + 15 Days`.
* **Flagging:** A boolean column `timeline_repaired` is added to track which rows were scientifically imputed.

### 4. Data Type & Value Cleaning

* **Currency Cleaning:** The `spend` column contained symbols (`$`) and string types. These were stripped and converted to floats.
* **Duplicate Resolution:** Handled duplicate columns (e.g., multiple `clicks` columns) that appeared in the raw CSV export.

