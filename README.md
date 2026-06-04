# E-Commerce Product Demand & Sales Analysis

[![Python](https://img.shields.io/badge/python-v3.8+-blue.svg)](https://www.python.org/downloads/)
[![Databricks](https://img.shields.io/badge/Databricks-Latest-orange.svg)](https://databricks.com/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

A comprehensive e-commerce demand analytics pipeline built on Databricks that enriches order data, calculates demand metrics, identifies product performance patterns, and generates forward-looking demand forecasts.

## 📋 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Notebooks](#notebooks)
- [Data Flow](#data-flow)
- [Getting Started](#getting-started)
- [Prerequisites](#prerequisites)
- [Usage](#usage)
- [Key Outputs](#key-outputs)
- [Future Improvements](#future-improvements)
- [License](#license)

## 📊 Overview

This project builds an end-to-end demand analytics pipeline that:

- Enriches order-level sales records with product attributes and time dimensions
- Builds daily and weekly demand metrics
- Identifies top sellers, slow movers, and category-level revenue trends
- Generates 7-day and 30-day moving-average demand signals
- Creates simple 30-day product demand forecasts

## 🏗️ Architecture

The pipeline follows a **medallion architecture** (Silver → Gold) staged transformation pattern:

1. **Silver Layer** - Enrichment stage that prepares analytics-ready order data
2. **Gold Layer - Metrics** - Aggregates daily and weekly demand measures
3. **Gold Layer - Performance** - Summarizes product and category performance
4. **Gold Layer - Forecasting** - Produces moving-average signals and future demand projections

### Data Transformation Flow

```
Input Tables (Silver)
├── databricks_cat.silver.orders_silver
└── databricks_cat.silver.products_silver
    │
    ▼
Silver Enrichment
└── databricks_cat.silver.orders_demand_silver
    │
    ├──▶ Gold Metrics
    │   ├── databricks_cat.gold.DailyDemand
    │   └── databricks_cat.gold.WeeklyDemand
    │
    └──▶ Gold Performance
        ├── databricks_cat.gold.ProductPerformance
        └── databricks_cat.gold.CategoryRevenue
    │
    └──▶ Gold Forecasting
        ├── databricks_cat.gold.DemandForecast_MA
        └── databricks_cat.gold.DemandForecast_30D
```

## 📓 Notebooks

### 1. Silver_DemandEnrichment
Enriches raw order and product data with additional attributes.

**Inputs:**
- `databricks_cat.silver.orders_silver`
- `databricks_cat.silver.products_silver`

**Processing:**
- Adds return flags and time attributes
- Enriches with unit price, category, and brand information

**Outputs:**
- `databricks_cat.silver.orders_demand_silver`

### 2. Gold_DemandMetrics
Aggregates enriched order data into daily and weekly demand metrics.

**Inputs:**
- `databricks_cat.silver.orders_demand_silver`

**Processing:**
- Computes daily demand aggregations
- Computes weekly demand aggregations

**Outputs:**
- `databricks_cat.gold.DailyDemand`
- `databricks_cat.gold.WeeklyDemand`

### 3. Gold_ProductPerformance
Calculates product-level KPIs and category-level performance metrics.

**Inputs:**
- `databricks_cat.gold.DailyDemand`

**Processing:**
- Computes product KPIs
- Identifies top sellers and slow movers
- Calculates category revenue

**Outputs:**
- `databricks_cat.gold.ProductPerformance`
- `databricks_cat.gold.CategoryRevenue`

### 4. Demand_Forecasting
Generates moving-average demand signals and forecast trends.

**Inputs:**
- `databricks_cat.gold.DailyDemand`

**Processing:**
- Computes 7-day and 30-day moving averages
- Applies demand trend labeling

**Outputs:**
- `databricks_cat.gold.DemandForecast_MA`
- `databricks_cat.gold.DemandForecast_30D`

## 📈 Data Flow Summary

| Layer | Inputs | Outputs |
|-------|--------|---------|
| **Silver** | `orders_silver`, `products_silver` | `orders_demand_silver` |
| **Gold Metrics** | `orders_demand_silver` | `DailyDemand`, `WeeklyDemand` |
| **Gold Performance** | `DailyDemand` | `ProductPerformance`, `CategoryRevenue` |
| **Gold Forecasting** | `DailyDemand` | `DemandForecast_MA`, `DemandForecast_30D` |

## 🚀 Getting Started

### Prerequisites

Before running the project, ensure you have:

- ✅ Access to a Databricks workspace
- ✅ Access to the `databricks_cat` catalog with Silver and Gold schemas
- ✅ Source tables populated:
  - `databricks_cat.silver.orders_silver`
  - `databricks_cat.silver.products_silver`
- ✅ Write permissions for target Gold and Silver tables
- ✅ Access to configured Azure Data Lake Storage paths
- ✅ Python and Delta Lake support enabled in your Databricks workspace

### Installation

1. Clone this repository:
```bash
git clone https://github.com/suressh25/E-Commerce-Product-Demand-Sales-Analysis.git
cd E-Commerce-Product-Demand-Sales-Analysis
```

2. Import the notebooks into your Databricks workspace

3. Update catalog and schema references if using different names than `databricks_cat`

## 📖 Usage

### Running the Pipeline

Execute the notebooks in the following order:

1. **Silver_DemandEnrichment** - Prepares and enriches raw data
2. **Gold_DemandMetrics** - Aggregates demand metrics
3. **Gold_ProductPerformance** - Calculates performance KPIs
4. **Demand_Forecasting** - Generates demand forecasts

> **Important:** Run in sequence to ensure each notebook can read tables created by the previous stage.

### Expected Outputs

After running the complete pipeline, you'll have:

- ✅ Enriched order demand table for downstream analytics
- ✅ Daily and weekly product demand metrics
- ✅ Product-level KPI and ranking outputs
- ✅ Category revenue summaries
- ✅ Moving-average demand trend signals
- ✅ 30-day simple forecast with lower and upper bounds

## 🎯 Key Outputs

| Output | Description |
|--------|-------------|
| `orders_demand_silver` | Enriched order data with product and time attributes |
| `DailyDemand` | Daily product-level demand aggregations |
| `WeeklyDemand` | Weekly product-level demand aggregations |
| `ProductPerformance` | Product KPIs, rankings, and performance metrics |
| `CategoryRevenue` | Category-level revenue summaries |
| `DemandForecast_MA` | 7-day and 30-day moving-average signals |
| `DemandForecast_30D` | 30-day demand forecast with confidence bounds |

## 🔮 Future Improvements

- [ ] Replace simple moving-average forecasts with advanced statistical or ML models (ARIMA, Prophet, etc.)
- [ ] Parameterize catalog, schema, and storage paths for easier multi-environment deployment
- [ ] Add comprehensive data quality checks and validation rules between layers
- [ ] Orchestrate notebooks with Databricks Workflows/Jobs scheduler
- [ ] Implement incremental processing to reduce full overwrite operations
- [ ] Create comprehensive dashboard documentation with metric definitions
- [ ] Add unit tests and integration tests for data quality
- [ ] Implement error handling and logging across all notebooks

## 📊 Dashboard

The project includes an **E-Commerce Product Demand & Sales Analytics** dashboard for business-facing analysis and visualization of demand metrics and forecast results.

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 📞 Support

For questions or issues, please open a GitHub Issue in this repository.

---

**Built with ❤️ using Databricks and Delta Lake**
