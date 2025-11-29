# Housing Market Report | Google BigQuery | Power BI | SQL | ChatGPT

A multi-page Power BI dashboard analyzing house prices, sales performance, regional trends, house types, and market behavior across Denmark’s housing market.

This report is made with tools, Google Big Query, SQL to perform query in Google Big Query and Power BI and used ChatGPT for and SQL queries.

## Problem Statement

This dashboard provides a 360-degree view of the real-estate market by combining pricing trends, sales volumes, regional behavior, and house-type financial characteristics.
It helps:

- Home buyers & investors understand price patterns
- Real-estate analysts study regional market performance
- Agencies evaluate offer vs purchase behavior
- Lenders understand housing value & yield trends
- Policy makers analyze housing growth dynamics

The dashboard consists of three key pages:

- House Market Overview
- Sales Performance
- House Type Analysis

### Steps Followed

- step 1 : Used Google cloud account to load the BigQuery.
- step 2 : Loaded data into BigQuery and the created dataset named "1" and table named "Housing".
- step 3 : Opened SQL query in the table mentioned, the query made are as follows:

  SELECT * FROM 'stalwart-bliss-452720-c7.1.Housing' ;  // to see the data

  SELECT *
FROM 'stalwart-bliss-452720-c7.1.Housing' 
WHERE 'city' IS NULL;

  SELECT *
FROM 'stalwart-bliss-452720-c7.1.Housing' 
WHERE 'area' IS NULL;

// to find rows where a column is null

SELECT
  COALESCE('city', "Unknown") AS 'city',
  COALESCE('area', "Unknown") AS 'area'
FROM 'stalwart-bliss-452720-c7.1.Housing';

// found null values in two cloumns and filled null values with "Unknown"

  SELECT *
FROM 'stalwart-bliss-452720-c7.1.Housing' 
WHERE 'annual_inflation_rate_%' IS NULL;

SELECT
  IFNULL('annual_inflation_rate_%', "1.85") AS 'annual_inflation_rate_%'
FROM 'stalwart-bliss-452720-c7.1.Housing';

//found null values in numerical data and filled it with mode


- step 4 : Connected the BigQuery with Power BI Desktop.Opened power query editor and in view tab, under Data preview section, check "column distribution", "column quality" and "column profile" options.
- step 5 : Created a new table in report view named "Measure's Table 1" to create DAX queries and store them as follows:

a) count of house type = 
DISTINCTCOUNT('Housing Data'[house_type])

b) last 12 months sales = 
CALCULATE(SUM('Housing Data'[purchase_price]),
DATESINPERIOD('Housing Data'[date],MAX('Housing Data'[date]),-12,MONTH))

c) median sales price change = 
VAR currmedianprice = 
    MEDIANX(FILTER('Housing Data',
    YEAR('Housing Data'[date]) = YEAR(MAX('Housing Data'[date]))),
    'Housing Data'[purchase_price])

VAR premedianprice =
    MEDIANX(FILTER('Housing Data',
    YEAR('Housing Data'[date]) = YEAR(MAX('Housing Data'[date])) - 1 ),
    'Housing Data'[purchase_price])

RETURN
    IF(premedianprice <> 0, (currmedianprice - premedianprice)/premedianprice, BLANK())

d) units sold in latest year && quarter = 
CALCULATE(DISTINCTCOUNT('Housing Data'[house_id]),
YEAR('Housing Data'[date]) = YEAR(MAX('Housing Data'[date]))
&&
QUARTER('Housing Data'[date]) = QUARTER(MAX('Housing Data'[date])))

e) yoy sales growth = 
VAR curryear = 
    CALCULATE(SUM('Housing Data'[purchase_price]),
    YEAR('Housing Data'[date]) = YEAR(MAX('Housing Data'[date])))
VAR prevyear = 
    CALCULATE(SUM('Housing Data'[purchase_price]),
    YEAR('Housing Data'[date]) = YEAR(MAX('Housing Data'[date])) -1)
RETURN
    IF(prevyear <> 0, (curryear - prevyear)/prevyear,BLANK())

f) avg price sqm = 
AVERAGE('Housing Data'[sqm_price])

g) offer to sqm ratio = 
DIVIDE(SUM('Housing Data'[offer price]),SUM('Housing Data'[sqm]))

h) sales by region = 
    CALCULATE(SUM('Housing Data'[purchase_price]),
    ALLEXCEPT('Housing Data','Housing Data'[region]))

i) toalytd sales = 
TOTALYTD(SUM('Housing Data'[purchase_price]),'Housing Data'[date].[Date])

- step 6 : Created three page and added canvas backgronud images to each pages for the report, further created two new columns as follows:

  Pages

  a) House Market Overview

  b) Sales performance

  c) House Type Analysis

  New columns

  a) offer price = 
100*'Housing Data'[purchase_price]/(100- 'Housing Data'[%_change_between_offer_and_purchase])

  b) house age = 
ABS(YEAR('Housing Data'[date].[Date]) - 'Housing Data'[year_build])

- step 7 : Created charts and visuals in each page as folows:

  a) House Market Analysis

  Three charts
  
  - Median Sales Price change by city and regiom
  - Offer Price V/S Purchase price
  - Year in Year Growth by Sales
 
    Three Cards

    - count of House Type
    - Units Sold in Latest Year in Quarter
    - Last 12 Months sales

   b) Sales Performance

  table

  - To get insights on Total year till date sale
 
  Four charts and one key influencer

  - Offer To Square Meter Ratio by Sales Type
  - Average Price Square Meter by region
  - Sales by Region
 
 c) House Type Analysis

 Three slicers

 - Region
 - Area
 - Sales type

Three Charts

- Average Offer/Purchase Price by House by House Type
- Average Inflation/Interest/Yield Price by House Type
- Average Square Meter Price by House Type

### Report Snapshot (Power BI Desktop)

![Dashboard_upload](https://github.com/rahulgowda2003/Housing-Report-Google-BigQuery-Power-BI-SQL/blob/main/House%20Market%20Overview%20Screenshot.png)

![Dashboard_upload](https://github.com/rahulgowda2003/Housing-Report-Google-BigQuery-Power-BI-SQL/blob/main/Sales%20Performance%20Screenshot.png)

![Dashboard_upload](https://github.com/rahulgowda2003/Housing-Report-Google-BigQuery-Power-BI-SQL/blob/main/House%20Type%20Analysis%20Screenshot.png)


## Insights

a) House Market Overview
1] Median Sales Price Change by City and Region

The bar chart displays how median sales prices vary across different cities, with regions color coded:

- Bornholm
- Fyn & Islands
- Jutland
- Zealand

Top cities by price change:

City	Price Change

- Kettinge	6.88
- Nørreballe	5.66
- Almind	4.37
- Agerskov	4.35
- Røma	4.03

Kettinge and Nørreballe show the strongest price growth.

2] Count of House Types

- Total 5 house types available in the market.

3] Offer Price vs Purchase Price Scatter Plot

Shows correlation between offer and actual purchase price across regions.

A strong upward diagonal trend indicates buyers generally purchase homes close to offer price, especially in high-value regions like Zealand.

4] Year on Year Growth by Sales

Sales Type	YoY Growth (%)

- auction	0
- other_sale	0
- regular_sale	0
- family_sale	-1

Growth is stable across sale types except family_sale, which shows decline.

5] Units Sold in Latest Quarter

- 430 units sold in the most recent quarter.

6] Last 12 Months Sales Volume

- 4bn DKK cumulative sales in last 12 months.

b) Sales Performance

1] Key Influencers

Purchase price increases when:

- House age > 75 : +431.2K
- House age ≤ 15 : +326.6K

Older, larger heritage properties tend to push purchase price upward.

2] Offer to Square Meter Ratio by Sales Type

Sales Type	Offer/Sq Meter Ratio

- regular_sale	15.1K
- other_sale	13.4K
- family_sale	11.7K
- auction	11.2K

Regular sales command the highest square-meter pricing.

3] Average Price Per Square Meter by Region

Region	Avg Price,	% Share

- Zealand	21.04K,	35.54%
- Jutland	13.5K,	22.8%
- Fyn & Islands	13.76K,	23.24%
- Bornholm	10.9K,	18.42%

Zealand dominates pricing due to major cities and higher demand.

4] Sales by Region

Region	Sales

- Zealand	36983M
- Jutland	31160M
- Fyn & Islands	5762M
- Bornholm	470M
  
5] Total Year-till-Date Sales Table

Contains sales values by:

- Year
- Quarter
- Month
- Total Year Till Date Sales

Shows consistent high-volume sales in Q3 and Q4 across all years.

 c) House Type Analysis

Filters/Slicers:

- Region
- Area
- City
- Sales Type

1] Average Offer & Purchase Price by House Type

House Type	Offer, Price	Purchase Price

- Farm	2.7M,	2.7M
- Apartment	2.4M,	2.5M
- Townhouse	2.1M,	2.1M
- Villa	1.8M,	1.8M
- Summerhouse	1.2M,	1.2M

Apartments show slightly higher purchase price than offer price, indicating competitive demand.

2] Average Inflation, Interest & Yield by House Type

House Type,	Inflation,	Interest,	Yield

- Farm	1.9,	2.1,	4.6
- Villa	1.9,	1.8,	4.2
- Apartment	1.9,	1.6,	3.9
- Summerhouse	1.9,	1.4,	3.8
- Townhouse	1.9,	1.5,	3.9

Farm properties deliver the highest yield (4.6%).

3] Average Square Meter & Price by House Type

House Type	Sq. Meter,	Sq. Meter Price

- Farm	195.1,	13.7K
- Villa	152.47,	12.0K
- Townhouse	108.37,	19.3K
- Apartment	86.44,	29.0K
- Summerhouse	83.5,	15.2K

Apartments have the highest price per sq meter (29K) despite being one of the smallest property types.

## Key Insights Across All Pages

a) Regional Market Behavior

- Zealand leads in both home prices and total sales volume.
- Bornholm is least expensive and has lowest sales.

b) House Type Economics

- Apartments have the highest per-meter pricing.
- Farm properties yield the highest ROI.

c) Sales Patterns

- Last 12 months sales = 4bn .
- Regular sales fetch highest sq-meter value.

d) Price Drivers

- House age plays a major role—higher age increases price significantly.

e) Offer vs Purchase Price

- Very strong positive correlation—ideal for pricing stability assessment.

