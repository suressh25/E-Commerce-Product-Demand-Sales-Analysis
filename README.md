# E-Commerce Product Demand & Sales Analysis

This project builds an e-commerce demand analytics pipeline in Databricks to enrich order data, calculate demand metrics, identify product performance patterns, and generate simple forward-looking demand forecasts. The workflow uses Silver and Gold layer tables to transform operational sales data into analytics-ready datasets for reporting and decision support.

## Project overview

The project focuses on:

* enriching order-level sales records with product attributes and time dimensions
* building daily and weekly demand metrics
* identifying top sellers, slow movers, and category-level revenue trends
* generating 7-day and 30-day moving-average demand signals
* creating a simple 30-day product demand forecast

## Architecture and flow

The pipeline follows a staged transformation pattern:

1. Silver enrichment prepares analytics-ready order data.
2. Gold metrics aggregate daily and weekly demand measures.
3. Gold performance models summarize product and category performance.
4. Forecasting produces moving-average signals and future demand projections.

Data flow:

* `databricks_cat.silver.orders_silver` + `databricks_cat.silver.products_silver`
* `databricks_cat.silver.orders_demand_silver`
* `databricks_cat.gold.DailyDemand` + `databricks_cat.gold.WeeklyDemand`
* `databricks_cat.gold.ProductPerformance` + `databricks_cat.gold.CategoryRevenue`
* `databricks_cat.gold.DemandForecast_MA` + `databricks_cat.gold.DemandForecast_30D`

## Notebooks included

* [Silver_DemandEnrichment](#notebook-1328420598295174)
  * reads `databricks_cat.silver.orders_silver` and `databricks_cat.silver.products_silver`
  * adds return flags, time attributes, unit price, category, and brand
  * writes `databricks_cat.silver.orders_demand_silver`

* [Gold_DemandMetrics](#notebook-1328420598295172)
  * reads `databricks_cat.silver.orders_demand_silver`
  * computes daily demand and weekly demand aggregations
  * writes `databricks_cat.gold.DailyDemand` and `databricks_cat.gold.WeeklyDemand`

* [Gold_ProductPerformance](#notebook-1328420598295175)
  * reads `databricks_cat.gold.DailyDemand`
  * computes product KPIs, category revenue, top sellers, and slow movers
  * writes `databricks_cat.gold.ProductPerformance` and `databricks_cat.gold.CategoryRevenue`

* [Demand_Forecasting](#notebook-1328420598295171)
  * reads `databricks_cat.gold.DailyDemand`
  * computes 7-day and 30-day moving averages and demand trend labels
  * writes `databricks_cat.gold.DemandForecast_MA` and `databricks_cat.gold.DemandForecast_30D`

## Source and target tables

| Layer | Inputs | Outputs |
| --- | --- | --- |
| Silver | `databricks_cat.silver.orders_silver`, `databricks_cat.silver.products_silver` | `databricks_cat.silver.orders_demand_silver` |
| Gold metrics | `databricks_cat.silver.orders_demand_silver` | `databricks_cat.gold.DailyDemand`, `databricks_cat.gold.WeeklyDemand` |
| Gold performance | `databricks_cat.gold.DailyDemand` | `databricks_cat.gold.ProductPerformance`, `databricks_cat.gold.CategoryRevenue` |
| Forecasting | `databricks_cat.gold.DailyDemand` | `databricks_cat.gold.DemandForecast_MA`, `databricks_cat.gold.DemandForecast_30D` |

## Key outputs

* enriched order demand table for downstream analytics
* daily and weekly product demand metrics
* product-level KPI and ranking outputs
* category revenue summaries
* moving-average demand trend signals
* 30-day simple forecast with lower and upper bounds

## Prerequisites

Before running the project, make sure you have:

* access to the `databricks_cat` catalog and required Silver and Gold schemas
* source tables populated in `databricks_cat.silver.orders_silver` and `databricks_cat.silver.products_silver`
* write permissions for the target Gold and Silver tables
* access to the configured Azure Data Lake Storage paths referenced by the notebooks
* a Databricks workspace with Python and Delta Lake support

## Run order

Run the notebooks in the following order:

1. [Silver_DemandEnrichment](#notebook-1328420598295174)
2. [Gold_DemandMetrics](#notebook-1328420598295172)
3. [Gold_ProductPerformance](#notebook-1328420598295175)
4. [Demand_Forecasting](#notebook-1328420598295171)

This sequence ensures each downstream notebook can read the tables created by the previous stage.

## Dashboard

The project includes the dashboard [E-Commerce Product Demand & Sales Analytics](#dashboard-01f158cd73101ced9a0723e432c43f42) for business-facing analysis and visualization of the resulting demand and sales outputs.

## Possible next improvements

* replace simple moving-average forecasts with a more advanced forecasting model
* parameterize catalog, schema, and storage paths for easier deployment
* add data quality checks and validation rules between layers
* orchestrate the notebooks with a scheduled Lakeflow Job
* add incremental processing to reduce full overwrite operations
* document dashboard metrics and business definitions in more detail
