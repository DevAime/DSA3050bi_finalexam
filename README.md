
```markdown
# 🌍 Global Fuel Price Analytics — Power BI Dashboard

An advanced Business Intelligence solution built in Microsoft Power BI,
analyzing global weekly fuel prices from 2020 to 2026 across multiple
countries, regions, and fuel types.

---

## 🔗 Quick Links

| Resource | Link |
|---|---|
| 📊 Live Dashboard | [View on Power BI](https://app.powerbi.com/view?r=eyJrIjoiZDQ1NjdmOGMtZjU1MS00MDNlLTgzZWYtMzg3ZGFlM2M3YTkyIiwidCI6IjE2ZDgzZWU2LTI1NGEtNDY5ZC1hNmNjLTU0ZTJjYTIzMTNlNyIsImMiOjh9) |
| 📁 Dataset Source | [Kaggle — Global Fuel Prices 2020–2026](https://www.kaggle.com/datasets/belbino/global-fuel-prices-20202026) |
| 📄 Full PDF Report | [`/report/Global_Fuel_Price_Analytics_Report.pdf`](./report/Global_Fuel_Price_Analytics_Report.pdf) |
| 💾 Power BI File | [`/pbix/Global_Fuel_Price_Analytics.pbix`](./pbix/Global_Fuel_Price_Analytics.pbix) |

---

## 📌 Project Overview

### Business Problem

Fuel pricing is one of the most consequential policy areas globally.
This project answers the following questions:

- How have global fuel prices evolved from 2020 to 2026?
- Which countries have the highest and lowest prices, and why?
- How closely do pump prices track Brent crude oil benchmarks?
- What role do taxation and subsidies play in price differences?
- Which countries show the most significant year-over-year changes?

### Domain

**Energy Economics and Global Fuel Policy Analytics**

### Tool

**Microsoft Power BI Desktop**

---

## 📂 Repository Structure

```
global-fuel-price-analytics/
│
├── data/
│   └── global_fuel_prices.csv          # Raw dataset from Kaggle
│
├── pbix/
│   └── Global_Fuel_Price_Analytics.pbix  # Power BI project file
│
├── report/
│   └── Global_Fuel_Price_Analytics_Report.pdf  # Full PDF submission
│
├── screenshots/
│   ├── 01_raw_data.png
│   ├── 02_power_query_raw.png
│   ├── 03_data_types.png
│   ├── 04_column_quality.png
│   ├── 05_unpivot_before_after.png
│   ├── 06_applied_steps.png
│   ├── 07_queries_panel.png
│   ├── 08_model_view.png
│   ├── 09_calculated_column_price_category.png
│   ├── 10_calculated_column_brent_ratio.png
│   ├── 11_measure_fuel_contribution.png
│   ├── 12_measure_yoy.png
│   ├── 13_measure_price_premium.png
│   ├── 14_navigation_buttons.png
│   ├── 15_page1_executive_summary.png
│   ├── 16_page2_country_deep_dive.png
│   ├── 17_page2_map_crossfilter.png
│   ├── 18_page3_time_intelligence.png
│   ├── 19_page3_ranking_table.png
│   ├── 20_page4_drillthrough_country.png
│   ├── 21_drillthrough_menu.png
│   └── 22_bookmarks_panel.png
│
└── README.md
```

---

## 📊 Dataset Description

| Field | Type | Description |
|---|---|---|
| `date` | Date | Weekly observation date (every 7 days) |
| `country` | Text | Country name |
| `region` | Text | Geographic region grouping |
| `income_level` | Text | World Bank income classification |
| `subsidy_level` | Text | Government subsidy classification |
| `petrol_usd_liter` | Decimal | Petrol pump price — USD per litre |
| `diesel_usd_liter` | Decimal | Diesel pump price — USD per litre |
| `lpg_usd_liter` | Decimal | LPG pump price — USD per litre |
| `brent_crude_usd` | Decimal | Brent crude benchmark — USD per barrel |
| `tax_percentage` | Decimal | Percentage of pump price from taxation |

**Dataset size:** 9,000+ rows in the main fact table after transformation
**Date range:** January 2020 — April 2026
**Frequency:** Weekly (every 7 days per country)
**Structure:** All weekly dates for Country A, then all weekly dates
for Country B, and so on

---

## ⚙️ Data Cleaning and Transformation (Power Query)

All transformations were performed in Power Query Editor
before loading into the model.

| Step | Transformation | Why |
|---|---|---|
| 1 | Import CSV | Load raw data into Power BI |
| 2 | Fix data types | Date → Date, prices → Decimal, text → Text |
| 3 | Rename fuel columns | petrol_usd_liter → Petrol, etc. |
| 4 | Handle missing values | Numeric nulls → column average, categorical → "Unknown" |
| 5 | Trim and clean text | Remove leading/trailing spaces from country, region, etc. |
| 6 | Unpivot fuel columns | Transform Petrol/Diesel/LPG into fuel_type + price_usd_liter |
| 7 | Standardize fuel names | Ensure Petrol/Diesel/LPG are consistently capitalized |
| 8 | Extract date parts | Add Year, Month, Month Name, Quarter columns |
| 9 | Create conditional column | Price Category: > 1.5 USD → "High", else → "Low" |
| 10 | Build dimension tables | DimDate, DimCountry, DimFuelType via reference queries |

### Key Transformation — Unpivot

The most important transformation converts three wide price columns
into a long analytical format:

**Before:**
| date | country | Petrol | Diesel | LPG |
|---|---|---|---|---|
| 06/01/2020 | United States | 1.465 | 1.289 | 1.093 |

**After:**
| date | country | fuel_type | price_usd_liter |
|---|---|---|---|
| 06/01/2020 | United States | Petrol | 1.465 |
| 06/01/2020 | United States | Diesel | 1.289 |
| 06/01/2020 | United States | LPG | 1.093 |

This enables fuel_type to be used as a proper dimension for
filtering, slicing, and grouping across the entire model.

---

## 🗂️ Data Model

A **star schema** was implemented with one central fact table
and three surrounding dimension tables.

```
DimDate ──────────────┐
                      │
DimCountry ───────── FactFuelPrices
                      │
DimFuelType ──────────┘
```

### Tables

| Table | Type | Description |
|---|---|---|
| `FactFuelPrices` | Fact | One row per country × date × fuel type. Contains price_usd_liter, brent_crude_usd, tax_percentage |
| `DimDate` | Dimension | Complete calendar table. Marked as Date Table for time intelligence |
| `DimCountry` | Dimension | One row per country with region, income_level, subsidy_level |
| `DimFuelType` | Dimension | Three rows — Petrol, Diesel, LPG |

### Relationships

| From | To | Column | Cardinality | Direction |
|---|---|---|---|---|
| FactFuelPrices | DimDate | date | Many-to-One | Single |
| FactFuelPrices | DimCountry | country | Many-to-One | Single |
| FactFuelPrices | DimFuelType | fuel_type | Many-to-One | Single |

---

## 📐 DAX Measures and Calculated Columns

All measures are stored in a dedicated `_Measures` table.

### Calculated Columns

| Column | Table | Formula Summary |
|---|---|---|
| `Price Category` | FactFuelPrices | IF price > 1.5 → "High" ELSE "Low" |
| `Price_to_Brent_Ratio` | FactFuelPrices | price_usd_liter ÷ brent_crude_usd |

### Measures

| Measure | Purpose |
|---|---|
| `Avg Fuel Price` | Average price in current filter context |
| `Total Price Index` | SUMX of all prices — used for contribution analysis |
| `Countries Tracked` | DISTINCTCOUNT of countries — KPI card |
| `Fuel Type % Contribution` | Each fuel type's share of total price index |
| `YTD Avg Price` | Year-to-date average using DATESYTD |
| `Prev Year Avg Price` | Same period last year using SAMEPERIODLASTYEAR |
| `YoY Price Change %` | % change vs previous year |
| `Country Price Rank` | RANKX — cheapest to most expensive |
| `Avg Brent Crude` | Average Brent crude benchmark |
| `Price Premium over Brent` | Pump price minus crude cost per litre |

---

## 📈 Dashboard Pages

### Page 1 — Executive Summary
Global KPI cards, price trend line chart by fuel type,
regional comparison bar chart, fuel type donut chart.
Slicers: Year, Fuel Type, Income Level.

![Executive Summary](./screenshots/15_page1_executive_summary.png)

### Page 2 — Country & Fuel Deep Dive
Filled map (anchor visual), top/bottom country bar chart,
country × fuel type matrix, scatter chart (Brent vs pump price
sized by tax %), donut chart.
Slicers: Region, Subsidy Level.

![Country Deep Dive](./screenshots/16_page2_country_deep_dive.png)

### Page 3 — Time Intelligence & Performance Monitoring
Line + column combo chart (pump price vs Brent over time),
YTD area chart by year, country ranking table with YoY values,
Price Premium over Brent KPI card.
Slicers: Year, Region.

![Time Intelligence](./screenshots/18_page3_time_intelligence.png)

### Page 4 — Country Profile (Drillthrough)
Accessed by right-clicking any country on Pages 1–3.
Shows full price history, annual benchmark comparison,
year-by-year summary table, and all country-level KPIs.
Includes a Back button to return to Page 2.

![Country Profile](./screenshots/20_page4_drillthrough_country.png)

---

## 💡 Key Insights

1. **The 2022 energy crisis is clearly visible** — prices peaked at
   nearly USD 3.00/litre globally, driven by the Russia-Ukraine
   supply disruption, before stabilising post-2023.

2. **Extreme geographic price disparity** — Hong Kong exceeds
   USD 4.00/litre while Venezuela, Libya, Iran, and Algeria sit
   below USD 0.50, a difference explained entirely by tax and
   subsidy policy rather than crude oil costs.

3. **High-income countries decouple pump prices from crude** —
   taxation creates a price floor, meaning consumers in these
   markets benefit less from crude price drops than they suffer
   from crude price rises.

4. **Most countries show stable YoY changes around 2%** — Venezuela
   (-0.26%) and Iran (1.27%) are outliers, both reflecting
   government price controls rather than market dynamics.

5. **A persistent price premium of ~USD 2.00/litre above crude
   equivalent** has been maintained throughout the period,
   representing taxes, refining, and distribution costs that
   did not significantly reduce even during the 2022 crisis.

---

## ✅ Recommendations

1. **Gradual subsidy reform** — High-subsidy markets should phase
   out blanket subsidies and replace them with targeted cash
   transfers to protect low-income households without perpetuating
   fiscal risk.

2. **Price relief trigger mechanisms** — High-tax markets should
   design automatic tax suspension rules when prices exceed a
   defined threshold for two or more consecutive quarters.

3. **LPG promotion in price-sensitive markets** — LPG is
   consistently the cheapest fuel type across all regions.
   Governments in lower-income countries should prioritise LPG
   infrastructure investment as a strategic buffer.

---

## 🚀 How to Open the Project

1. Download and install
   [Power BI Desktop](https://powerbi.microsoft.com/desktop)
   (free)
2. Clone or download this repository
3. Open `pbix/Global_Fuel_Price_Analytics.pbix` in Power BI Desktop
4. If prompted about data source paths, update the CSV path to match
   your local `data/` folder location
5. Click **Refresh** to reload the data

---

## 📋 Requirements

- Microsoft Power BI Desktop (latest version recommended)
- No additional software or licenses required to open the .pbix file
- Internet connection required to view the live published dashboard

---

## 👤 Author

**[Your Name]**
[Your Institution / Course Name]
[Your Email — optional]
[Your LinkedIn — optional]
```
