Project Summary
Datasets Schema
1. Table: order_details
This table contains details about each order, including the unique order details ID, the order ID it belongs to, the pizza ID, and the quantity of each pizza ordered.

Column	Description
order_details_id	Unique identifier for the order detail.
order_id	Identifier for the order.
pizza_id	Identifier for the pizza.
quantity	Quantity of the pizza ordered.
Sample Data:

order_details_id	order_id	pizza_id	quantity
1	1	hawaiian_m	1
2	2	classic_dlx_m	1
3	2	five_cheese_l	1
4	2	ital_supr_l	1
2. Table: order_id
This table captures the order timing details, including the order ID, date, and time of the order.

Column	Description
order_id	Unique identifier for the order.
date	Date when the order was placed.
time	Time when the order was placed.
Sample Data:

order_id	date	time
1	01-01-2015	11:38:36
2	01-01-2015	11:57:40
3	01-01-2015	12:12:28
4	01-01-2015	12:16:31
3. Table: pizzatype_id
This table describes the various types of pizzas available, including the pizza type ID, name, category, and ingredients.

Column	Description
pizza_type_id	Unique identifier for the pizza type.
name	Name of the pizza.
category	Category of the pizza (e.g., Chicken, Classic, Supreme).
ingredients	Ingredients used in the pizza.
Sample Data:

pizza_type_id	name	category	ingredients
bbq_ckn	The Barbecue Chicken Pizza	Chicken	Barbecued Chicken, Red Peppers, Green Peppers, Tomatoes, Red Onions, Barbecue Sauce
cali_ckn	The California Chicken Pizza	Chicken	Chicken, Artichoke, Spinach, Garlic, Jalapeno Peppers, Fontina Cheese, Gouda Cheese
ckn_alfredo	The Chicken Alfredo Pizza	Chicken	Chicken, Red Onions, Red Peppers, Mushrooms, Asiago Cheese, Alfredo Sauce
ckn_pesto	The Chicken Pesto Pizza	Chicken	Chicken, Tomatoes, Red Peppers, Spinach, Garlic, Pesto Sauce
4. Table: pizza_id
This table specifies the pizza sizes and their prices, including the pizza ID, pizza type ID, size, and price.

Column	Description
pizza_id	Unique identifier for the pizza.
pizza_type_id	Identifier linking to the pizza type.
size	Size of the pizza (e.g., S, M, L).
price	Price of the pizza.
Sample Data:

pizza_id	pizza_type_id	size	price
bbq_ckn_s	bbq_ckn	S	12.75
bbq_ckn_m	bbq_ckn	M	16.75
bbq_ckn_l	bbq_ckn	L	20.75
cali_ckn_s	cali_ckn	S	12.75
SQL Queries
Basic Queries
Retrieve the total number of orders placed:

sql
Copy code
SELECT COUNT(*) AS total_orders
FROM order_id;
Calculate the total revenue generated from pizza sales:

sql
Copy code
SELECT SUM(od.quantity * p.price) AS total_revenue
FROM order_details od
JOIN pizza_id p ON od.pizza_id = p.pizza_id;
Identify the highest-priced pizza:

sql
Copy code
SELECT pizza_id, price
FROM pizza_id
ORDER BY price DESC
LIMIT 1;
Identify the most common pizza size ordered:

sql
Copy code
SELECT size, COUNT(*) AS count
FROM order_details od
JOIN pizza_id p ON od.pizza_id = p.pizza_id
GROUP BY size
ORDER BY count DESC
LIMIT 1;
List the top 5 most ordered pizza types along with their quantities:

sql
Copy code
SELECT pt.name, SUM(od.quantity) AS total_quantity
FROM order_details od
JOIN pizza_id p ON od.pizza_id = p.pizza_id
JOIN pizzatype_id pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY total_quantity DESC
LIMIT 5;
Intermediate Queries
Join the necessary tables to find the total quantity of each pizza category ordered:

sql
Copy code
SELECT pt.category, SUM(od.quantity) AS total_quantity
FROM order_details od
JOIN pizza_id p ON od.pizza_id = p.pizza_id
JOIN pizzatype_id pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.category;
Determine the distribution of orders by hour of the day:

sql
Copy code
SELECT HOUR(TIME(time)) AS order_hour, COUNT(*) AS order_count
FROM order_id
GROUP BY order_hour
ORDER BY order_hour;
Join relevant tables to find the category-wise distribution of pizzas:

sql
Copy code
SELECT pt.category, COUNT(od.order_details_id) AS count
FROM order_details od
JOIN pizza_id p ON od.pizza_id = p.pizza_id
JOIN pizzatype_id pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.category;
Group the orders by date and calculate the average number of pizzas ordered per day:

sql
Copy code
SELECT date, AVG(total_quantity) AS avg_pizzas_per_day
FROM (
    SELECT date, SUM(quantity) AS total_quantity
    FROM order_details od
    JOIN order_id o ON od.order_id = o.order_id
    GROUP BY date
) daily_orders
GROUP BY date;
Determine the top 3 most ordered pizza types based on revenue:

sql
Copy code
SELECT pt.name, SUM(od.quantity * p.price) AS total_revenue
FROM order_details od
JOIN pizza_id p ON od.pizza_id = p.pizza_id
JOIN pizzatype_id pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY total_revenue DESC
LIMIT 3;
Advanced Queries
Calculate the percentage contribution of each pizza type to total revenue:

sql
Copy code
SELECT pt.name, 
       SUM(od.quantity * p.price) / (SELECT SUM(quantity * price) FROM order_details JOIN pizza_id ON order_details.pizza_id = pizza_id.pizza_id) * 100 AS revenue_percentage
FROM order_details od
JOIN pizza_id p ON od.pizza_id = p.pizza_id
JOIN pizzatype_id pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name;
Analyze the cumulative revenue generated over time:

sql
Copy code
SELECT date, SUM(daily_revenue) OVER (ORDER BY date) AS cumulative_revenue
FROM (
    SELECT date, SUM(od.quantity * p.price) AS daily_revenue
    FROM order_details od
    JOIN order_id o ON od.order_id = o.order_id
    JOIN pizza_id p ON od.pizza_id = p.pizza_id
    GROUP BY date
) daily_revenue;
Determine the top 3 most ordered pizza types based on revenue for each pizza category:

sql
Copy code
SELECT pt.category, pt.name, SUM(od.quantity * p.price) AS total_revenue
FROM order_details od
JOIN pizza_id p ON od.pizza_id = p.pizza_id
JOIN pizzatype_id pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.category, pt.name
ORDER BY pt.category, total_revenue DESC
LIMIT 3;
Documentation Overview
This document provides a detailed summary of the datasets used, the structure of each table, and the SQL queries executed for analysis. The queries range from basic to advanced levels, covering various aspects of data extraction and
