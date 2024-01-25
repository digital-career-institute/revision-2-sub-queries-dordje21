# java-DB-sub-queries

1. Return all customers listed in the orders table who placed orders on the most recently recorded day. 

```
SELECT customer_id
FROM orders
WHERE orders.order_date = (SELECT MAX(orders.order_date) FROM orders);
```

2. Select all product names and prices that have unit price which is bigger than price of product with name 'Carnarvon Tigers'

```
SELECT products.product_name, products.unit_price
FROM products
WHERE products.unit_price > (SELECT products.unit_price FROM products WHERE products.product_name = 'Carnarvon Tigers');
```

3. Select customer ids and ther contact names that made order that was shipped to Brazil

```
SELECT customers.customer_id, customers.contact_name
FROM customers
WHERE customers.customer_id IN (
    SELECT DISTINCT customers.customer_id
    FROM orders
    WHERE orders.ship_country = 'Brazil'
);
```

4. Select customer ids who ordered more than 20 items of product with name = 'Tofu' on a single order.

```
SELECT
  o.customer_id,
  p.product_name,
  od.quantity
FROM
  orders o 
LEFT JOIN
  order_details od ON od.order_id = o.order_id
LEFT JOIN
  products p ON od.product_id = p.product_id
WHERE
  quantity > 20
AND
  product_name = 'Tofu';
```

5. Select only discounted products names, dicount value, category of it, and shipped date of order where this product occurs. Use join + sub query structure.

```
SELECT
  p.product_name,
 (SELECT category_name FROM categories WHERE p.category_id = category_id) AS category_name,
  od.discount,
  o.shipped_date
FROM 
  products p
LEFT JOIN 
  order_details od ON p.product_id = p.product_id
LEFT JOIN 
  orders o ON od.order_id = o.order_id
WHERE
  od.discount > 0
AND
  shipped_date IS NOT NULL
ORDER BY
  od.discount;
```

6. Select orders for customers that come from London and are shipped via United Package. Remember to use sub queries (not joins). 

```
SELECT DISTINCT
  (o.order_id),
  (SELECT city FROM customers WHERE customer_id = o.customer_id) AS customer_city,
  (SELECT company_name FROM shippers WHERE o.ship_via = shippers.shipper_id) AS ship_via
FROM
  orders o 
WHERE
  (SELECT c.city FROM customers c WHERE customer_id = o.customer_id) = 'London'
AND
  (SELECT company_name FROM shippers WHERE o.ship_via = shippers.shipper_id) = 'United Package'
ORDER BY
  order_id;
```
