# Data-Modeling-Project-using-Power-BI

# Enterprise Sales & Financial Analytics Data Model

## 📊 Project Overview

This project demonstrates the end-to-end transformation of a fragmented, operational transactional database into a highly optimized, analytical Star Schema using Power BI and Power Query. The goal was to create a scalable, performant model to enable cross-functional reporting across Sales, Finance, Inventory, and Marketing departments.

## 🛠 Tools & Technologies

* Data Modeling & Visualization: Power BI  
* ETL / Data Preparation: Power Query (M)  
* Analytics Engine: DAX (Data Analysis Expressions)

## 📈 The Business Problem

The source system consisted of isolated operational tables (orders, invoices, payments, shipments, campaigns) with varying granularities. Key issues included:

1. Fragmented Data: Orders were split across years (ORDERS\_2025, ORDERS\_2026), and customer data was isolated from credit limits and contacts.  
2. Poor Query Performance: Flat, single-table structures caused slow rendering in BI visuals.  
3. Complex Reporting: Calculating metrics like "Days to Pay," "Delivery vs. Ship Time," or "Campaign ROI" required complex, multi-table joins that were inefficient for business users.  
4. Data Quality: Presence of test data, retired products, and missing exchange rates that needed handling.

## 🏗 The Solution: Before & After Data Modeling

### Before (Operational Schema)

The initial structure was a complex web of operational tables. While normalized for data entry, it was highly inefficient for BI querying.Before Data Modeling

### After (Star Schema)

I redesigned the model into a standard Star Schema. This involved:

* Fact Tables: Aggregating transactional data into fact\_sales, fact\_campaign\_spend, fact\_inventory, etc., at the correct grain (grain \= one row per order line item).  
* Dimension Tables: Conformed dimensions like dim\_customer, dim\_product, dim\_date, and dim\_geo were created to slice the facts.  
* Role-Playing Dimensions: Handled multiple dates in the same fact table (e.g., Order Date, Ship Date, Payment Date) using role-playing dimensions via DAX USERELATIONSHIP.  
* Row-Level Security (RLS): Implemented dynamic RLS using the security table to ensure regional managers only see data relevant to their territory.

After Data Modeling

## 🔑 Key Data Transformations (Power Query / M)

* Append & Combine: Appended ORDERS\_2025 and ORDERS\_2026 into a single unified fact table.  
* Data Cleansing: Filtered out test accounts (e.g., CustomerID 9999\) and obsolete products (e.g., ProductCode 'ZZZ-000') from the final dataset.  
* Unpivoting: Transformed the inventory table from a wide format (months as columns) into a long format (Date, ProductName, InventoryLevel) to fit a standard fact table structure.  
* Bridge Tables: Created a bridge table to handle the many-to-many relationship between campaign\_skus (multiple SKUs per campaign) and the product dimension.

## 🧮 Featured DAX Measures

Here are a few examples of the business logic implemented using DAX:

\# Calculate Revenue, respecting exchange ratesTotal Revenue (USD) \= SUMX(    fact\_sales,    fact\_sales\[LineTotal\] \* RELATED(exchange\_rates\[Rate\]))\# Days to Deliver (Shipping efficiency)Avg Days to Deliver \= AVERAGEX(    fact\_sales,    DATEDIFF(fact\_sales\[ShipDate\], fact\_sales\[DeliveryDate\], DAY))\# Campaign ROI (Marketing Efficiency)Campaign ROI \= DIVIDE(    \[Total Revenue from Campaign SKUs\],    SUM(fact\_campaign\_spend\[Spend\]))

## 📂 Repository Structure

* /data: Contains the raw dataset.xlsx file used for the project.  
* /images: Contains the before/after data model screenshots.  
* /pbix: Contains the final Power BI file (.pbix).  
* /docs: Data dictionary detailing table grains and relationships.

## 💡 Business Impact

* Performance: Reduced report rendering time by \~60% by moving from a flat model to a Star Schema.  
* Scalability: New data sources (e.g., 2027 orders) can be appended to the fact tables without breaking existing DAX measures or visuals.  
* Self-Service BI: Empowered business users to easily drag-and-drop fields without needing to understand complex underlying SQL joins.

