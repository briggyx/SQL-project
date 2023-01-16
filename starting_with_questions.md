Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
SELECT country, SUM(product_price) AS total_transaction_revenue
FROM all_sessions
WHERE 
	product_price != 0 
GROUP BY country
ORDER BY total_transaction_revenue DESC;


SELECT city, SUM(product_price) AS total_transaction_revenue
FROM all_sessions
WHERE
	product_price != 0 
	AND city != 'not available in demo dataset' 
	AND city != '(not set)'
GROUP BY city
ORDER BY total_transaction_revenue DESC;


Answer: 
The U.S. has the highest level of transaction revenues on the site. The city of Mountain View has the highest level of transactions on the site.





**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
SELECT city, COUNT(product_sku) AS products_ordered
FROM all_sessions
WHERE
	product_price != 0 AND city != 'not available in demo dataset' AND city != '(not set)'
GROUP BY city
ORDER BY products_ordered DESC;

SELECT country, COUNT(product_sku) AS products_ordered
FROM all_sessions
WHERE
	product_price != 0 AND country != 'not available in demo dataset' AND country != '(not set)'
GROUP BY country
ORDER BY products_ordered DESC;

Answer:
The U.S. has the highest number of order, 8498.
Mountain View has the highest number of orders, 1149.





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**

SELECT x.country , x.product_sku , y.sentiment_score , y.sentiment_magnitude, x.v2_product_name, x.city
FROM all_sessions x
INNER JOIN sales_report y
ON x.product_sku = y.product_sku

CREATE TABLE Q3 (
	country TEXT,
	product_sku VARCHAR,
	sentiment_score FLOAT,
	sentiment_magnitude FLOAT,
	v2_product_name VARCHAR,
	city TEXT);

\copy Q3(country, product_sku,sentiment_score,sentiment_magnitude,v2_product_name,city) FROM '/Users/brigitte/desktop/ecommerce_dataset3/Q3.csv' DELIMITER ',' CSV HEADER;


SELECT 
    country, 
    v2_product_name, 
    COUNT(v2_product_name), -- window function??
	sentiment_score, 
	sentiment_magnitude
FROM 
    Q3
WHERE
	country != 'not available in demo dataset' AND country!= '(not set)'
GROUP BY 
    country, v2_product_name, sentiment_score, 
	sentiment_magnitude
ORDER BY 
    country, COUNT(v2_product_name)
    DESC;


Answer: The items bought in countries match the climate and popular activities. e.g. In Argentina, popular items are backpacks, In Australia, waterproof backpacks are popular, and in Switzerland, "Google Women's Lightweight Microfleece Jacket."




**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:

SELECT country, v2_product_name
FROM (SELECT country, v2_product_name, COUNT(*) AS cnt,
             ROW_NUMBER() OVER (PARTITION BY country ORDER BY COUNT(*) DESC) AS seqnum
      FROM all_sessions
      GROUP BY country, v2_product_name
     ) ic
WHERE seqnum = 1 AND country != '(not set)';


SELECT city, v2_product_name
FROM (SELECT city, v2_product_name, COUNT(*) AS cnt,
             ROW_NUMBER() OVER (PARTITION BY city ORDER BY COUNT(*) DESC) AS seqnum
      FROM all_sessions
      GROUP BY city, v2_product_name
     ) ic
WHERE seqnum = 1 AND city != '(not set)';

Answer: 
Please see the tables created by the queries above.





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

SELECT SUM(product_price) as total_revenue, 
	country
FROM all_sessions 
GROUP BY country
ORDER BY total_revenue DESC;

Answer:
The country with the highest revenue is the U.S., with 280332.






