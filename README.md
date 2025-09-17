# Customer-Performance-Dashboard-DAX-Power-BI
I created a Customer Performance Analysis Report in Power BI using a star schema data model with multiple related tables. The report applies advanced DAX formulas. This report helps stakeholders to evaluate customer performance, identify growth opportunities, and take data-driven decisions for improving customer engagement and profitability.

## 🔎 Overview  
This project presents a **Customer Performance Analysis Dashboard** built entirely in **Power BI**.  
The dashboard provides a comprehensive view of customer demographics, buying patterns, revenue segmentation, and gender-based earnings.  
It incorporates **light and dark theme modes**, interactive filters (slicers), and dynamic visuals powered by **advanced DAX formulas** for precise calculations and insights.  

## 📷 Dashboard Views  
### 🌑 Dark Theme  
![Dark Theme Dashboard](https://github.com/Rutvik1429/Customer-Performance-Dashboard-DAX-Power-BI/blob/main/Customer_Perfomance_Analysis_Dark_Theme.png)  

### 🌕 Light Theme  
![Light Theme Dashboard](https://github.com/Rutvik1429/Customer-Performance-Dashboard-DAX-Power-BI/blob/main/Customer_Perfomance_Analysis_Light_Theme.png)  

## 🎯 Objectives  
- To analyze **customer demographics** (age, gender, family status).
- Deliver a user-friendly and insightful dashboard to monitor and analyze customer performance.
- To evaluate **revenue contribution** by age groups and gender.
- Enable data-driven decision-making by visualizing important metrics such as age distribution, revenue by demographic, customer segmentation, and contribution by gender.
- To identify **top-performing customers** and their purchase impact.  
- To distinguish between **customers with children vs. without children** and their revenue contributions.
- Showcase Power BI proficiency, including advanced use of DAX for custom measures and context-based analytics.
- To improve decision-making using **data-driven insights** on customer behavior.  


## 📌 Key Features  
- **Theme Mode Toggle** → Light & Dark dashboard views for better user experience.  
- **Dynamic Slicers** → Filter analysis by country and customer segments.  
- **Advanced DAX Calculations** → Custom KPIs, customer segmentation, ranking, and revenue share metrics.  
- **Customer Profiling** → Loyal, periodic, and VIP customer categorization.  
- **Top Customer Insights** → Identification of top 4 esteemed buyers with revenue contribution.  


## 📈 Insights from the Dashboard  
- **Average Customer Age**: 45 years.  
- **Total Customers**: 18K across 6 countries.  
- **Customer Demographics**:  
  - 71% Customers **with children** generate higher revenue ($77M+).  
  - 29% Customers **without children** contribute less ($39M+).  
- **Revenue by Age Group**:  
  - Highest revenue comes from **31–40 age group ($80M)** followed by 51–60 ($77M).  
  - Average revenue line: $61.4M.
- **Customer Profiling**:
  - Visual segmentation of loyal, periodic, and VIP customers for strategic analysis.
- **Top Customers**:  
  - Willie Xu leads with **194K purchases**, followed by Jordan (189K).  
- **Earnings by Gender**:  
  - Male customers → **$153M (49.7%)**  
  - Female customers → **$154M (50.3%)** (slightly higher contribution).  

## Features
- Fully interactive Power BI dashboard (.pbix) with DAX-powered insights.
- Dynamic color theme toggle (light/dark mode).
- Country and customer group slicers for flexible analysis.
- In-depth customer profiling and revenue breakdowns by various demographics.
- Easy-to-read visuals for at-a-glance understanding by business stakeholders.

## 📂 Schema Structure Explanation
This project uses a Star Schema design in Power BI to build an efficient data model. The schema consists of one Fact Table (transactions/sales) connected to multiple Dimension Tables (descriptive attributes). Additionally, there are Measure Tables that store DAX calculations for insights.

![Schema Structure](https://github.com/Rutvik1429/Customer-Performance-Dashboard-DAX-Power-BI/blob/main/Schema%20Structure.png)
## Fact Table:
**Keys**
- CustomerKey, ProductKey, SalesTerritoryKey, GeographyKey
**Columns**
- OrderDate, DueDate, ShipDate → Dates for transaction lifecycle.
- OrderQuantity → Number of products ordered.
**Purpose**
- Stores the core transactional data (orders placed, shipped, and quantities sold).
- It is the central table linked with all dimension tables.

## Dimension Tables:
### DimCustomer
**Columns**
- CustomerKey (Primary Key)
- Full Name, Gender, Age, Age Category, BirthDate, Customer Type
**Purpose**
- Contains demographic and personal attributes of customers for segmentation (e.g., male vs. female, with/without children, age group, etc.).

### DimProduct
**Columns**
- ProductKey (Primary Key)
- ProductName, Color, Cost, Price
**Purpose**
- Provides product details (pricing, cost, attributes). Used to analyze revenue by product type.

### DimGeography
**Columns**
- GeographyKey (Primary Key)
- Country, City
**Purpose**
- Holds geographic information to analyze customers and revenue by country or city.

### DimSalesTerritory
**Columns**
- SalesTerritoryKey (Primary Key)
- SalesTerritoryRegion, SalesTerritoryCountry, SalesTerritoryGroup, SalesTerritoryAlternateKey
**Purpose**
- Represents sales regions (territory & grouping). Helps in regional revenue analysis.

## 🧩 Schema Flow
- The FactTable connects with all dimension tables through foreign keys (CustomerKey, ProductKey, SalesTerritoryKey, GeographyKey).
- Dimension tables enrich the fact data with descriptive attributes.
- Measure tables store advanced DAX logic to calculate KPIs and insights used in dashboards.

### DAX Measure: Caption customer without children
This measure highlights the **top country with the most customers without children**  
and breaks it down into percentage share, revenue, and gender split.
```DAX
Caption customer without children = 
    -- total countries 
    VAR _noofcountries =
        DISTINCTCOUNT(DimGeography[Country])

    -- this extract the top country with more customer without children --

    VAR _topcountry =
        TOPN(
            1,
            DISTINCT(DimGeography[Country]),
            [C without C],
            DESC,
            DimGeography[Country],ASC)
    
    -- total customer in the top country --

    VAR _totalcustomertopcuntry =
        CALCULATE(
            DISTINCTCOUNT(DimCustomer[CustomerKey]),
            _topcountry
            )

    -- here we remove filter from the country column and return all customer withour children --

    VAR _allcustomerwithouchildren =
        CALCULATE(
        [C without C],
        _topcountry,
        ALL(
            DimGeography[Country]
            )
        )

    -- this calculate total revenue from top country --

    VAR _totalrevenutopcounty =
        CALCULATE(
            [C without C],
            _topcountry
        )

    -- pctcustomer of without children --

    var _pctcustomerwithoutchildern =
        DIVIDE(
            _totalrevenutopcounty,
            CALCULATE(
                [C without C],
                ALL(DimGeography[Country])
            )
        )

    -- calculate male customer without children --

    VAR _malegenderwithoutchildren =
        CALCULATE(
            [C without C],
            _topcountry,
            DimCustomer[Gender]="M"
        )

    -- extrect % male customer without children --

    VAR _pctmalegenderwithoutchildren =
        DIVIDE(
            _malegenderwithoutchildren,
            _allcustomerwithouchildren
        )

     -- calculate female customer without children --

    VAR _femalegenderwithoutchildren =
        CALCULATE(
            [C without C],
            _topcountry,
            DimCustomer[Gender]="F"
        )

    -- extrect % female customer without children --

    VAR _pctfemalegenderwithoutchildren =
        DIVIDE(
            _femalegenderwithoutchildren,
            _allcustomerwithouchildren
        )

    RETURN
        UPPER(_topcountry)& " Leads With "&
        FORMAT(_pctcustomerwithoutchildern,"0.00%")& " Customers Without Children Amonge "&
        FORMAT(_noofcountries,"#,##")& " Countrise,Gneerating "&
        FORMAT(_totalrevenutopcounty,"$#,##")& " In Revenue From "&
        FORMAT(_totalcustomertopcuntry,"#,##")& " Coustomers,Comprising "&
        FORMAT(_pctmalegenderwithoutchildren,"0.00%")& " Male and "&
        FORMAT(_pctfemalegenderwithoutchildren,"0.00%")& " Female "
```
- Count total countries
- Identify the top country (highest customers without children)
- Calculate:
  - Total customers (in top country)
  - Total revenue (from top country)
  - % of customers without children (vs all countries)
- Gender Breakdown:
  - % Male customers without children
  - % Female customers without children
- Return a formatted caption combining all insights

## DAX Measure: Caption customer with children
This measure highlights the **top country with the most customers with children**  
and breaks it down into percentage share, revenue, and gender split.
**This is Same as Without childer but point are used difference**

## DAX Measure: Avg Caption  
This measure builds a dynamic caption comparing **age group revenue vs. the overall average**.
```DAX
Avg Caption = -- This is the Revenue of Age-Group above and below average Caption -- 
        VAR _AvgRevenueAgegroup =
            AVERAGEX(
                SUMMARIZE(DimCustomer,DimCustomer[Age Category]),[Age revenue (age group)]
                )
        VAR _Revenuefiltertable =
            SUMX(
                FILTER(
                    SUMMARIZE(DimCustomer,DimCustomer[Age Category]),
                    [Total Revenue]>_AvgRevenueAgegroup
                ),
                [Total Revenue]
                )
        VAR _TopAgeGroup =
            TOPN(1,
            DISTINCT(DimCustomer[Age Category]),[Total Revenue],DESC,DimCustomer[Age Category],ASC)
        VAR _RevenueTopAgeGroup =
            CALCULATE([Total Revenue],_TopAgeGroup)
        VAR _Result =
            DIVIDE(
                _Revenuefiltertable,
                [Total Revenue]
                )
        RETURN
            FORMAT(_Result,"#% ") & "OF Revenue is attributed to the yellow bars, primarily led by the "& _TopAgeGroup & " Age-Group surpassing  the average Revenue LINE"
```

- Calculate **average revenue across all age groups**  
- Identify age groups with revenue **above average**  
- Find the **top-performing age group** and its revenue  
- Compute % share of above-average groups vs total revenue  
- Return a formatted caption:  
  → e.g., "65% of revenue is attributed to the yellow bars, primarily led by the 31–40 Age-Group surpassing the average revenue line"

## 🛠️ Tools & Technologies Used  
- **Power BI** → Data modeling, dashboard creation, theme design.  
- **DAX (Data Analysis Expressions)** → Custom KPIs, measures, ranking logic.  
- **Star Schema** → Efficient data modeling across multiple tables.  

## 📂 Dataset  

The dataset used in this project is available in the `Dataset/` folder.  
You can explore it here: [Dataset Folder](https://github.com/Rutvik1429/Customer-Performance-Dashboard-DAX-Power-BI/tree/main/Dataset)  


## 🚀 Conclusion  
This dashboard enables businesses to **track customer behavior, identify revenue-driving segments, and optimize strategies** for better customer engagement and profitability.  
By leveraging **DAX-powered measures** and interactive design, it serves as a powerful decision-making tool.  
