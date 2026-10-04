# AdventureWorks Sales Analysis

An interactive Power BI report analysing sales, product costs, calculated profit, customer behaviour, and product returns.

## Project Overview

This project uses AdventureWorks sales and lookup data to explore business performance across products, customers, and geographic markets.

The report combines executive-level indicators with detailed product and customer views, geographic exploration, and drillthrough pages.

## Dashboard Preview

### Sales Overview

![Sales Overview](screenshort%201.jpeg)

### Geographic Analysis

![Geographic Analysis](screenshort%202.jpeg)

### Product Details

![Product Details](screenshort%203.jpeg)

### Customer Details

![Customer Details](screenshort%204.jpeg)

## Business Objectives

- Monitor revenue, product costs, and calculated profit.
- Track order volume, quantities sold, and quantities returned.
- Compare product and subcategory performance.
- Explore customer purchasing patterns.
- Analyse sales across geographic markets.
- Examine monthly revenue and profit trends.

## Tools and Technologies

| Tool | Application |
|---|---|
| Power BI Desktop | Data modelling and interactive reporting |
| Power Query | CSV import, folder combination, and data transformation |
| DAX | Financial calculations, customer metrics, and time intelligence |
| Field Parameters | Interactive metric and location selection |
| CSV Files | Sales, returns, and lookup data sources |

## Report Structure

| Page | Purpose |
|---|---|
| Home | Sales indicators and performance overview |
| Map | Geographic analysis with metric and location parameters |
| Product Details | Product-level financial and order analysis |
| Customer details | Customer attributes and purchasing metrics |
| Tooltip 1 | Supporting tooltip page |
| Product drillthrough | Detailed product exploration |
| Customer drillthrough | Detailed customer exploration |

## Data Model

| Table | Description |
|---|---|
| `Sales Table` | Order dates, order numbers, quantities, and related keys |
| `Returns Data` | Return dates, products, territories, and returned quantities |
| `Product Lookup` | Product attributes, prices, and costs |
| `Product Subcategories Lookup` | Product subcategory definitions |
| `Product Categories Lookup` | Product category definitions |
| `Customer Lookup` | Customer demographics and profile attributes |
| `Territory lookup` | Region, country, and continent information |
| `Calendar Lookup` | Date attributes for filtering and time analysis |

Power Query combines sales files from a folder and imports supporting lookup files separately.

## Key Performance Indicators

| Metric | Displayed Value |
|---|---:|
| Number of Orders | 25.16K |
| Total Product Cost | $14.5M |
| Total Order Quantity | 84K |
| Calculated Profit | $10.5M |
| Total Revenue | $24.9M |
| Quantity Returned | 2K |
| Product Return Rate | 2.17% |
| Average Orders per Customer | 1.44 |
| Average Cost per Customer | $830.09 |
| Average Profit per Customer | $600.47 |
| Average Revenue per Customer | $1.43K |
| Average Quantity Sold per Customer | 5 |

> These are rounded values displayed in the overview screenshot. The source CSV files are not included, so the figures have not been independently recalculated.

## Metric Definitions

- **Revenue:** Order quantity multiplied by the related product lookup price.
- **Product Cost:** Order quantity multiplied by the related product lookup cost.
- **Calculated Profit:** Revenue minus product cost.
- **Orders:** Distinct order numbers.
- **Customers:** Distinct customer keys in the sales table.
- **Product Return Rate:** Returned quantity divided by sold quantity.

Calculated profit does not account for operating expenses, taxes, or other costs. Revenue is calculated using lookup prices rather than a recorded transaction-price field.

## Key Findings

### 1. Revenue and Profit Trends

The overview chart shows lower monthly revenue and profit around late 2020, followed by a general increase through the later displayed period.

Selected labels rise to approximately **$1.8M in monthly revenue** and **$0.77M in monthly calculated profit** near the end of the chart.

The causes of this movement require further analysis of quantities, product mix, and reporting-period completeness.

### 2. Product Subcategory Performance

The overview displays the following revenue values:

| Subcategory | Displayed Revenue |
|---|---:|
| Road Bikes | $4.4M |
| Mountain Bikes | $3.9M |
| Touring Bikes | $1.4M |
| Tires and Tubes | $0.2M |
| Helmets | $0.1M |

The product detail page also shows Tires and Tubes leading the displayed order-count ranking at approximately **9K orders**.

These observations distinguish order frequency from revenue contribution. Products with frequent orders do not necessarily generate the greatest revenue.

The overview subcategory chart's filter context should be checked before interpreting its values as full-report totals.

### 3. Revenue by Gender

| Gender Category | Displayed Revenue Share |
|---|---:|
| Female | 50.23% |
| Male | 49.14% |
| NA | 0.63% |

Revenue is distributed almost evenly between the Female and Male categories.

The displayed profit values are approximately **$5.3M for Female**, **$5.1M for Male**, and **$0.1M for NA**.

These are descriptive comparisons and do not establish why purchasing patterns differ.

### 4. Geographic Coverage

The map displays customer markets in:

- United States
- Canada
- United Kingdom
- France
- Germany
- Australia

Metric and location parameters support different geographic views. Exact market rankings should be confirmed using the selected metric and filter context.

### 5. Product Returns

The overview displays a quantity-based return rate of **2.17%**.

This ratio compares returned units with sold units. It should not be described as the percentage of orders returned.

Returns are stored separately from sales, so date relationships and reporting windows require validation before interpreting changes in return rates.

## Core DAX Measures

### Order and Customer Counts

```dax
NO of Order =
DISTINCTCOUNT('Sales Table'[OrderNumber])

Total Customer =
DISTINCTCOUNT('Sales Table'[CustomerKey])

Total Order Qty =
SUM('Sales Table'[OrderQuantity])
```

### Revenue, Cost, and Profit

```dax
Total Revenue =
SUMX(
    'Sales Table',
    'Sales Table'[OrderQuantity] *
    RELATED('Product Lookup'[ProductPrice])
)

Total Cost =
SUMX(
    'Sales Table',
    'Sales Table'[OrderQuantity] *
    RELATED('Product Lookup'[ProductCost])
)

Total Profit =
[Total Revenue] - [Total Cost]
```

### Return Metrics

```dax
Quantity Returned =
SUM('Returns Data'[ReturnQuantity])

Product Return Rate =
DIVIDE(
    [Quantity Returned],
    [Total Order Qty]
)

No of Returns =
COUNTROWS('Returns Data')
```

`No of Returns` counts return records, not necessarily distinct return transactions or customers.

### Customer Averages

```dax
Avg Revenue Per Customer =
DIVIDE([Total Revenue], [Total Customer])

Avg Profit Per Customer =
DIVIDE([Total Profit], [Total Customer])

Avg Cost Per Customer =
DIVIDE([Total Cost], [Total Customer])

Avg Order Per Customer =
DIVIDE([NO of Order], [Total Customer])

Avg Qty Sold Per Customer =
DIVIDE([Total Order Qty], [Total Customer])
```

### Previous-Month Revenue

```dax
Previous Month Revenue =
CALCULATE(
    [Total Revenue],
    DATEADD(
        'Calendar Lookup'[Date],
        -1,
        MONTH
    )
)
```

Time-intelligence calculations require a suitable calendar table and valid date relationships.

## Analysis Workflow

1. Import product, customer, territory, calendar, and returns CSV files.
2. Combine sales files using the Power Query folder connector.
3. Assign data types and transform customer attributes.
4. Enrich the calendar with month, quarter, and weekday information.
5. Create financial, order, customer, and return measures.
6. Build overview, map, product, and customer pages.
7. Add field parameters, navigation, tooltips, and drillthrough views.
8. Validate calculations and visual filter contexts.

## Business Recommendations

- Investigate product mix and quantities behind revenue growth.
- Compare high-order accessories with high-revenue bike categories.
- Review product-level return patterns alongside sales volume.
- Evaluate customer segments using both revenue and calculated profit.
- Compare markets using consistent dates and metric definitions.
- Include transaction prices and additional costs for more complete financial analysis.

These recommendations are proposed analytical actions. Their business impact has not been measured.

## Repository Contents

| File | Description |
|---|---|
| `Sales analysis project.pbit` | Power BI report template |
| `screenshort 1.jpeg` | Sales overview screenshot |
| `screenshort 2.jpeg` | Geographic analysis screenshot |
| `screenshort 3.jpeg` | Product detail screenshot |
| `screenshort 4.jpeg` | Customer detail screenshot |
| `README.md` | Project documentation |

## How to Open the Project

1. Download or clone this repository.
2. Open `Sales analysis project.pbit` in Power BI Desktop.
3. Obtain compatible AdventureWorks sales and lookup CSV files.
4. Update the file and folder paths in Power Query.
5. Check the helper queries used to combine sales files.
6. Apply changes and refresh the report.

> The template references local files and folders on the author's computer. Source CSV files are not included in this repository.

## Limitations and Future Improvements

- Document the dataset source, licence, and reporting period.
- Parameterise source file and folder paths.
- Validate relationships and lookup-key uniqueness.
- Check for duplicate sales records when combining files.
- Confirm that calendar filters apply consistently to sales and returns.
- Explain the use of lookup prices in revenue calculations.
- Distinguish calculated product profit from net profit.
- Correct repeated “Monthly Trend” titles on product and occupation charts.
- Verify the metric behind the occupation visual before describing it as revenue.
- Document visual filters that produce different subcategory totals.
- Review the `Bike Return` measure: its current filter selects Clothing.
- Review the `Total Returns` measure, which has no expression in the inspected model.
- Add year-over-year and month-over-month comparisons.
- Confirm that publicly displayed customer details are appropriate for sharing.

## Author

**Junaed Bogdadi**

[GitHub Profile](https://github.com/junaed-bogdadi)
