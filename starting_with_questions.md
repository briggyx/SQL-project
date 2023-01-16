Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:

--All_sessions contains country, city, and product_price
--In hindsight, I shouldn't have dropped the column 'total_transactions_revenue' since it indicates a purchase was actually made
--I made the wrong assumption that every row of data reflected a purchase that was made, where in actuality I think a row is generated when someone interacts with or browses the site
--Since the column tota_transactions_revenue- was mostly empty, I thought it was irrelevant, but it's like that because most interactions with the website don't involve purchases, most are just browsing
--The following queries are made on the assumption that each row is a purchase, and that product_price is the amount that was paid


--Group by country, get the sum of product_price for each country, and order the list of countries with their sum of product_prices with highest values on top
SELECT country, SUM(product_price) AS total_transaction_revenue
FROM all_sessions
WHERE 
	product_price != 0 
GROUP BY country
ORDER BY total_transaction_revenue DESC;

--Group by city, get the sum of product_price for each city, and order the list of cities with their sum of product_prices with highest values on top
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
--Since I made the wrong assumption that product_price is the amount paid for a purchase and that each row is a purchase, this question was awkward to approach
--Here, I simply grouped by city or country, and summed the number of products_sku, omitting where product_price is 0 since that's where the object was out of stock
--This returned the total number of items that were browsed that weren't out of stock, grouped by city or country

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






**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**

--I wanted to use data from both all_sessions which contains city and country, and sales_report which contains product name and sentiment scores 
--I wanted to see the most common product type from v2_product_name, and what the sentiment score and magnitude for that item was 

-- I don't want to have more country, city than there is data available for their v2_product_name, so I did an inner join
SELECT x.country , x.product_sku , y.sentiment_score , y.sentiment_magnitude, x.v2_product_name, x.city
FROM all_sessions x
INNER JOIN sales_report y
ON x.product_sku = y.product_sku

--I exported the table as a .csv, and brought it back into the database as Q3
CREATE TABLE Q3 (
	country TEXT,
	product_sku VARCHAR,
	sentiment_score FLOAT,
	sentiment_magnitude FLOAT,
	v2_product_name VARCHAR,
	city TEXT);

\copy Q3(country, product_sku,sentiment_score,sentiment_magnitude,v2_product_name,city) FROM '/Users/brigitte/desktop/ecommerce_dataset3/Q3.csv' DELIMITER ',' CSV HEADER;

--Count the instances of each item browsed in each country, group by country, and order the most browsed items in descending order to see which are the most popular
--Also show sentiment data to see if there's a pattern there
SELECT 
    country, 
    v2_product_name, 
    COUNT(v2_product_name), 
	sentiment_score, 
	sentiment_magnitude
FROM 
    Q3
WHERE
	country != 'not available in demo dataset' AND country!= '(not set)'
GROUP BY 
    country,
	v2_product_name,
	sentiment_score, 
	sentiment_magnitude
ORDER BY 
    country, COUNT(v2_product_name)
    DESC;



--Do the same for cities, to see the most popular items for each city along with ssentiment data
SELECT 
    city, 
    v2_product_name, 
    COUNT(v2_product_name), 
	sentiment_score, 
	sentiment_magnitude
FROM 
    Q3
WHERE
	city != 'not available in demo dataset' AND city!= '(not set)'
GROUP BY 
    city, 
	v2_product_name, 
	sentiment_score, 
	sentiment_magnitude
ORDER BY 
    city, COUNT(v2_product_name)
    DESC;


Answer: 

I am unsure if there is a pattern based on my query and assumptions about the dataset.



**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


--Count the number of times browsed for each item, grouped by country, and return a list of countries with the name of their most browsed item 
SELECT country, v2_product_name
FROM (SELECT country, v2_product_name, COUNT(*) AS cnt,
             ROW_NUMBER() OVER (PARTITION BY country ORDER BY COUNT(*) DESC) AS seqnum
      FROM all_sessions
      GROUP BY country, v2_product_name) ic
WHERE seqnum = 1 AND country != '(not set)';

--Count the number of times browsed for each item, grouped by city, and return a list of cities with the name of their most browsed item 
SELECT city, v2_product_name
FROM (SELECT city, v2_product_name, COUNT(*) AS cnt,
             ROW_NUMBER() OVER (PARTITION BY city ORDER BY COUNT(*) DESC) AS seqnum
      FROM all_sessions
      GROUP BY city, v2_product_name) ic
WHERE seqnum = 1 AND city != '(not set)';

**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

--Sum the total product_price, grouped by country
--Since I had wrong assumptions about the dataset, what this query actually returned was the sum of product prices of all items browsed in a city/country and not sum of actual transactions
SELECT SUM(product_price) as total_revenue, 
	country
FROM all_sessions 
GROUP BY country
ORDER BY total_revenue DESC;

SELECT SUM(product_price) as total_revenue, 
	city
FROM all_sessions 
WHERE city != '(not set)' AND city !=  'not available in demo dataset'
GROUP BY city
ORDER BY total_revenue DESC;







