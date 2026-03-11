# E-commerce-Data-Pipeline
S3 &amp; Local to Postgres via Prefect

Project Overview
This project simulates a real-world data engineering ETL (Extract, Transform, Load) pipeline. It merges daily sales data stored in cloud object storage (mocked as an S3 JSON file) with on-premise inventory data (a local CSV file). The pipeline is orchestrated using Prefect, transformed using Pandas, and loaded into a PostgreSQL data warehouse for analytics.

Business Objectives
The final output of this pipeline feeds two critical "Gold" layer analytics tables to drive business decisions:

Revenue by Category (revenue_by_category): Calculates the total revenue, average sale value, and total units sold per product category over the last year.

Restock Alerts (restock_alerts): Identifies products where the current stock level has fallen below the designated reorder threshold, calculating exactly how many units are needed to replenish.

Prerequisites
Before running this project, ensure you have the following installed on your machine:

Python 3.8+

PostgreSQL (running locally on port 5432)

Git

Step-by-Step Setup Instructions
1. Environment Setup
First, clone the repository and set up a Python virtual environment to keep dependencies isolated.

Bash
# Clone the repo
git clone <your-github-repo-url>
cd <your-repo-folder>

# Create and activate a virtual environment
python -m venv venv

# Activate (Mac/Linux)
source venv/bin/activate
# Activate (Windows)
venv\Scripts\activate

# Install required packages
pip install pandas sqlalchemy psycopg2-binary prefect
2. Database Setup
Create a local Postgres database named ecommerce_dw. Then, run the SQL schema script to generate the staging and analytics tables.

Bash
# Log into your local postgres instance
psql -U postgres

# Inside the psql prompt, create the database:
CREATE DATABASE ecommerce_dw;
\q

# Run the setup script to create tables
psql -U postgres -d ecommerce_dw -f setup_schema.sql
(Note: Update the database credentials in prefect_pipeline.py if your local Postgres user/password is different).

3. Generate Mock Data
Run the mock data generator script. This will create daily_sales_s3.json (1,000 sales records) and inventory_on_prem.csv (50 products) in your project directory.

Bash
python generate_mock_data.py
4. Run the Pipeline
With the data generated and the database ready, you can execute the Prefect orchestrated pipeline.

Bash
python prefect_pipeline.py
To view the Prefect UI and track your flow runs visually, open a new terminal, activate your virtual environment, and run:

Bash
prefect server start
Then navigate to http://127.0.0.1:4200 in your browser.

Testing the Pipeline (End-to-End)
To verify that the pipeline dynamically updates the database, follow these steps to introduce new data:

Trigger a Low Stock Alert:

Open inventory_on_prem.csv.

Find the first row, note the product_id, and change its stock_level to 1.

Add a Massive Sale:

Open daily_sales_s3.json.

Add a new JSON object at the end of the array with a price of 9999.99 and quantity of 10 for a specific product ID.

Re-run the ETL Flow:

Run python prefect_pipeline.py again. Prefect will drop the old staging data and insert the fresh data.

Verify in Postgres:

Log into your database: psql -U postgres -d ecommerce_dw

Check the new revenue: SELECT * FROM revenue_by_category ORDER BY total_revenue DESC;

Verify the restock alert caught the change: SELECT * FROM restock_alerts;
