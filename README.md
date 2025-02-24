# Data Engineering Zoomcamp 2025
## Homework Solutions for Module 3: Data Warehousing

The For Hire Vehicle dataset has incomplete data for some of the columns, to avoid errors when reading the .csv files, set the option allow_jagged_rows=TRUE to ignore missing column data issues.

```SQL
CREATE OR REPLACE EXTERNAL TABLE `PROJECT_ID.ny_taxi_data.fhv_data_ext` (
    dispatching_base_num STRING,
    pickup_datetime TIMESTAMP,
    dropoff_datetime TIMESTAMP,
    PUlocationID FLOAT64,
    DOlocationID FLOAT64,
    SR_Flag FLOAT64,
    Affiliated_base_number STRING
)
OPTIONS (
  format = 'csv',
  uris = ['gs://analytics_taxi_dataset/fhv*csv.gz'],
  skip_leading_rows=1,
  allow_jagged_rows=TRUE;
);
```

A native table was created from the external table:

```SQL
CREATE OR REPLACE TABLE `PROJECT_ID.ny_taxi_data.fhv_data`  
AS SELECT * FROM `PROJECT_ID.ny_taxi_data.fhv_data_ext` 
```

### Q1. Understanding dbt model resolution

The dataset myproject is loaded as the DBT_BIGQUERY_PROJECT because it is 
explicitly set when dbt runs. However, the schema is determined by the 
DBT_BIGQUERY_SOURCE_DATASET environment variable. Since DBT_BIGQUERY_SOURCE_DATASET 
was not set and DBT_BIGQUERY_DATASET=my_nyc_tripdata was exported instead, 
dbt defaults to using raw_nyc_tripdata as the schema.

**Answer**: select * from myproject.raw_nyc_tripdata.ext_green_taxi



### Q2. dbt Variables & Dynamic Models

Set the env_var to be the default value of the command line value, and '30' to be the
default value of the env_var.
This way, if the command line value is not set, the env_value is loaded instead. If nothing
is explicitly declared, 30 days are going to be processed by default.

**Answer**: Update the WHERE clause to pickup_datetime >= CURRENT_DATE - 
                INTERVAL '{{ var("days_back", env_var("DAYS_BACK", "30")) }}' DAY

### Q3. dbt Data Lineage and Execution

dbt run --select models/staging/+ would only materialize themselves, since dim_taxi_trips 
also depends on dim_zone_lookup, as a result fct_taxi_monthly_zone_revenue would also 
not be build.

**Answer**: dbt run --select models/staging/+

### Q3. dbt Macros and Jinja

**Answer**: 

    - Setting a value for DBT_BIGQUERY_TARGET_DATASET env var is mandatory, or it'll fail to compile.
    - When using core, it materializes in the dataset defined in DBT_BIGQUERY_TARGET_DATASET
    - When using stg, it materializes in the dataset defined in DBT_BIGQUERY_STAGING_DATASET, or defaults to DBT_BIGQUERY_TARGET_DATASET
    - When using staging, it materializes in the dataset defined in DBT_BIGQUERY_STAGING_DATASET, or defaults to DBT_BIGQUERY_TARGET_DATASET
