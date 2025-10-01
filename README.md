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
    O.PIZZA_ID, O.QUANTITY, P.PRICE, QUANTITY * PRICE AS REVENUE
FROM
    ORDER_DETAILS AS O
       LEFT JOIN
   PIZZAS AS P ON P.PIZZA_ID = O.PIZZA_ID)
   

SELECT 
 ROUND(SUM(REVENUE),0) AS TOTAL_REVENUE
FROM
CTE;
```
### CS2: 📊 Determine the top three pizza types by revenue within each category:
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
GROUP BY 1, 2),
RNK_CTE AS( 
SELECT 
CATEGORY, NAME, REVENUE,
RANK() OVER(PARTITION BY CATEGORY ORDER BY REVENUE) AS RK
FROM RNK )
SELECT *FROM RNK_CTE
WHERE RK<=3;
```
## Output

| Category | Pizza Name                               | Revenue    | Rank |
|----------|------------------------------------------|-----------|------|
| Chicken  | The Chicken Pesto Pizza                   | 16,701.75 | 1    |
| Chicken  | The Chicken Alfredo Pizza                 | 16,900.25 | 2    |
| Chicken  | The Southwest Chicken Pizza               | 34,705.75 | 3    |
| Classic  | The Pepperoni, Mushroom, and Peppers Pizza | 18,834.50 | 1    |
| Classic  | The Big Meat Pizza                        | 22,968.00 | 2    |
| Classic  | The Napolitana Pizza                      | 24,087.00 | 3    |
| Supreme  | The Brie Carre Pizza                      | 11,588.50 | 1    |
| Supreme  | The Spinach Supreme Pizza                 | 15,277.75 | 2    |
| Supreme  | The Calabrese Pizza                       | 15,934.25 | 3    |
| Veggie   | The Green Garden Pizza                     | 13,955.75 | 1    |
| Veggie   | The Mediterranean Pizza                   | 15,360.50 | 2    |
| Veggie   | The Spinach Pesto Pizza                   | 15,596.00 | 3    |

### CS3: Mean order amount.
```sql
SELECT ROUND(SUM(od.quantity * p.price) / COUNT(DISTINCT od.order_id), 2) AS avg_order_value
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id;
```
    
### CS4: 📊 Show the distribution of pizza sizes based on total revenue:
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
## 📊 Pizza Size Distribution

| Pizza Size | Total Orders | Total Revenue |
|------------|--------------|---------------|
| L          | 18,956       | 375,319       |
| M          | 15,635       | 249,382       |
| S          | 14,403       | 178,076       |
| XL         | 552          | 14,076        |
| XXL        | 28           | 1,007         |


### CS5: 📌 CS4: Customers Who Purchased More Than One Pizza:
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
## Output:

| Pizza Name       | Quantity | Price  | Revenue |
|------------------|----------|--------|---------|
| mediterraneo_m   | 2        | 16.00  | 32.00   |
| spicy_ital_l     | 3        | 20.75  | 62.25   |
| bbq_ckn_l        | 2        | 20.75  | 41.50   |
| the_greek_m      | 2        | 16.00  | 32.00   |
| hawaiian_s       | 2        | 10.50  | 21.00   |
| bbq_ckn_m        | 3        | 16.75  | 50.25   |
| big_meat_s       | 2        | 12.00  | 24.00   |
| ckn_alfredo_l    | 2        | 20.75  | 41.50   |
| cali_ckn_m       | 2        | 16.75  | 33.50   |
| ckn_alfredo_m    | 2        | 16.75  | 33.50   |
| ckn_pesto_l      | 2        | 20.75  | 41.50   |
| ckn_alfredo_m    | 2        | 16.75  | 33.50   |
| four_cheese_l    | 2        | 17.95  | 35.90   |
| pepperoni_m      | 2        | 12.50  | 25.00   |
| big_meat_s       | 2        | 12.00  | 24.00   |
| pepperoni_s      | 2        | 9.75   | 19.50   |

### CS6: orders on a daily basis:

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



### CS7: 📊Month-wise revenue: 
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
## Output:

| Month      | Month Number | Total Revenue |
|------------|--------------|---------------|
| December   | 12           | 64,701        |
| November   | 11           | 70,395        |
| October    | 10           | 64,028        |
| September  | 9            | 64,180        |
| August     | 8            | 68,278        |
| July       | 7            | 72,558        |
| June       | 6            | 68,230        |
| May        | 5            | 71,403        |
| April      | 4            | 68,737        |
| March      | 3            | 70,397        |
| February   | 2            | 65,160        |
| January    | 1            | 69,793        |


### CS8: 📊 Hour-wise Revenue
```sql
 WITH CTE AS (
SELECT 
  STR_TO_DATE(TIME,'%H:%i:%s') AS HOURLY_SALES,
  O.PIZZA_ID, O.QUANTITY, P.PRICE, QUANTITY * PRICE AS REVENUE 
  FROM ORDER_DETAILS AS O 
  LEFT JOIN PIZZAS AS P ON O.PIZZA_ID=P.PIZZA_ID
  LEFT JOIN ORDERS AS R ON O.ORDER_ID=R.ORDER_ID)
SELECT 
   HOUR(HOURLY_SALES) AS NUM_OF_HOURS , ROUND(SUM(REVENUE),0) AS TOTAL_BILL
FROM CTE
GROUP BY 1
ORDER BY 2 DESC;
```
## Output:

| Hour | Total Revenue |
|------|---------------|
| 12   | 111,878       |
| 13   | 106,066       |
| 18   | 89,297        |
| 17   | 86,237        |
| 19   | 72,629        |
| 16   | 70,055        |
| 14   | 59,201        |
| 20   | 58,215        |
| 15   | 52,992        |
| 11   | 44,936        |
| 21   | 42,030        |
| 22   | 22,815        |
| 23   | 1,121         |
| 10   | 304           |
| 9    | 83            |



### CS9:📊 Orders by Time of Day (morning, afternoon, evening):
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
## Outputs:

| Time of Day | Total Orders |
|-------------|--------------|
| Afternoon   | 12,171       |
| Evening     | 7,939        |
| Morning     | 1,240        |


### CS10: 📊 Sales quantities of pizzas by category:
```sql
SELECT pt.category, SUM(od.quantity) AS total_sold
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.category
ORDER BY total_sold DESC;
```
## Output:

| Category  | Total Sold |
|-----------|------------|
| Classic   | 14,888     |
| Supreme   | 11,987     |
| Veggie    | 11,649     |
| Chicken   | 11,050     |


### CS11: 📊 Revenue by Day of the Week:
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
## Outputs

| Day of Week | Total Revenue |
|-------------|---------------|
| Friday      | 136,073.90    |
| Thursday    | 123,528.50    |
| Saturday    | 123,182.40    |
| Wednesday   | 114,408.40    |
| Tuesday     | 114,133.80    |
| Monday      | 107,329.55    |
| Sunday      | 99,203.50     |



### CS12:TOP 10 ORDERS:
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
### CS13: Daily count of quantity for December:
```sql
WITH CTE AS 
  ( SELECT
     month(STR_TO_DATE(DATE,'%Y-%m-%d')) AS DAYS ,DATE ,O.QUANTITY
     FROM ORDER_DETAILS AS O
     LEFT JOIN ORDERS AS R ON O.ORDER_ID=R.ORDER_ID)
SELECT * FROM CTE
WHERE DAYS=12;
```


### CS14: Total number of orders in December:
```sql
  WITH CTE AS 
  ( SELECT
     month(STR_TO_DATE(DATE,'%Y-%m-%d')) AS DAYS ,DATE ,O.QUANTITY
     FROM ORDER_DETAILS AS O
     LEFT JOIN ORDERS AS R ON O.ORDER_ID=R.ORDER_ID)
SELECT COUNT(QUANTITY) AS TOTAL_ORDER FROM CTE
WHERE  DAYS=12;
```

   
### CS15: Category-wise count:
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

### CS16: Examine cumulative revenue trends over time:

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
DATE, REV,
SUM(REV) OVER(ORDER BY DATE) AS CUMULATIVE_REV
FROM CTE
GROUP BY 1;
```


