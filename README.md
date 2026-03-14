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

> [!IMPORTANT]  
> If you encounter any terminal errors during this setup (such as `command not found` or `invalid syntax`), please scroll directly to the **Troubleshooting & Edge Cases** section at the bottom of this document for immediate fixes.

Because this project is built for easy demonstration, the entire ETL pipeline, database schema creation, and mock data generation are contained within a single Jupyter Notebook.

### 1. Database Setup

Ensure your local PostgreSQL server is running. Create a blank database named `ecommerce_db` using your preferred SQL client (like pgAdmin or DBeaver) or via your terminal:

```bash
psql -U postgres -c "CREATE DATABASE ecommerce_db;"
```

*Note: For Mac users, [Postgres.app](https://postgresapp.com/) is the recommended, one-click local server setup.*

### 2. Environment Setup

Open your terminal, clone this repository, and isolate the dependencies using a virtual environment:

```bash
git clone https://github.com/Rizwan-SK/E-commerce-Data-Pipeline.git
cd E-commerce-Data-Pipeline

# Create a virtual environment
python3 -m venv venv

# Activate the environment (Mac/Linux)
source venv/bin/activate
# Activate the environment (Windows)
# venv\Scripts\activate

# Install the necessary dependencies (including Jupyter)
pip install --upgrade pip
pip install -r requirements.txt
```

### 3. Run the Pipeline

Once the virtual environment is active and requirements are installed, launch the Jupyter interface from your terminal:

```bash
jupyter notebook
```

1. In the browser window that opens, click on `Data Engineering Project.ipynb`.
2. Locate the configuration cell and ensure the `DATABASE_URL` (or `DB_CONN`) matches your local Postgres credentials. *(If you have no password set locally, use `postgresql://postgres@localhost:5432/ecommerce_db`)*.
3. Click **Kernel → Restart Kernel and Run All** from the top menu to execute the pipeline.

**The notebook will automatically:**

* Generate the mock S3 JSON and local CSV files.
* Build the Postgres staging and analytics tables.
* Run the Prefect data orchestrator to extract, transform, and load the data.
* Display the final business reports directly on the screen.


### 4. How to Test the Automation (Idempotency)

To see the pipeline adapt to new data without duplicating existing records:

1. Open the generated `inventory_on_prem.csv` file on your computer.
2. Find any product and change its `stock_level` to `1` (forcing it below the threshold) and save the file.
3. Go back to the Jupyter Notebook and re-run only the **Prefect Pipeline cell** (the second to last cell).
4. Re-run the **Display cell** (the final cell).

You will instantly see your newly edited product appear in the Restock Alerts table!

---

### Clean Teardown

Because this project uses a virtual environment, it leaves zero footprint on your global system. To completely remove the project, dependencies, and data:

1. Delete the `E-commerce-Data-Pipeline` folder (this automatically uninstalls all downloaded Python packages).
2. Drop the local database by running your SQL client or this terminal command:

```bash
psql -U postgres -c "DROP DATABASE ecommerce_db;"
```

---

### Troubleshooting & Edge Cases

If you hit any errors during setup, check these common fixes:

* **`SyntaxError: invalid syntax`**: You are likely typing terminal commands inside the Python interpreter (your prompt shows `>>>`). Type `exit()` and hit Enter to return to your normal terminal, then run your setup commands.
* **`zsh: command not found: psql`**: Postgres tools are not in your system PATH. For Mac, install Postgres.app. For Windows, add the Postgres `/bin` folder to your System Environment Variables.
* **`zsh: command not found: pip`**: Try using `pip3` instead of `pip` or ensure your virtual environment is activated.
* **`zsh: command not found: jupyter`**: Jupyter is not installed in your active environment. Ensure `notebook` is listed in your `requirements.txt` file and rerun the `pip install -r requirements.txt` command.
* **`FATAL: database "ecommerce_db" does not exist`**: The pipeline cannot find the target database. Ensure you successfully completed Step 1.
* **`ConnectError: [Errno 61]`**: This is a Prefect background server port conflict. If you run the pipeline a second time in the same session, Prefect's local logger can hang. Click **Kernel → Restart Kernel and Run All**.
* **`UnicodeDecodeError: 'utf-8'`**: Your mock data files may have corrupted text encoding from being opened in standard text editors. Delete the `inventory_on_prem.csv` and `daily_sales_s3.json` files and rerun the notebook to generate fresh ones.
