What are your risk areas? Identify and describe them.

-product_sku that are not 14 characters long
-product_sku not in the format 'GG...'
-INNER joins that only include shared columns values between two tables 
-NULL values in columns such as visit_id, full_visitor_id
-product_sku that point to different products on different tables
-missing values for cities
-values that don't make sense, such as negative sentiment score



QA Process:
Describe your QA process and include the SQL queries used to execute it.


--Check that there are no product_sku that are not 15 characters long
SELECT product_sku
FROM products
WHERE 
    LENGTH (product_sku) != 14;

SELECT product_sku
FROM sales_by_sku
WHERE 
    LENGTH (product_sku) != 14;

SELECT product_sku
FROM all_sessions
WHERE 
    LENGTH (product_sku) != 14;

--Join tables all_sessions with sales_report, this was used to answer one of the earlier questions
SELECT x.country, 
    x.product_sku, 
    y.sentiment_score, 
    y.sentiment_magnitude, 
    x.v2_product_name, x.city
FROM all_sessions x
INNER JOIN sales_report y
ON x.product_sku = y.product_sku;

--454 unique product_sku in sales_report
SELECT product_sku 
FROM sales_report;

--278 distinct product_sku in Q3. Since 278 is less than 454, it indicates there are no values in the the joined table that aren't in sales_report.
--This is consistent with expectations of an inner join.
SELECT DISTINCT product_sku
FROM Q3;

--Left join all_sessions and sales_report to get product_sku in all_sessions only
--Then take a random product_sku and see if it's in Q3
SELECT x.product_sku 
FROM all_sessions x
LEFT JOIN sales_report y
ON x.product_sku = y.product_sku
WHERE 
    y.product_sku IS NULL;

--No product_sku is found with a random value that's in all_sessions but not sales_report
--Validates that the INNER JOIN between all_sessions and sales_report worked
SELECT product_sku
FROM Q3
WHERE 
    product_sku = 'GGGGGGG9182788';
    

--Check that there are no null visit_id in the all_sessions and analytics
SELECT visit_id
FROM all_sessions
WHERE  visit_id IS NULL;

SELECT visit_id
FROM analytics
WHERE  visit_id IS NULL;


--get the number of distinct product_sku from join query used in Q8 in starting_with_data
SELECT COUNT(distinct product_sku) FROM
(SELECT x.country, y.stock_level, x.product_sku
FROM all_sessions x
INNER JOIN products y
ON x.product_sku = y.product_sku
WHERE 
    stock_level != 0) tmp;
    
--the number of distinct product_sku in both products and all_sessions is LESS THAN the one in the INNER JOIN table which is consistent with that function 
SELECT DISTINCT product_sku
FROM all_sessions;

SELECT DISTINCT product_sku
FROM products;
