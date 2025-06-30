## You have a table of orders. How would you get the top 5 customers with the highest total spend?
### Why it’s asked:
Tests your ability to aggregate and optimize queries.
### Expected Answer:
```php
SELECT customer_id, SUM(total_amount) AS total_spend
FROM orders
GROUP BY customer_id
ORDER BY total_spend DESC
LIMIT 5;
```
