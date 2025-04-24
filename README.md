# Real-Estate-Market-Analysis-Dashboard

## Project Overview
This repository contains a comprehensive Power BI dashboard for real estate market analysis. The dashboard provides interactive visualizations and insights into property pricing, location factors, and market trends based on an extensive dataset of real estate transactions.

## Dataset
The analysis is based on a real estate dataset with the following key attributes:
- Transaction date
- House age
- Distance to the nearest MRT station (Mass Rapid Transit)
- Number of convenience stores nearby
- Geographic coordinates (Latitude, Longitude)
- House price of unit area

## Dashboard Features

### Data Transformation & Enhancement
The raw data was transformed using Power Query to:
- Fix data type issues
- Add categorical classifications for:
  - Age categories (New, Almost New, Middle Age, Old, Very Old)
  - Price categories (Very Low, Low, Medium, High, Very High, Premium)
  - Distance categories (Very Close, Close, Medium, Far, Very Far)
  - Store availability categories (Few, Some, Many, Abundant)
- Create geographic point data for mapping

### DAX Measures
Several DAX measures were implemented to analyze the real estate market:
- Average Price = AVERAGE('Real Estate'[House price of unit area])
- Median Price = MEDIAN('Real Estate'[House price of unit area])
- Property Count = COUNTROWS('Real Estate')
- Avg Distance to MRT = AVERAGE('Real Estate'[Distance to the nearest MRT station])
- Price per Convenience = DIVIDE(AVERAGE('Real Estate'[House price of unit area]), AVERAGE('Real Estate'[Number of convenience stores]))
- High Price Properties = CALCULATE(COUNT('Real Estate'[House price of unit area]), 'Real Estate'[House price of unit area] > 40)
- Close to MRT Count = CALCULATE(COUNT('Real Estate'[Distance to the nearest MRT station]), 'Real Estate'[Distance to the nearest MRT station] < 500)
- Price to Distance Ratio = DIVIDE(AVERAGE('Real Estate'[House price of unit area]), AVERAGE('Real Estate'[Distance to the nearest MRT station])*100)

## Dashboard Layout

### 1. Categorical Analysis Dashboard
This view presents price distribution across different categorical dimensions:
- House price by Age Category (Middle Age properties contribute to over 40% of total value)
- House price by Distance Category (Very Close properties dominate with 61.71% of total value)
- House price by Price Category (Medium and High categories together represent ~58% of market value)
- House price by Store Category (Properties with Many and Some convenience stores represent ~74% of market value)

### 2. Location Analysis Dashboard
This view provides spatial analysis of the market:
- Geographic distribution map showing property locations with price intensity
- Price vs MRT distance scatter plot revealing a negative correlation (properties closer to MRT stations command higher prices)
- Regional price analysis by age category showing price variations across different regions

### 3. Real Estate Dashboard
This comprehensive view combines:
- KPI cards showing average price (₹32.62), property count (320), price range (₹63.63), and average price (₹32.44)
- Interactive slicers for Distance Category, Age Category, and Price Category
- Bar chart showing the impact of convenience stores on property prices
- Scatter plot showing the relationship between house age and price

## Key Insights

### Price and Location Relationships
1. **MRT Proximity Effect**: Properties in the "Very Close" distance category to MRT stations command significantly higher prices, representing 61.71% of total market value despite being fewer in number.
2. **Geographic Clustering**: The map visualization reveals price hotspots, with central areas generally commanding higher prices.
3. **Regional Variation**: Central region properties (especially new ones at ₹42.29) command the highest average prices.

### Age and Price Correlation
1. **Middle Age Dominance**: Middle Age properties contribute to over 40% of total market value.
2. **Price Resilience**: Newer properties generally command higher prices, but well-located older properties maintain strong values.

### Convenience Store Impact
1. **Price Correlation**: Properties with more convenience stores nearby (7-10 stores) command higher average prices (₹37-38.5) compared to those with fewer stores (₹28-30).
2. **Market Segment**: Properties in Medium and High price categories have the highest number of convenience stores nearby.

### Market Distribution
1. **Price Segmentation**: Medium and High price categories together represent approximately 58% of the market value.
2. **Property Count**: The dashboard tracks 320 properties in total, providing a substantial sample for analysis.

## How to Use the Dashboard

### Filtering Options
The dashboard provides multiple filtering capabilities:
- Filter by Age Category (New, Almost New, Middle Age, Old, Very Old)
- Filter by Distance Category (Very Close, Close, Medium, Far, Very Far)
- Filter by Price Category (Very Low, Low, Medium, High, Very High, Premium)
- Filter by Region (Central, North, East, West, South)

### Cross-Filtering
All visualizations support cross-filtering - clicking on any category in any chart will filter all other visualizations to show only data related to that selection.

## Technical Implementation

### Power Query Transformations
```
let
    Source = Csv.Document(File.Contents("C:\Path\To\Real_Estate.csv"),[Delimiter=",", Columns=7, Encoding=1252, QuoteStyle=QuoteStyle.None]),
    #"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"Transaction date", type text}, {"House age", type number}, {"Distance to the nearest MRT station", type number}, {"Number of convenience stores", type number}, {"Latitude", type number}, {"Longitude", type number}, {"House price of unit area", type number}}),
    #"Replaced Errors" = Table.ReplaceErrorValues(#"Changed Type", {{"House price of unit area", null}, {"Distance to the nearest MRT station", null}}),
    #"Added Location" = Table.AddColumn(#"Replaced Errors", "Location", each Geography.Point([Latitude], [Longitude], "WGS84")),
    #"Added Price Category" = Table.AddColumn(#"Added Location", "Price Category", each if [House price of unit area] <= 10 then "Low" else if [House price of unit area] <= 30 then "Medium" else if [House price of unit area] <= 50 then "High" else "Premium"),
    #"Added Distance Category" = Table.AddColumn(#"Added Price Category", "Distance Category", each if [Distance to the nearest MRT station] <= 500 then "Very Close" else if [Distance to the nearest MRT station] <= 1000 then "Close" else if [Distance to the nearest MRT station] <= 3000 then "Moderate" else "Far"),
    #"Added Age Category" = Table.AddColumn(#"Added Distance Category", "Age Category", each if [House age] <= 5 then "New" else if [House age] <= 15 then "Recent" else if [House age] <= 30 then "Established" else "Older")
in
    #"Added Age Category"
```

## Future Enhancements
Potential improvements for the dashboard include:
1. Time-series analysis of price trends if data is available over multiple periods
2. Predictive analytics for price forecasting
3. Integration with external demographic or economic data
4. Additional drill-through pages for deeper analysis of specific regions or property types

## Requirements
- Power BI Desktop to open and modify the dashboard
- The original CSV data file (Real_Estate.csv)

## Installation & Setup
1. Clone this repository
2. Open the .pbix file in Power BI Desktop
3. If prompted, update the data source path to the location of your Real_Estate.csv file
4. Refresh the data to apply all transformations

