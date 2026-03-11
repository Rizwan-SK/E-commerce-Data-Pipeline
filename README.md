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

## Quick Start Setup & Execution

You can set up the environment, database, and run the entire pipeline using the following sequence of commands. 

*(Note: Windows users should use `venv\Scripts\activate` to activate the virtual environment).*

```bash
# 1. Clone the repository and navigate into it
git clone <your-github-repo-url>
cd <your-repo-folder>

# 2. Create and activate a Python virtual environment (Mac/Linux)
python -m venv venv
source venv/bin/activate

# 3. Install required dependencies from requirements.txt
pip install -r requirements.txt

# 4. Create the Postgres database and run the schema setup
# (Assuming your local postgres user is 'postgres')
psql -U postgres -c "CREATE DATABASE ecommerce_dw;"
psql -U postgres -d ecommerce_dw -f setup_schema.sql

# 5. Generate the mock data (creates the JSON and CSV files)
python generate_mock_data.py

# 6. Run the Prefect ETL pipeline
python prefect_pipeline.py

# 7. (Optional) Start the Prefect UI server to view flow logs
# Note: This runs continuously, so you can open it in [http://127.0.0.1:4200](http://127.0.0.1:4200)
prefect server start
