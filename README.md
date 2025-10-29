# Shopify-Sales-Customer-Funnel-Analytics-Dashboard
Developed an automated Shopify sales dashboard
### GitHub Portfolio Project Description

#### Project Title: Shopify Sales & Customer Funnel Analytics Dashboard

**Overview**  
This Power BI dashboard analyzes Shopify transaction data to uncover insights into sales performance, customer segmentation, regional trends, and retention metrics. Using a dataset of ~7,400 line items (from the provided `Shopify Sales.xlsx`), the project transforms raw e-commerce data into an interactive funnel report. Key focus: Enabling data-driven decisions for revenue optimization and customer loyalty strategies.

**Business Objectives** (from requirements doc):  
- Track transaction KPIs: Net Sales, Total Quantity, Avg Order Value.  
- Profile customer behavior: Unique customers, single vs. repeat buyers.  
- Measure retention: LTV, Repeat Rate, Purchase Frequency.  
- Visualize trends: Regional maps, time-series (daily/hourly), payment gateways, and product categories.  
- Support drill-through for granular exploration (e.g., order-level details).

**Key Insights Generated**  
Based on the dashboard outputs and data analysis:  

| Category | Metric | Value | Insight |  
|----------|--------|-------|---------|  
| **Transaction Performance** | Net Sales | $4,180,874 | Strong baseline revenue; 7,534 units sold indicate healthy volume, but AOV ($562.60) suggests upselling opportunities for bundles. |  
| | Total Quantity | 7,534 | Concentrated in footwear (e.g., Running Shoes: ~28% of sales volume). |  
| **Customer Behavior** | Total Customers | 4,431 | 54% single-order (2,392) vs. 46% repeat (2,039)—focus retention efforts on the latter for 2x LTV uplift. |  
| **Retention & Value** | Lifetime Value (LTV) | $943.60 | Calculated via DAX: `LTV = DIVIDE(SUM(Total Price USD), DISTINCTCOUNT(Customer ID))`. High potential in repeat segments. |  
| | Repeat Rate | 46% | `Repeat Rate = DIVIDE([Repeat Customers], [Total Customers])`. Derived from customer-order grouping. |  
| | Purchase Frequency | 1.68 | `Purchase Freq = DIVIDE(COUNTROWS(Orders), DISTINCTCOUNT(Customer ID))`. Low avg signals need for re-engagement campaigns. |  
| **Regional Overview** | Top Provinces | Texas (high density), California | Filled map uses color saturation on selected measures; bubble map for cities (e.g., Houston: top performer with 200+ customers). |  
| | Top Cities (by Customers) | Houston, NYC, El Paso, Dallas | Bar chart sorted descending; tooltips show full KPIs for cross-filtering. |  
| **Trends Over Time** | Daily Peak | Mid-week spikes (e.g., Wed-Fri: 1,000+ customers) | Area chart for day trends; reveals promotional timing sweet spots. |  
| | Hourly Peak | 18:00-21:00 (20% of daily activity) | Line chart highlights evening surges—ideal for flash sales. |  
| **Payment Gateways** | Shopify Payments | 51.96% (3,117 customers) | Donut chart; PayPal (19.64%) and Gift Cards (19.29%) as secondary prefs—streamline for conversion. |  
| **Product Types** | Running Shoes | 2,132 customers (48% share) | Stacked bar; Tennis/Walking Shoes follow—categorize for inventory forecasting. Low performers (e.g., Water Shoes: 5) flag deprioritization. |  

**Technical Implementation**  
Followed a structured ETL-to-viz pipeline (inspired by project steps in `Shopify PPT.pptx`):  

1. **Data Ingestion & Cleaning** (Power Query):  
   - Connected to `Shopify Sales.xlsx` (Sheet: shopify_sales).  
   - Handled duplicates (e.g., CITY vs. Billing Address City—merged/redundancy check).  
   - Data types: Converted Invoice Date to DateTime; Subtotal Price/Total Price USD to Currency (USD).  
   - Removed nulls in Customer ID/Product Type; filtered US-only (Billing Address Country = "United States").  
   - Created Date table for time intelligence: `Date = CALENDAR(MIN(Invoice Date), MAX(Invoice Date))`.  

2. **Data Modeling** (Star Schema):  
   - Fact Table: Line Items (keys: Order Number, Customer ID, Product ID, Variant ID).  
   - Dimension Tables: Customers (from unique Customer IDs), Products (Product Type, ID), Dates, Gateways, Locations (Province/City).  
   - Relationships: One-to-Many (e.g., Customers → Line Items on Customer ID).  
   - Inactive relationships for advanced calcs (e.g., cross-filter Province to City).  

3. **DAX Calculations** (Key Measures):  
   - **Net Sales**: `Net Sales = SUM(Subtotal Price) - SUM(Total Tax)` (excludes taxes per requirements).  
   - **Repeat Customers**: `Repeat Customers = CALCULATE(DISTINCTCOUNT(Customer ID), FILTER(ALL(LineItems), [Order Count per Customer] > 1))` (using summarized table for order counts).  
   - **Single Order Customers**: `Single Order Customers = [Total Customers] - [Repeat Customers]`.  
   - **LTV**: As above; handles multi-currency via Total Price USD.  
   - **Dynamic Measure Selector**: Field parameter for switching KPIs across visuals (Net Sales, Total Quantity, etc.).  
   - Time-based: `Customers by Hour = HOUR(Invoice Date)`.  

4. **Visualization & Interactivity** (Power BI Desktop):  
   - **Pages**: Overview (KPIs + trends) & Details (drill-through table with order-level rows).  
   - **Core Visuals**:  
     - KPI Cards: Transaction/Customer/Retention metrics.  
     - Filled Map: Province-level (color by measure).  
     - Bubble/Density Map: City granularity (size/intensity by customers).  
     - Area/Line Charts: Daily/hourly trends.  
     - Donut/Bar Charts: Gateways & Product Types.  
     - Table: Drill-through to raw data (e.g., Order Number, Customer Name, Net Sales).  
   - Slicers: Gateway, Province, Measure Selector.  
   - Formatting: Conditional colors (green for high performers), tooltips with all KPIs.  

**Challenges & Solutions**  
- **Granularity Mismatch**: Line-item data required aggregation to order/customer level—solved with SUMMARIZE/ADDCOLUMNS in DAX.  
- **Time Intelligence**: Irregular Invoice Dates—used custom Date table with fiscal calcs.  
- **Performance**: Large dataset (~7K rows)—optimized with DirectQuery fallback and query folding in Power Query.  
- **Insights Validation**: Cross-checked totals (e.g., sum(Total Price USD) = $4.6M pre-tax) against Excel for accuracy.  

**Tools & Skills Demonstrated**  
- **Power BI**: ETL (Power Query), Modeling, DAX (advanced measures like CALCULATE, DIVIDE for YoY potential), Visuals & Bookmarks.  
- **Excel**: Initial data prep/export.  
- **Domain Knowledge**: E-commerce metrics (LTV, AOV) from Shopify terminology doc.  

**Files Included**  
- `Shopify Sales.xlsx`: Raw dataset.  
- `Shopify - Data Terminology.docx`: Field definitions.  
- `Shopify Sales and Customer Dashboard.pdf`: Exported visuals.  
**Future Enhancements**  
- Integrate real-time Shopify API for live data.  
- Add predictive modeling (e.g., churn risk via Python in Power BI).  
- YoY comparisons using DAX time-intel functions.  
