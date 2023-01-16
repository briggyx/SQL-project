Question 1: What are the highest average sentiment scores and magnitudes of items that people from different countries look at, and what are the countries?

SQL Queries:

---Join all_sessions and sales_report by product_sku to get sentiment_magnitude and sentiment_score for countries
SELECT x.country, 
    x.product_sku,
    y.sentiment_score, 
    y.sentiment_magnitude
FROM all_sessions x
INNER JOIN sales_report y
ON x.product_sku = y.product_sku;

CREATE TABLE Q6 (
    country TEXT,
    product_sku VARCHAR,
    sentiment_score FLOAT,
    sentiment_magnitude FLOAT);

\copy Q6 (country, product_sku,sentiment_score,sentiment_magnitude) FROM '/Users/brigitte/desktop/ecommerce_dataset3/Q6.csv' DELIMITER ',' CSV HEADER;

SELECT *
FROM Q6;

--Order countries by the average sentiment_scores of all the items browsed in those countries, and also show their average sentiment_magnitude
SELECT country, AVG(sentiment_score) AS average_sentiment_score, AVG(sentiment_magnitude) AS average_sentiment_magnitude
FROM Q6
WHERE country != '(not set)'
GROUP BY country
ORDER BY average_sentiment_score DESC;

SELECT country, AVG(sentiment_score) AS average_sentiment_score, AVG(sentiment_magnitude) AS average_sentiment_magnitude
FROM Q6
WHERE country != '(not set)'
GROUP BY country
ORDER BY average_sentiment_magnitude DESC;

Answer: 
The country with the highest average sentiment score of items browsed is Sint Maarten (0.9)
The country with the highest average sentiment magnitude is Malta (1.8)







Question 2: Which country has the highest/lowest average product price of all browsed items?

SQL Queries:

SELECT country, 
    AVG(product_price) AS average_product_price
FROM all_sessions
WHERE 
    country != '(not set)'
GROUP BY country
ORDER BY average_product_price DESC;

SELECT country, 
    AVG(product_price) AS average_product_price
FROM all_sessions
WHERE 
    country != '(not set)'
GROUP BY country
ORDER BY average_product_price ASC;

Answer:
The Maldives has the highest average product price of all browsed items, of 119.99.
Uganda has the lowest average product price of all browsed items, of 1.495.






Question 3: Which country tends to browse items that are the lowest/highest in stock?

SQL Queries:

--Join all_sessions and products in order to get stock_level for items browsed in each country
SELECT x.country, y.stock_level, x.product_sku
FROM all_sessions x
INNER JOIN products y
ON x.product_sku = y.product_sku
WHERE 
    stock_level != 0;

CREATE TABLE Q8 (
    country TEXT,
    stock_level SMALLINT,
    product_sku VARCHAR);

\copy Q8 (country, stock_level, product_sku) FROM '/Users/brigitte/desktop/ecommerce_dataset3/Q8.csv' DELIMITER ',' CSV HEADER;

SELECT *
FROM Q8;

SELECT country, AVG(stock_level) AS average_product_stock_level 
FROM Q8
WHERE 
    country != '(not set)'
GROUP BY country
ORDER BY average_product_stock_level  ASC;


SELECT country, 
    AVG(stock_level) 
    AS average_product_stock_level 
FROM Q8
    WHERE country != '(not set)'
GROUP BY country
ORDER BY average_product_stock_level DESC;

SELECT country, 
    AVG(stock_level) 
    AS average_product_stock_level 
FROM Q8
    WHERE country != '(not set)'
GROUP BY country
ORDER BY average_product_stock_level ASC;

Answer:
Haiti has the lowest average product stock level for all items browsed in the country. Its value is 1.0. Mali has the highest average product stock level for all items browsed in the country. Its value is 4536. 

