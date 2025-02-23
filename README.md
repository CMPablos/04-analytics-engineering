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
CREATE OR REPLACE TABLE `PROJECT_ID.ny_taxi_data.fhv_data`  AS SELECT * FROM `PROJECT_ID.ny_taxi_data.fhv_data_ext` 
```
