# SQL Data Warehouse Project

A data warehouse I built in SQL Server to practice data engineering end to end: loading raw CSV exports from two business systems, cleaning them, and modeling them into a star schema that is ready for analysis.

> **Credit:** This project follows the [SQL Data Warehouse tutorial](https://github.com/DataWithBaraa/sql-data-warehouse-project) by **Baraa Khatib Salkini** ([Data With Baraa](https://www.youtube.com/@datawithbaraa)). The project design, datasets and original scripts come from his course, which is released under the MIT License. I rebuilt it step by step as a learning exercise. Thank you, Baraa, for sharing such a complete and well-explained project!

---

## What this project does

The company in this scenario keeps its sales data in two separate systems:

- **CRM**: customers, products and sales transactions
- **ERP**: extra customer details, customer locations and product categories

The warehouse brings both sources together into one consistent model, so that questions about customers, products and sales can be answered with simple SQL queries.

## Architecture

The warehouse uses the **Medallion architecture**, with one schema per layer:

| Layer | Schema | What happens here | Object type |
|:--|:--|:--|:--|
| Bronze | `bronze` | Raw data loaded as-is from the CSV files | Tables |
| Silver | `silver` | Data cleaned, standardized and de-duplicated | Tables |
| Gold | `gold` | Business-ready star schema for reporting | Views |

**Flow:** CSV files → `bronze.load_bronze` → `silver.load_silver` → gold views

### The gold layer (star schema)

- `gold.dim_customers`: one row per customer, combining CRM and ERP details
- `gold.dim_products`: current products with their categories
- `gold.fact_sales`: sales transactions linked to both dimensions

Every column is described in the [data catalog](docs/data_catalog.md).

## Repository structure

```
sql_data_warehouse-project/
├── datasets/
│   ├── source_crm/           cust_info, prd_info, sales_details (CSV)
│   └── source_erp/           CUST_AZ12, LOC_A101, PX_CAT_G1V2 (CSV)
├── docs/
│   └── data_catalog.md       description of the gold layer tables and columns
├── scripts/
│   ├── init_database.sql     creates the DataWarehouse database and the three schemas
│   ├── bronze/               bronze tables and the bronze load procedure
│   ├── silver/               silver tables and the cleaning/load procedure
│   └── gold/                 gold views (star schema)
└── tests/                    data quality checks for the silver and gold layers
```

## How to run it

1. Install **SQL Server** (the free Express or Developer edition works) and **SQL Server Management Studio (SSMS)**.
2. Run `scripts/init_database.sql`.
   ⚠️ It drops and recreates the `DataWarehouse` database if it already exists.
3. Create the bronze tables with `scripts/bronze/DDL_script.sql` and the load procedure with `scripts/bronze/proc_load.sql`. Update the CSV file paths in the procedure to match where you saved the datasets, then run:
   ```sql
   EXEC bronze.load_bronze;
   ```
4. Create the silver tables with `scripts/silver/ddl_silver.sql` and the procedure with `scripts/silver/proc_load_silver`, then run:
   ```sql
   EXEC silver.load_silver;
   ```
5. Create the gold views with `scripts/gold/ddl_gold.sql`.
6. Check the results with the scripts in `tests/`.

## What I learned

- Designing a layered (bronze / silver / gold) warehouse architecture
- Writing ETL logic in T-SQL with stored procedures, including bulk loading and error handling
- Cleaning real-world data problems: duplicates, missing values, inconsistent codes and invalid dates
- Data modeling with fact and dimension tables (star schema)
- Writing data quality checks and documenting a data model

## Tools

SQL Server · T-SQL · SQL Server Management Studio · Git & GitHub

## License

This project is shared under the [MIT License](LICENSE). The original tutorial material is © 2024 Baraa Khatib Salkini, also under the MIT License.
