# An-SQL-approach-to-pizza-sales-analytics
## Project Title:
## `Pizza Sales Analytics:` A SQL Approach to Revenue and Customer Behavior.
## Project Description
This project analyzes pizza sales data using **SQL** to generate insights into revenue, customer behaviour, and sales trends. It aims to help businesses understand sales performance, customer preferences, and operational efficiency.
## Objectives:
- `Revenue Analysis:`Track and analyze total revenue from orders by pizza category, type, and size.
- `Customer Behavior: `Identify purchasing patterns like average order value and multi-pizza orders.
- `Category Performance:` Identify the best-performing pizza categories and sizes.
- `Cumulative Trends:` Track revenue growth over time for better business forecasting.

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
  
   ## Performing Analysis with MySQL
  ### Show only the table details.
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
### CS1: Total Revenue from all orders.
```sql
 WITH CTE AS
(SELECT 
    O.PIZZA_ID, O.QUANTITY, P.PRICE, QUANTITY * PRICE AS REVENUE,SIZE
FROM
    ORDER_DETAILS AS O
       LEFT JOIN
   PIZZAS AS P ON P.PIZZA_ID = O.PIZZA_ID)

SELECT 
  SIZE, SUM(QUANTITY)
FROM
CTE
GROUP BY 1
ORDER BY 2 ASC;
```
## 📊 Pizza Size Distribution

| Pizza Size | Total Orders |
|------------|--------------|
| XXL        | 28           |
| XL         | 552          |
| S          | 14,403       |
| M          | 15,635       |
| L          | 18,956       |

### CS2: Mean order amount.
```sql
SELECT ROUND(SUM(od.quantity * p.price) / COUNT(DISTINCT od.order_id), 2) AS avg_order_value
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id;
```
    
### CS3: Show the distribution of pizza sizes based on total revenue:
```sql
WITH CTE AS 
  (SELECT 
    O.PIZZA_ID, O.QUANTITY, P.PRICE, QUANTITY * PRICE AS REVENUE,SIZE 
FROM
    ORDER_DETAILS AS O
        LEFT JOIN
    PIZZAS AS P ON O.PIZZA_ID = P.PIZZA_ID)
  SELECT 
    SIZE, SUM(QUANTITY) AS TOTAL_ORDERS, ROUND(SUM(REVENUE)) AS TOTAL_REVENUE
FROM
    CTE
GROUP BY 1
ORDER BY 3 DESC;
```

### CS4: Find those customers who purchased more than one pizza:
```sql
WITH CTE AS 
(SELECT 
    O.PIZZA_ID, O.QUANTITY, P.PRICE, QUANTITY * PRICE AS REVENUE
FROM
    ORDER_DETAILS AS O
        LEFT JOIN
    PIZZAS AS P ON P.PIZZA_ID = O.PIZZA_ID)
SELECT 
    *
FROM
    CTE
WHERE
    -- PRICE <> REVENUE
    quantity!=1;
```
### CS5: orders on a daily basis:

```sql 
SELECT 	COUNT(DISTINCT DATE) FROM ORDERS;
SELECT 
   MIN(DATE)
FROM ORDERS;
SELECT 
   MAX(DATE)
FROM ORDERS;

SELECT 
   DATE, COUNT(QUANTITY) AS TOTAL_ORDERS
FROM ORDER_DETAILS AS O
LEFT JOIN ORDERS AS R ON O.ORDER_ID=R.ORDER_ID
GROUP BY 1;
```



### CS6: Month-wise revenue: 
```sql
WITH CTE AS (
SELECT 
  monthname(STR_TO_DATE(DATE,'%Y-%m-%d')) AS MONTHNAME,
  STR_TO_DATE(DATE,'%Y-%m-%d') AS MONTHS,
  O.PIZZA_ID, O.QUANTITY, P.PRICE, QUANTITY * PRICE AS REVENUE 
  FROM ORDER_DETAILS AS O 
  LEFT JOIN PIZZAS AS P ON O.PIZZA_ID=P.PIZZA_ID
  LEFT JOIN ORDERS AS R ON O.ORDER_ID=R.ORDER_ID)
SELECT 
   MONTHNAME,MONTH(MONTHS) AS NUM_OF_MONTH , ROUND(SUM(REVENUE),0) AS TOTAL_BILL
FROM CTE
GROUP BY 1,2
ORDER BY 2 DESC;
```

### CS7: Hour-wise bill:
```sql
 WITH CTE AS (
SELECT 
  STR_TO_DATE(TIME,'%H:%i:%s') AS HOURLY_SALES,
  O.PIZZA_ID ,O.QUANTITY, P.PRICE, QUANTITY * PRICE AS REVENUE 
  FROM ORDER_DETAILS AS O 
  LEFT JOIN PIZZAS AS P ON O.PIZZA_ID=P.PIZZA_ID
  LEFT JOIN ORDERS AS R ON O.ORDER_ID=R.ORDER_ID)
SELECT 
   HOUR(HOURLY_SALES) AS NUM_OF_HOURS , ROUND(SUM(REVENUE),0) AS TOTAL_BILL
FROM CTE
GROUP BY 1
ORDER BY 2 DESC;
```


### CS8: Orders by time of day (morning, afternoon, evening):
```sql
WITH CTE AS ( 
 SELECT 
     HOUR(STR_TO_DATE(TIME,'%H:%i:%S')) AS DAY_TIME ,
     ORDER_ID 
     FROM ORDERS)

SELECT 
  CASE 
     WHEN DAY_TIME BETWEEN 0 AND 11 THEN 'MORNING'
     WHEN DAY_TIME BETWEEN 12 AND 17 THEN 'AFTERNOON'
     WHEN DAY_TIME BETWEEN 18 AND 23 THEN 'EVENING'
END AS DAY, COUNT(ORDER_ID) AS TOTAL_ORDERS FROM CTE 
GROUP BY DAY
ORDER BY 2 DESC;
```

### CS9: Sales quantities of pizzas by category:
```sql
SELECT pt.category, SUM(od.quantity) AS total_sold
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.category
ORDER BY total_sold DESC;
```

### CS10: Revenue by day of the week:
```sql
SELECT 
      dayofweek(o.date) as num_day_of_week,
      DAYNAME(o.date) AS day_of_week, 
       ROUND(SUM(od.quantity * p.price), 2) AS revenue
FROM orders o
JOIN order_details od ON o.order_id = od.order_id
JOIN pizzas p ON od.pizza_id = p.pizza_id
GROUP BY 1,2
ORDER BY revenue DESC;
```


### CS11:TOP 10 ORDERS:
```sql
WITH CTE AS ( 
   SELECT DATE ,COUNT(ORDER_ID) AS DAILY_SALES, 
   RANK() OVER (ORDER BY COUNT(ORDER_ID) DESC ) AS RK
   FROM 
   ORDERS
   GROUP BY 1)
SELECT * FROM CTE 
WHERE RK <=10;
```
### CS12: Daily count of quantity for December:
```sql
WITH CTE AS 
  ( SELECT
     month(STR_TO_DATE(DATE,'%Y-%m-%d')) AS DAYS ,DATE ,O.QUANTITY
     FROM ORDER_DETAILS AS O
     LEFT JOIN ORDERS AS R ON O.ORDER_ID=R.ORDER_ID)
SELECT * FROM CTE
WHERE DAYS=12;
```


### CS13: Total number of orders in December:
```sql
  WITH CTE AS 
  ( SELECT
     month(STR_TO_DATE(DATE,'%Y-%m-%d')) AS DAYS ,DATE ,O.QUANTITY
     FROM ORDER_DETAILS AS O
     LEFT JOIN ORDERS AS R ON O.ORDER_ID=R.ORDER_ID)
SELECT COUNT(QUANTITY) AS TOTAL_ORDER FROM CTE
WHERE  DAYS=12;
```

   
### CS14: Category-wise count:
```sql
SELECT 
   PT.CATEGORY, SUM(QUANTITY) AS TOTAL_QUANTITY_ORDERED
FROM
   PIZZA_TYPES AS PT
       LEFT JOIN
   PIZZAS AS P ON PT.PIZZA_TYPE_ID = P.PIZZA_TYPE_ID
       LEFT JOIN
   order_details AS OD ON P.PIZZA_ID = OD.PIZZA_ID
GROUP BY 1
ORDER BY 2 DESC;
```

### CS15: Examine cumulative revenue trends over time:

```sql
 WITH  CTE AS 
 (SELECT 
  R.DATE,
  ROUND(SUM(O.QUANTITY * P.PRICE),0) AS REV
FROM ORDER_DETAILS AS O
LEFT JOIN PIZZAS AS P ON O.PIZZA_ID = P.PIZZA_ID
LEFT JOIN ORDERS AS R ON O.ORDER_ID = R.ORDER_ID
GROUP BY 1
)
SELECT 
DATE,REV,
SUM(REV) OVER(ORDER BY DATE) AS CUMULATIVE_REV
FROM CTE
GROUP BY 1;
```


### CS16: Determine the top three pizza types by revenue within each category:
```sql
WITH RNK AS (
SELECT 
   PT.CATEGORY,
   PT.NAME,
   ROUND(SUM(P.PRICE * OD.QUANTITY), 2) AS REVENUE
FROM
   PIZZA_TYPES AS PT
       LEFT JOIN
   PIZZAS AS P ON PT.PIZZA_TYPE_ID = P.PIZZA_TYPE_ID
       LEFT JOIN
   order_details AS OD ON P.PIZZA_ID = OD.PIZZA_ID
GROUP BY 1 , 2),
RNK_CTE AS( 
SELECT 
CATEGORY ,NAME ,REVENUE,
RANK() OVER(PARTITION BY CATEGORY ORDER BY REVENUE) AS RK
FROM RNK )
SELECT *FROM RNK_CTE
WHERE RK<=3;
```
