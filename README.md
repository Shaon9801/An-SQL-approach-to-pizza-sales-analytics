# An-SQL-approach-to-pizza-sales-analytics
## Project Title:
## `Pizza Sales Analytics:` A SQL Approach to Revenue and Customer Behavior.
## Project Description
This project analyzes pizza sales data using **SQL** to generate insights into revenue, customer behaviour, and sales trends. It aims to help businesses understand sales performance, customer preferences, and operational efficiency.
## Objectives:
- `Revenue Analysis:`Track and analyze total revenue from orders by pizza category, type, and size.
- `Customer Behavior:`Identify purchasing patterns like average order value and multi-pizza orders.
- `Category Performance:` Identify the best-performing pizza categories and sizes.
- `Cumulative Trends: Track revenue growth over time for better business forecasting.`

## Key Features:
- `Revenue Calculation:` Summarizes total revenue across different dimensions like size and category.
- `Order Count by Time:` Analyzes orders based on time of day and day of the week.
- `Average Order Value (AOV):` Calculates the mean value per order.
 - `Sales by Pizza Type:` Tracks the performance of different pizza types by quantity and revenue.
 - `Peak Periods:` Identifies high-sales times like specific hours of the day or week.

   ## SQL Queries Overview:
   - `Revenue Metrics:` Tracks total and category-specific revenue.
   - `Customer Behavior:` Identifies multi-pizza orders to understand customer preferences.
   - `Sales by Time of Day:` Analyzes sales patterns during different times (morning, afternoon, evening).
   - `Top Pizza Types:` Identifies top-selling pizzas based on revenue.
   - `Trend Analysis:` Measures sales growth over time for informed decision-making.
  
   ## Analysis With SQL(MYSQL)
   ### first file
```sql
DESCRIBE ORDER_DETAILS;
DESCRIBE ORDERS;
DESCRIBE pizza_types;
DESCRIBE pizzas;

SELECT * FROM ORDERS;
SELECT * FROM ORDER_DETAILS;
SELECT * FROM PIZZAS;
SELECT * FROM PIZZA_TYPES;
```
