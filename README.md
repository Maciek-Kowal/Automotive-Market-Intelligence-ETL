# EV Market Intelligence 

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/pandas-%23150458.svg?style=for-the-badge&logo=pandas&logoColor=white)
![Google BigQuery](https://img.shields.io/badge/Google_BigQuery-669DF6?style=for-the-badge&logo=googlecloud&logoColor=white)
![Excel](https://img.shields.io/badge/Microsoft_Excel-217346?style=for-the-badge&logo=microsoftexcel&logoColor=white)
![VBA](https://img.shields.io/badge/VBA-F2C811?style=for-the-badge)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white)

## Project Overview

This project is a fully automated, end-to-end data engineering pipeline designed to monitor the electrified vehicle market (EVs and HEVs) in Poland. The solution autonomously extracts data from a major automotive marketplace, transforms it, loads it into a cloud data warehouse, and delivers ready-to-use business intelligence reports via an automated spreadsheet.

The architecture was built with reliability, network optimization, and maintainability in mind, strictly adhering to industry standards for data ingestion and ETL workflows.

## Architecture & Data Pipeline

The system consists of three core modules executing in a strictly defined daily sequence:

1. **Extraction & Transformation (Python)**
   - **Orchestration:** The entry point is a Python script triggered nightly by GitHub Actions.
   - **Asynchronous Scraping:** Implemented a highly concurrent network module using `asyncio` and `aiohttp`. This non-blocking architecture allows for rapid, concurrent HTTP requests, significantly reducing overall data extraction time compared to synchronous approaches.
   - **Dynamic Pagination:** A custom pagination mechanism traverses result pages to bypass hardcoded display limits and ensure complete data volume extraction.
   - **Data Wrangling:** Extracted HTML metadata is rigorously cleaned and transformed using `pandas` and `numpy`. This includes vectorized type casting, string mapping, handling missing values, and validating business logic (e.g., battery ownership status).

2. **Data Warehouse (Google BigQuery)**
   - **Cloud Integration:** Cleaned and validated datasets are pushed to Google Cloud via the native BigQuery Python API.
   - **Dimensional Modeling:** Data is structured following a Star Schema approach, separated into a Fact Table (time-variant metrics like price, mileage, scrape date) and a Dimension Table (static attributes like make, model, engine power).
   - **Idempotency & Data Integrity:** Implemented UPSERT logic utilizing SQL `MERGE` statements. This guarantees idempotency, preventing duplicate records and maintaining data integrity during daily batch loads.

3. **Reporting Layer (Excel VBA)**
   - **Business Interface:** The final deliverable is an interactive analytical file that eliminates the need for business users to log into external cloud consoles.
   - **Automated Data Retrieval:** Upon opening the file, a hidden VBA script establishes a direct ODBC connection to Google BigQuery.
   - **Dynamic Rendering:** The script cleans the workspace, fetches the latest materialized view, and automatically rebuilds Pivot Tables, appending a visible timestamp of the most recent update.

## Business Value & Insights

The tool provides immediate visibility into the secondary automotive market. Key metrics delivered by the automated report include:
- Precise inventory volumes broken down by specific makes and models.
- Average market pricing based on the current day's active listings.
- A foundational dataset for identifying macroeconomic supply-side trends within the low- and zero-emission vehicle sector.

![Business Report Excel](images/Raport.png)
*Fig 1. Preview of the automatically generated business report in Excel.*

## Repository Structure

- `.github/workflows/` - CRON job definitions for CI/CD and pipeline automation.
- `config/` - Configuration files defining input datasets (target vehicle manifest).
- `data/` - Environment dumps and sample datasets.
- `excel/` - End-user reporting files.
- `src/` - Core application source code (business logic, async network requests, database operations).
- `vba_scripts/` - Reporting layer source code, extracted as plain text for version control.
- `main.py` - Application entry point triggering the asynchronous pipeline.
- `requirements.txt` - Python environment dependencies.

![BigQuery Structure](images/Schema.png)
*Fig 2. Structure of implemented Fact and Dimension tables in Google BigQuery.*

## Future Scope

Once a statistically significant volume of historical data is accumulated, the project will be expanded with the following features:
- **Listing Lifecycle Analysis:** Tracking the time-to-sale for specific models and monitoring price depreciation trends over time.
- **Geospatial Dimensions:** Expanding the dataset with geographic coordinates to map vehicle supply across different regions of the country.
- **BI Migration:** Replacing the Excel reporting layer with a fully interactive dashboard built in a modern Business Intelligence tool (e.g., Power BI or Tableau).