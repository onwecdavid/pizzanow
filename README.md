Building a Retail SQL Database for a Pizza Business: A Step-by-Step Guide

The world of data management in retail is constantly evolving, and for a pizza business, having a robust and efficient database is essential. This article walks you through the process of designing a SQL database tailored for a pizza business, focusing on orders, stock control, and staff management. This setup helps a pizza business owner capture, store, and analyze critical information, ultimately improving performance monitoring and business insights.

Key Focus Areas
Our retail pizza database design covers three main areas:

Orders: Capture customer orders, including pizza details, customer information, and delivery addresses.
Stock Control: Monitor inventory levels, track ingredient use, and alert when stock replenishment is needed.
Staff Management: Track staff schedules, shifts, and associated costs.
Each section below will outline the database tables, relationships, and key queries needed for creating insightful dashboards.

Steps to Build the Database
To create a functional SQL database, here are the foundational steps taken:

Identify Required Data Fields: Outline all data fields relevant to the business.
Normalize the Data: Eliminate redundancies by structuring data into relational tables.
Create Related Tables: Define tables based on relationships (e.g., orders, customers, items).
Define Table Relationships: Set up primary and foreign keys to establish connections.
Database Structure and Table Design
Orders Table
The Orders table is designed to capture essential details for each customer order. Initially, the fields include:

Item Name
Item Price
Quantity
Customer Name
Delivery Address
After further development, additional fields were added to ensure clarity and data separation. Here’s the final table structure:

Field	Type	Description
row_id	int (PK)	Unique row identifier
order_id	varchar(10)	Unique order identifier
created_at	datetime	Order creation timestamp
item_name	varchar(50)	Name of the ordered item
item_cat	varchar(50)	Category of the item (e.g., Pizza)
item_size	varchar(20)	Size of the item
item_price	decimal(5,2)	Price of the item
quantity	int	Quantity ordered
delivery	boolean	Delivery status (true/false)
cust_id	int (FK)	Customer ID
add_id	int (FK)	Address ID
item_id	int (FK)	Item ID
Additional Tables
Customer Table:

Stores basic customer details, linked to orders by cust_id.
Address Table:

Breaks down delivery address fields for normalization.
Item Table:

Contains information on each item, such as category, size, and price.
Ingredient, Recipe, and Inventory Tables:

Supports stock control by tracking ingredient usage and stock levels for each item.
Staff, Shift, and Rota Tables:

Manages staff schedules, shift information, and cost calculations.
SQL Queries and Dashboard Creation
To monitor key business metrics, we designed three primary dashboards:

Dashboard 1: Order Activity
The Order Activity Dashboard helps visualize order patterns and sales performance. Essential metrics include:

Total Orders
Total Sales
Sales by Category
Top-Selling Items
Orders by Delivery/Pick-Up
Here’s a sample query used for this dashboard:

sql
Copy code
SELECT
    o.order_id,
    i.item_price,
    o.quantity,
    i.item_cat,
    i.item_name,
    o.created_at,
    a.delivery_address1,
    a.delivery_address2,
    a.delivery_city,
    a.delivery_zipcode,
    o.delivery
FROM 
    orders o
LEFT JOIN item i ON o.item_id = i.item_id
LEFT JOIN address a ON o.add_id = a.add_id;
This query provides order details, allowing the dashboard to analyze patterns and trends.

Dashboard 2: Inventory Management
This dashboard is designed to track ingredient inventory and alert on low-stock items. Key metrics include:

Total Quantity by Ingredient
Cost of Ingredients
Stock Remaining by Ingredient
We created a view (stock1) to simplify complex calculations for ingredient tracking:

sql
Copy code
SELECT  
    s1.item_name,
    s1.ing_id,
    s1.ing_name,
    s1.ing_weight,
    s1.ing_price,
    s1.order_quantity,
    s1.recipe_quantity,
    s1.order_quantity * recipe_quantity AS ordered_weight,
    s1.ing_price / s1.ing_weight AS unit_cost,
    (s1.order_quantity * s1.recipe_quantity) * (s1.ing_price / s1.ing_weight) AS ingredient_cost
FROM (SELECT
    o.item_id,
    i.sku,
    i.item_name,
    r.ing_id,
    ing.ing_name,
    r.quantity AS recipe_quantity,
    SUM(o.quantity) AS order_quantity,
    ing.ing_weight,
    ing.ing_price
FROM 
    orders o
LEFT JOIN item i ON o.item_id = i.item_id
LEFT JOIN recipe r ON i.sku = r.recipe_id
LEFT JOIN ingredient ing ON ing.ing_id = r.ing_id
GROUP BY 
    o.item_id, 
    i.sku , 
    i.item_name,
    r.ing_id,
    r.quantity,
    ing.ing_name,
    ing.ing_weight,
    ing.ing_price) AS s1;
Dashboard 3: Staff Management
This dashboard tracks labor costs and hours worked, allowing the business to manage staff efficiency. Key metrics include:

Staff Cost per Role
Hours in Shift
A query to analyze staff shifts and costs is:

sql
Copy code
SELECT
    ro.date,
    st.first_name,
    st.last_name,
    st.hourly_rate,
    sh.start_time,
    sh.end_time,
    TIME_TO_SEC(TIMEDIFF(sh.end_time, sh.start_time)) / 3600 AS hours_in_shift,
    (TIME_TO_SEC(TIMEDIFF(sh.end_time, sh.start_time)) / 3600) * st.hourly_rate AS staff_cost
FROM 
    rota AS ro
LEFT JOIN staff AS st ON ro.staff_id = st.staff_id
LEFT JOIN shift AS sh ON ro.shift_id = sh.shift_id;
Conclusion
A well-designed SQL database not only provides a systematic approach to data collection but also enhances business insight through analytics dashboards. With this setup, the pizza business can efficiently monitor orders, manage inventory, and optimize staffing—ultimately improving decision-making and business growth.

By building custom queries and dashboards, this SQL design offers a powerful tool for any retail business looking to leverage data for smarter operations.
