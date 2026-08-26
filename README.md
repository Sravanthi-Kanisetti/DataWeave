# 📊 Sales, Campaign & Inventory Analytics — Power BI Project

An end-to-end Power BI project that transforms a messy, real-world-style raw dataset (23 loosely related Excel sheets — duplicated year-over-year order tables, generic placeholder columns, no defined keys) into a clean **star schema** data model, ready for reporting and analysis.

This repo documents the full journey: **raw data → data modeling → relationships → analysis-ready model**.

---

## 🗂️ Before vs. After

| | Raw Source (`dataset.xlsx`) | Final Data Model (`project_datamodel.pbix`) |
|---|---|---|
| Structure | 23 flat/loosely-linked sheets, duplicated `ORDERS_2025` / `ORDERS_2026`, unclear keys, placeholder `Column1/Column2` fields | Clean **star schema**: fact tables + conformed dimensions |
| Relationships | Implicit / inconsistent (multiple `*` to `*` links, ambiguous joins) | Explicit 1-to-many relationships, single source of truth per entity |
| Usability | Not analysis-ready | Plug-and-play for DAX measures, visuals & reports |

📸 See [`docs/images/`](docs/images) for the entity-relationship diagrams of both states.
 
---

## 🧱 Final Star Schema

**Fact tables**
- `fact_sales` — line-level sales transactions (cost, discount, quantity)
- `fact_order_process` — order lifecycle (order date, invoice date, delivery date)
- `fact_inventory` — monthly stock/unit snapshots by product
- `fact_promotion_coverage` — bridge table linking campaigns to promoted products
- `fact_campaign_spend` — daily marketing spend, clicks, impressions
- `fact_sales_targets` — revenue targets by period

**Dimension tables**
- `dim_customer`, `dim_product`, `dim_campaign`, `dim_date`, `dim_geo`, `dim_order_flags`, `security` (row-level security table)

This design follows Kimball-style dimensional modeling: fact tables at the grain of a transaction/event, connected to shared conformed dimensions — avoiding the many-to-many/circular joins present in the raw source.

---

## 📁 Repository Structure

```
├── README.md                      # You are here
├── data/
│   └── dataset.xlsx                # Raw source data (23 sheets, pre-modeling)
├── powerbi/
│   └── project_datamodel.pbix      # Final Power BI file (model + relationships + report)
├── docs/
│   └── images/
│       ├── raw_source_erd.png      # ERD of the raw/unmodeled source tables
│       └── final_star_schema.png   # ERD of the final star schema model
└── screenshots/                    # Report/dashboard visuals (optional, add your own)
```

> Rename the two screenshot files to `raw_source_erd.png` and `final_star_schema.png` when you add them to `docs/images/` (see setup steps below).

---

## 🛠️ Tech Stack

- **Power BI Desktop** — data modeling, relationships, DAX, reporting
- **Power Query (M)** — data cleaning & transformation
- **Excel** — raw source data

---

## 🚀 Getting Started

1. Clone the repo:
   ```bash
   git clone https://github.com/Sravanthi-Kanisetti/DataWeave.git
   cd DataWeave
   ```
2. Open `powerbi/data_modelling_project.pbix` in **Power BI Desktop**.
3. If prompted, update the data source path to point to `data/dataset.xlsx` in your local clone.
4. Explore the **Model view** to see the star schema, or the **Report view** for the dashboards.

---

## 🔑 Key Data Modeling Decisions

- Consolidated duplicated yearly order tables (`ORDERS_2025`, `ORDERS_2026`) into a single `fact_order_process` fact table using `dim_date` for time intelligence, instead of one table per year.
- Split customer identity (`CUST_MASTER`, `customer_contacts`, `Address`, `cities`) into a single conformed `dim_customer` / `dim_geo`.
- Replaced ambiguous many-to-many links between orders/invoices/shipments with clearly scoped fact tables (`fact_sales`, `fact_order_process`) sharing common dimensions.
- Added a `fact_promotion_coverage` bridge table to resolve the many-to-many relationship between campaigns and products.
- Included a `security` table for row-level security (RLS) by region/user.
