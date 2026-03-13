# E-commerce Data Pipeline (S3 & Local to Postgres via Prefect)

## Project Overview
This project simulates a real-world data engineering ETL (Extract, Transform, Load) pipeline. It merges daily sales data stored in cloud object storage (mocked as an S3 JSON file) with on-premise inventory data (a local CSV file). The pipeline is orchestrated using **Prefect**, transformed using **Pandas**, and loaded into a **PostgreSQL** data warehouse for analytics.

## Business Objectives
The final output of this pipeline feeds two critical "Gold" layer analytics tables to drive business decisions:
1. **Revenue by Category (`revenue_by_category`)**: Calculates the total revenue, average sale value, and total units sold per product category over the last year.
2. **Restock Alerts (`restock_alerts`)**: Identifies products where the current stock level has fallen below the designated reorder threshold, calculating exactly how many units are needed to replenish.

---

## Prerequisites
Before running this project, ensure you have the following installed on your machine:
* **Python 3.8+**
* **PostgreSQL** (running locally on port `5432`)
* **Git**
* **Jupyter Notebook**

---

## Quick Start Setup & Execution

Because this project is built for easy demonstration, the entire ETL pipeline, database schema creation, and mock data generation are contained within a single Jupyter Notebook.

### 1. Database Setup
Ensure your local PostgreSQL server is running. Create a blank database named `ecommerce_db` using your preferred SQL client (like pgAdmin or DBeaver) or via your terminal:
```bash
psql -U postgres -c "CREATE DATABASE ecommerce_db;"
```

### 2. Environment Setup
Open your terminal, clone this repository, and install the necessary Python dependencies:
```bash
git clone https://github.com/Rizwan-SK/E-commerce-Data-Pipeline.git
cd E-commerce-Data-Pipeline
pip install -r requirements.txt
```

### 3. Run the Pipeline
Once the requirements are installed, launch the Jupyter interface from your terminal:
```bash
jupyter notebook
```
1. In the browser window that opens, click on `Data Engineering Project.ipynb`.
2. Locate the code cells containing the `DB_CONN` variable and ensure the password matches your local Postgres password.
3. Click **"Run All"** at the top of the notebook.

**The notebook will automatically:**
* Generate the mock S3 JSON and local CSV files.
* Build the Postgres staging and analytics tables.
* Run the Prefect data orchestrator to extract, transform, and load the data.
* Display the final business reports directly on the screen.

---

## How to Test the Automation (Idempotency)
To see the pipeline adapt to new data without duplicating existing records:
1. Open the generated `inventory_on_prem.csv` file on your computer.
2. Find any product and change its `stock_level` to `1` (forcing it below the threshold) and save the file.
3. Go back to the Jupyter Notebook and re-run *only* the **Prefect Pipeline cell** (the second to last cell).
4. Re-run the **Display cell** (the final cell). 

You will instantly see your newly edited product appear in the Restock Alerts table!
