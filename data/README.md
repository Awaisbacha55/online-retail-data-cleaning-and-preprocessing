# Dataset Information

This project uses the **Online Retail II** dataset from the UCI Machine Learning Repository.

## Dataset Details
- **Name:** Online Retail II
- **Repository:** UCI Machine Learning Repository
- **Dataset ID:** 502
- **Official URL:** [https://archive.ics.uci.edu/dataset/502/online+retail+ii](https://archive.ics.uci.edu/dataset/502/online+retail+ii)
- **Direct Download:** The archive contains `online_retail_II.xlsx` (~43.5 MB)
- **Worksheet Analyzed:** `Year 2009-2010` (525,461 records across 8 columns)

## Why Raw and Processed Datasets Are Not Committed to GitHub
In accordance with standard open-source and data analytics repository practices:
1. GitHub repositories should avoid committing large data binaries (the raw Excel file is ~43.5 MB and the cleaned CSV is ~43.2 MB).
2. Committing raw datasets directly can cause repository bloat and potential bandwidth/quota issues.
3. Keeping data external ensures users can always download the official, uncompressed source directly from UCI.

## How to Obtain and Place the Data Locally
1. Download `online_retail_II.xlsx` from the [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/502/online+retail+ii).
2. Place the file in the project root directory or upload it into your Google Colab runtime:
   ```text
   online-retail-data-cleaning-and-preprocessing/
   ├── online_retail_II.xlsx  <-- (place here locally; ignored by git)
   ├── Task1_Data_Cleaning.ipynb
   └── ...
   ```
3. Open `Task1_Data_Cleaning.ipynb` to view or replicate the data cleaning workflow.
