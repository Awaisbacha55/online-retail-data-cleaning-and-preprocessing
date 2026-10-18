# Online Retail Data Cleaning & Preprocessing

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)
[![Pandas](https://img.shields.io/badge/Pandas-2.0%2B-150458.svg)](https://pandas.pydata.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Internship](https://img.shields.io/badge/Internship-CodSoft-orange.svg)](https://www.codsoft.in/)

> **CodSoft Data Analytics Internship — Task 1**  
> A structured, transparent data quality analysis and cleaning pipeline applied to the UCI Online Retail II dataset using Python and Pandas.

---

## 📌 Project Overview

Real-world transactional datasets often contain significant data quality defects—such as duplicated transactions, invalid data types, missing customer keys, and non-standardized string fields. Before performing exploratory data analysis (EDA), customer segmentation (RFM), or sales modeling, raw records must be cleaned and validated.

This repository contains the complete implementation of **Task 1: Data Cleaning & Preprocessing** for the **CodSoft Data Analytics Internship**. The analysis was developed and executed in Google Colab using Python and Pandas, focusing on:
- Diagnosing missing values and exact duplicate transactions.
- Preserving critical business records (such as order cancellations and guest checkouts) rather than arbitrarily dropping or imputing them.
- Standardizing column formats and casting fields to memory-efficient, mathematically sound data types.
- Validating the final cleaned dataset before export.

---

## 📊 Dataset Information

This project analyzes the official **Online Retail II** dataset hosted by the **UCI Machine Learning Repository**.

| Attribute | Details |
| :--- | :--- |
| **Dataset Name** | Online Retail II |
| **Repository** | UCI Machine Learning Repository (Dataset ID: 502) |
| **Official Source** | [https://archive.ics.uci.edu/dataset/502/online+retail+ii](https://archive.ics.uci.edu/dataset/502/online+retail+ii) |
| **File Analyzed** | `online_retail_II.xlsx` |
| **Sheet Analyzed** | `Year 2009-2010` (representing transactional activity from 01/12/2009 to 09/12/2010) |
| **Original Dimensions** | 525,461 rows × 8 columns |

> **Note on Dataset Distinction:**  
> The standard *Online Retail* dataset (UCI ID 352) covers one year (2010–2011 with ~541k rows), whereas *Online Retail II* (UCI ID 502) is a two-sheet workbook covering 2009–2011 across two distinct years. This analysis examines the **`Year 2009-2010`** sheet.

### Dataset Schema

| Column Name | Raw Type | Cleaned Type | Description |
| :--- | :--- | :--- | :--- |
| `Invoice` | `object` | `object` | 6-digit transaction identifier. Prefixed with `'C'` if cancelled. |
| `StockCode` | `object` | `string[python]` | 5-digit unique product/item identifier. |
| `Description` | `object` | `string[python]` | Product item name / description. |
| `Quantity` | `int64` | `int64` | Number of units purchased or returned per transaction. |
| `InvoiceDate` | `datetime64[ns]` | `datetime64[ns]` | Date and timestamp of transaction generation. |
| `Price` | `float64` | `float64` | Unit price of product in sterling (£). |
| `Customer ID` | `float64` | `Int64` | 5-digit unique customer identifier (nullable integer). |
| `Country` | `object` | `string[python]` | Country name of the customer residence. |

---

## 🔄 Data Cleaning Workflow

The cleaning pipeline follows an audit-first methodology to ensure transparency and reproducibility:

```mermaid
flowchart TD
    A[Raw Dataset: online_retail_II.xlsx<br/>525,461 records] --> B[Data Integrity & Structure Audit]
    B --> C[Missing-Value Analysis<br/>Customer ID: 20.54% | Description: 0.56%]
    B --> D[Duplicate Detection<br/>Found 6,865 exact duplicate records]
    B --> E[Anomaly & Inconsistency Check<br/>12,326 negative quantities | 3,690 zero/negative prices]
    
    C --> F[Cleaning & Preprocessing]
    D --> F
    E --> F
    
    F --> G[1. Drop exact duplicates: -6,865 rows]
    F --> H[2. Text standardization: str.strip on text columns]
    F --> I[3. Type casting: Customer ID to Int64 nullable]
    F --> J[4. Preserve guest transactions & valid returns]
    
    G --> K[Post-Cleaning Validation]
    H --> K
    I --> K
    J --> K
    
    K --> L[Cleaned Dataset: 518,596 records<br/>Exported to Online_Retail_Cleaned.csv]
```

---

## 🧹 Cleaning Methodology & Decisions

1. **Exact Duplicate Removal:**
   - 6,865 duplicate rows were identified where all fields (Invoice, StockCode, Description, Quantity, InvoiceDate, Price, Customer ID, Country) matched identically.
   - These records were removed using `df.drop_duplicates()`, reducing the row count from 525,461 to 518,596.

2. **String Standardization:**
   - Leading and trailing whitespace characters were trimmed from text columns (`Description`, `Country`, `StockCode`) using `.astype("string").str.strip()`.

3. **Data Type Correction:**
   - `Customer ID` was converted from `float64` (which forces IDs into decimal representations like `13085.0`) to Pandas' nullable integer format `Int64` (`13085` and `<NA>`), maintaining numerical integrity while allowing missing values.
   - `InvoiceDate` was formatted into standard `datetime64[ns]`.
   - `Quantity` and `Price` were verified via `pd.to_numeric(..., errors="coerce")`.

4. **Handling Missing Values (Transparent Rationale):**
   - **Customer ID (107,927 missing initially; 107,833 remaining post-deduplication):**  
     These rows represent guest checkouts or unregistered transactions. Dropping them would discard over 20% of sales activity, distorting total revenue. Imputing artificial IDs would introduce false customer entities into cohort or RFM models. They are preserved with `<NA>`.
   - **Description (2,928 missing):**  
     Preserved without arbitrary text substitution to avoid corrupting inventory auditing.

5. **Returns and Cancellations Preservation:**
   - Invoices beginning with `'C'` accompanied by negative quantities (12,326 non-positive quantity records) represent legitimate customer returns and transaction cancellations.
   - Rather than dropping these events as erroneous entries, they are maintained so that downstream analysts can either filter them for net sales analysis (`Quantity > 0`) or utilize them for return rate modeling.

---

## 📈 Before-and-After Cleaning Summary

| Quality Metric | Before Cleaning | After Cleaning | Analytical Impact |
| :--- | :--- | :--- | :--- |
| **Total Rows** | 525,461 | 518,596 | Removed 6,865 redundant duplicated entries |
| **Total Columns** | 8 | 8 | All relevant business fields preserved |
| **Duplicate Records** | 6,865 | 0 | 100% resolution of identical rows |
| **Missing Customer IDs** | 107,927 (20.54%) | 107,833 (20.79%) | Preserved for sales reporting; -94 duplicates removed |
| **Missing Descriptions** | 2,928 (0.56%) | 2,928 (0.56%) | Intact for administrative reconciliation |
| **Customer ID Format** | `float64` (`13085.0`) | `Int64` (`13085`) | Cleaner key representation without fake decimals |
| **Exported Dataset** | Raw Excel (43.51 MB) | Cleaned CSV (43.16 MB) | Structured and ready for downstream analytics |

---

## 🖼️ Included Visualizations

The Jupyter Notebook (`Task1_Data_Cleaning.ipynb`) includes generated visualizations:
1. **Missing Values by Column (Horizontal Bar Chart):** Displays the distribution and counts of missing values across all dataset fields prior to cleaning.
2. **Record Count Before and After Cleaning (Bar Chart):** Quantifies the row count reduction resulting from duplicate removal (525,461 original rows vs. 518,596 cleaned rows).
3. **Distribution of Transaction Quantities (Histogram):** Illustrates the distribution of order quantities across sales transactions.

---

## 📁 Repository Structure

```text
online-retail-data-cleaning-and-preprocessing/
│
├── README.md                      # Project documentation, methodology, and results
├── Task1_Data_Cleaning.ipynb      # Main Jupyter Notebook with executed cells and outputs
├── requirements.txt               # Required Python packages for reproducibility
├── LICENSE                        # MIT License
├── .gitignore                     # Git ignore rules for data, cache, and virtual environments
│
└── data/
    ├── README.md                  # Dataset source links and local download instructions
    └── sample/
        └── README.md              # Sample data placeholder note
```

---

## 🚀 How to View and Run the Project

### Option 1: View Directly on GitHub
GitHub natively renders Jupyter Notebooks. You can click on [`Task1_Data_Cleaning.ipynb`](Task1_Data_Cleaning.ipynb) directly in this repository to view all code cells, Markdown explanations, and output charts.

### Option 2: Open in Google Colab
1. Download or clone this repository.
2. Visit [Google Colab](https://colab.research.google.com/).
3. Upload `Task1_Data_Cleaning.ipynb`.
4. Upload `online_retail_II.xlsx` into the Colab runtime session.
5. Run the notebook cells sequentially.

### Option 3: Run Locally

1. **Clone the repository:**
   ```bash
   git clone https://github.com/Awaisbacha55/online-retail-data-cleaning-and-preprocessing.git
   cd online-retail-data-cleaning-and-preprocessing
   ```

2. **Create and activate a virtual environment (optional but recommended):**
   ```bash
   python -m venv .venv
   # Windows:
   .venv\Scripts\activate
   # macOS / Linux:
   source .venv/bin/activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Download the raw dataset:**
   - Download `online_retail_II.xlsx` from the [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/502/online+retail+ii).
   - Place `online_retail_II.xlsx` in the project root directory.

5. **Launch Jupyter Notebook:**
   ```bash
   jupyter notebook Task1_Data_Cleaning.ipynb
   ```

---

## 🛠️ Technologies Used

- **Language:** Python 3
- **Data Manipulation:** Pandas, NumPy
- **File Parsing:** OpenPyXL
- **Data Visualization:** Matplotlib, Seaborn
- **Environment:** Google Colab, Jupyter Notebook
- **Version Control:** Git, GitHub

---

## 👨‍💻 Author

**Awais**  
- GitHub: [@Awaisbacha55](https://github.com/Awaisbacha55)  
- Role: Data Analytics Intern — CodSoft

---

## 🤝 Acknowledgments

- **CodSoft** for providing the internship program and practical data analytics curriculum.
- **UCI Machine Learning Repository** for hosting and maintaining the Online Retail II dataset.
