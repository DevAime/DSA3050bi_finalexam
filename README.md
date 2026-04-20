# Global Fuel Price Analytics — Power BI Dashboard

Access here is the deployed public dashboard:
https://app.powerbi.com/view?r=eyJrIjoiZDQ1NjdmOGMtZjU1MS00MDNlLTgzZWYtMzg3ZGFlM2M3YTkyIiwidCI6IjE2ZDgzZWU2LTI1NGEtNDY5ZC1hNmNjLTU0ZTJjYTIzMTNlNyIsImMiOjh9

An advanced Business Intelligence solution built in Microsoft Power BI,
analyzing global weekly fuel prices from 2020 to 2026 across multiple
countries, regions, and fuel types.

---

## Quick Links

| Resource | Link |
|---|---|
| Live Dashboard | [View on Power BI](https://app.powerbi.com/view?r=eyJrIjoiZDQ1NjdmOGMtZjU1MS00MDNlLTgzZWYtMzg3ZGFlM2M3YTkyIiwidCI6IjE2ZDgzZWU2LTI1NGEtNDY5ZC1hNmNjLTU0ZTJjYTIzMTNlNyIsImMiOjh9) |
| Dataset Source | [Kaggle — Global Fuel Prices 2020–2026](https://www.kaggle.com/datasets/belbino/global-fuel-prices-20202026) |
| Full PDF Report | [Global_Fuel_Price_Analytics_Report.pdf](./report/Global_Fuel_Price_Analytics_Report.pdf) |
| Power BI File | [Global_Fuel_Price_Analytics.pbix](./pbix/Global_Fuel_Price_Analytics.pbix) |

---

## Project Overview

### Business Problem

Fuel pricing is one of the most consequential policy areas globally.
This project answers the following core questions:

- How have global fuel prices evolved from 2020 to 2026?
- Which countries have the highest and lowest prices, and why?
- How closely do pump prices track Brent crude oil benchmarks?
- What role do taxation and subsidies play in price differences?
- Which countries show the most significant year-over-year changes?

**Domain:** Energy Economics and Global Fuel Policy Analytics

**Tool:** Microsoft Power BI Desktop

---


## Dataset Description

**Source:** [Kaggle — Global Fuel Prices 2020–2026](https://www.kaggle.com/datasets/belbino/global-fuel-prices-20202026)

**Size:** 9,000+ rows after transformation

**Date range:** January 2020 to April 2026

**Frequency:** Weekly observation per country (every 7 days)

**Structure:** All weekly dates for Country A, followed by all weekly
dates for Country B, and so on through every country in the dataset.

### Fields

| Field | Type | Description |
|---|---|---|
| date | Date | Weekly observation date |
| country | Text | Country name |
| region | Text | Geographic region grouping |
| income_level | Text | World Bank income classification |
| subsidy_level | Text | Government subsidy classification |
| petrol_usd_liter | Decimal | Petrol pump price in USD per litre |
| diesel_usd_liter | Decimal | Diesel pump price in USD per litre |
| lpg_usd_liter | Decimal | LPG pump price in USD per litre |
| brent_crude_usd | Decimal | Brent crude benchmark in USD per barrel |
| tax_percentage | Decimal | Percentage of pump price from taxation |

---

## Data Cleaning and Transformation

All transformations were performed in Power Query Editor
before loading data into the model.

| Step | Transformation | Purpose |
|---|---|---|
| 1 | Import CSV | Load raw data into Power BI |
| 2 | Fix data types | Date, Decimal, and Text types assigned correctly |
| 3 | Rename fuel columns | petrol_usd_liter renamed to Petrol, etc. |
| 4 | Handle missing values | Numeric nulls replaced with column average |
| 5 | Trim and clean text | Remove spaces from country, region, and category columns |
| 6 | Unpivot fuel columns | Petrol, Diesel, LPG converted to fuel_type and price_usd_liter |
| 7 | Standardize fuel names | Consistent capitalization across Petrol, Diesel, LPG |
| 8 | Extract date parts | Year, Month, Month Name, Quarter added from date column |
| 9 | Conditional column | Price Category — High if price above 1.5 USD, else Low |
| 10 | Build dimension tables | DimDate, DimCountry, DimFuelType created via reference queries |

### Core Transformation — Unpivot

The unpivot step is the most structurally important transformation.
It converts three wide price columns into a long analytical format
suitable for a star schema fact table.

Before:

| date | country | Petrol | Diesel | LPG |
|---|---|---|---|---|
| 06/01/2020 | United States | 1.465 | 1.289 | 1.093 |

After:

| date | country | fuel_type | price_usd_liter |
|---|---|---|---|
| 06/01/2020 | United States | Petrol | 1.465 |
| 06/01/2020 | United States | Diesel | 1.289 |
| 06/01/2020 | United States | LPG | 1.093 |

---

## Data Model

A star schema was implemented with one central fact table
and three surrounding dimension tables.

![Model View](./screenshots/08_model_view.png)

### Tables

| Table | Type | Description |
|---|---|---|
| FactFuelPrices | Fact | One row per country, date, and fuel type |
| DimDate | Dimension | Complete calendar table marked as Date Table |
| DimCountry | Dimension | One row per country with region and policy attributes |
| DimFuelType | Dimension | Three rows — Petrol, Diesel, LPG |

### Relationships

| From | To | Column | Cardinality | Direction |
|---|---|---|---|---|
| FactFuelPrices | DimDate | date | Many-to-One | Single |
| FactFuelPrices | DimCountry | country | Many-to-One | Single |
| FactFuelPrices | DimFuelType | fuel_type | Many-to-One | Single |

---

## DAX Measures and Calculated Columns

All measures are stored in a dedicated measures table named _Measures.

### Calculated Columns

| Column | Table | Logic |
|---|---|---|
| Price Category | FactFuelPrices | High if price above 1.5 USD per litre, else Low |
| Price_to_Brent_Ratio | FactFuelPrices | price_usd_liter divided by brent_crude_usd |

### Measures

| Measure | Purpose |
|---|---|
| Avg Fuel Price | Average price in current filter context |
| Total Price Index | Sum of all prices, used for contribution analysis |
| Countries Tracked | Distinct count of countries for KPI card |
| Fuel Type % Contribution | Each fuel type share of the total price index |
| YTD Avg Price | Year-to-date average using DATESYTD |
| Prev Year Avg Price | Same period last year using SAMEPERIODLASTYEAR |
| YoY Price Change % | Percentage change compared to previous year |
| Country Price Rank | RANKX ranking from cheapest to most expensive |
| Avg Brent Crude | Average Brent crude benchmark price |
| Price Premium over Brent | Pump price minus crude cost per litre |

---

## Dashboard Pages

### Page 1 — Executive Summary

Global KPI cards showing Avg Fuel Price (2.04 USD), Avg Brent Crude
(106.69 USD), and YoY Price Change (2.16%). Price trend line chart
by fuel type, regional bar chart, and fuel type donut chart.
Slicers: Year, Fuel Type, Income Level.

![Executive Summary](./screenshots/15_page1_executive_summary.png)

### Page 2 — Country and Fuel Deep Dive

Filled map as the anchor visual with country-level price shading.
Top and bottom country bar chart, country by fuel type matrix,
scatter chart plotting Brent crude against pump price sized by
tax percentage. Slicers: Region, Subsidy Level.

![Country Deep Dive](./screenshots/16_page2_country_deep_dive.png)

### Page 3 — Time Intelligence and Performance Monitoring

Line and column combo chart overlaying pump prices against Brent
crude over time. YTD area chart comparing years. Country ranking
table showing YoY changes with Venezuela and Iran as outliers.
Price Premium over Brent KPI card. Slicers: Year, Region.

![Time Intelligence](./screenshots/18_page3_time_intelligence.png)

### Page 4 — Country Profile (Drillthrough)

Accessed by right-clicking any country on Pages 1 through 3 and
selecting Drill through. Shows full price history by fuel type,
annual benchmark comparison, year-by-year summary table, and all
country-level KPIs. Includes a Back button to return to Page 2.

![Country Profile](./screenshots/20_page4_drillthrough_country.png)

---

## Key Insights

**1. The 2022 energy crisis is clearly visible in the data.**
Prices peaked at nearly 3.00 USD per litre globally, driven by
supply disruptions, before stabilising from 2023 onward.

**2. Extreme geographic price disparity exists.**
Hong Kong exceeds 4.00 USD per litre while Venezuela, Libya, Iran,
and Algeria sit below 0.50 USD — a difference driven entirely by
tax and subsidy policy, not crude oil costs.

**3. High-income countries decouple pump prices from crude.**
Taxation creates a price floor, meaning consumers in these markets
benefit less from crude price drops than they suffer from rises.

**4. Most countries show stable YoY changes near 2%.**
Venezuela at -0.26% and Iran at 1.27% are outliers, both reflecting
government price controls rather than market dynamics.

**5. A persistent price premium of around 2.00 USD per litre above
crude equivalent** has been maintained throughout the full period,
representing taxes, refining, and distribution costs.

---

## Recommendations

**1. Gradual subsidy reform in high-subsidy markets.**
Replace blanket subsidies with targeted cash transfers to protect
low-income households without perpetuating fiscal risk at scale.

**2. Price relief trigger mechanisms in high-tax markets.**
Design automatic tax suspension rules when prices exceed a defined
threshold for two or more consecutive quarters.

**3. LPG promotion in price-sensitive markets.**
LPG is consistently the cheapest fuel type across all regions and
periods. Governments in lower-income countries should prioritise
LPG infrastructure as a strategic affordability buffer.

---

## How to Open the Project

1. Download and install [Power BI Desktop](https://powerbi.microsoft.com/desktop) (free)
2. Clone or download this repository
3. Open `pbix/Global_Fuel_Price_Analytics.pbix` in Power BI Desktop
4. If prompted about data source path, update it to point to your local `data/` folder
5. Click Refresh to reload the data

---

## Requirements

- Microsoft Power BI Desktop (latest version recommended)
- No additional software or licenses required to open the .pbix file
- Internet connection required to view the live published dashboard

---

## Author

**Aime 232**
aimmug200507@gmail.com
