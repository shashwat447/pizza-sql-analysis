# Pizza SQL & Excel Analysis

![pizza cover](https://user-images.githubusercontent.com/116041695/234172419-28b8f5ed-425d-477f-9586-44c2a86d456e.jpg)

## Table of Content

* [Data Summary](#data-summary)
* [ER Diagram](#er-diagram)
* [Queries and Insights](#queries-and-insights)
* [Excel Dashboard](#excel-dashboard)

## Data Summary

The dataset is found on [Maven Analytics](https://www.mavenanalytics.io/data-playground) under the name Pizza Place Sales with a total of 4 tables with 48,620 records and 12 fields in total.

- Order Details

  The table has the order_details_id which is the primary key of the table along with the order_id, pizza_id as the foreign key of the orders and pizzas table and last, we have the quantity column of each type of pizza.

- Orders

  This table include the order_id which is the primary key, it also has the date and time of each order.

- Pizza Types

  We have the pizza_type_id as the primary key, along with the name, category and ingredients of each pizza.

- Pizzas

  The Pizzas table has the pizza_id as primary key, pizza_type_id as the foreign key from the pizza types table, it also include the size and price of the pizzas.

## ER Diagram

![Pizza ER Diagram](https://user-images.githubusercontent.com/116041695/234453942-3df6eb5c-52cb-4386-a385-bec29cd8e060.png)

## Queries and Insights

### 1. What is the average number of customers per day?
```
SELECT count(order_id)/count(distinct date) as average_customers_per_day
FROM pizza.dbo.orders
```

The average daily customer traffic is 59.

### 2. How many pizzas are typically in an order?
```
SELECT count(order_details_id)/count(distinct order_id) as avg_no_of_pizzas_per_order
FROM pizza.dbo.order_details;
```
On average, customers order 2 pizzas per order.

### 3. Do we have any bestsellers?
```
SELECT 
  SUM(quantity) AS total_quantity, 
  p.pizza_id, 
  CONCAT(pt.name, ' ', p.size) AS pizza_name_size
FROM pizza.dbo.order_details od 
JOIN pizza.dbo.pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza.dbo.pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY p.pizza_id, pt.name, p.size
ORDER BY total_quantity DESC;
```
Based on sales, the three most popular pizzas are The Big Meat Pizza (in size small), The Thai Chicken Pizza (in size large), and The Five Cheese Pizza (in size large).

### 4. Are there any peak days?
```
SELECT count(order_id) as orders, weekday
FROM pizza.dbo.orders
GROUP BY weekday 
ORDER BY orders DESC;
```
Friday, Saturday, and Thursday are the days with the highest customer traffic, or peak days.

### 5. Which month experienced the highest number of orders?
```
SELECT count(order_id) as orders, month
FROM pizza.dbo.orders
GROUP BY month 
ORDER BY orders DESC;
```
July had the highest number of orders.

### 6. How many pizzas are we making during peak hour?
```
SELECT DATEPART(HOUR, time) AS hours, count(distinct(o.order_id)) as total_orders, 
  count(quantity) as total_quantity 
FROM pizza.dbo.orders o join pizza.dbo.order_details od on o.order_id = od.order_id
GROUP BY DATEPART(HOUR, time) 
ORDER BY total_orders DESC;
```
At 12 PM, the restaurant received 2520 orders, which consisted of 6543 pizzas in total.

### 7. Which pizza generated the most revenue?
```
SELECT p.price * COUNT(quantity) AS revenue, CONCAT(pt.name, ' ', p.size) AS pizza_name_size
FROM pizza.dbo.order_details od 
JOIN pizza.dbo.pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza.dbo.pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY p.pizza_id, p.price, CONCAT(pt.name, ' ', p.size)
ORDER BY revenue DESC;
```
The Thai Chicken Pizza in size large is the top revenue-generating pizza, with a total revenue of $28,323.75.

### 8. What is the total revenue from 2015?
```
SELECT ROUND(SUM(p.price * od.quantity),2) AS total_revenue
FROM  pizza.dbo.pizzas p
JOIN pizza.dbo.order_details od
ON p.pizza_id = od.pizza_id;
```
Total revenue for the year 2015 is $817860.05.

## Excel Dashboard
![Pizza Dashboard](https://user-images.githubusercontent.com/116041695/235843398-097bdeb8-22f9-4e8d-8a60-882037f5f182.png)
