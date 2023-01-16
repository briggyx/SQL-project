--Before cleaning data, the tables need to be created and filled with data

CREATE TABLE all_sessions (
	full_visitor_id VARCHAR, 
	channel_grouping TEXT,
	elapsed_time INT, 
	country TEXT,
	city TEXT,
	time_on_site INT,
	page_views INT,
	session_quality_dim INT,
	purchase_date DATE,
	visit_id VARCHAR,
	website_type TEXT,
	product_refund_amount FLOAT,
	product_quantity INT,
	product_price FLOAT,
	product_revenue FLOAT,
	product_sku VARCHAR, 
	v2_product_name VARCHAR,
	v2_product_category TEXT,
	product_variant TEXT,
	currency_code TEXT,
	item_quantity INT,
	item_revenue FLOAT,
	transaction_revenue FLOAT,
	transaction_id VARCHAR,
	page_title TEXT,
	search_keyword TEXT,
	page_path_level1 VARCHAR,
	ecommerce_action_type INT,
	ecommerce_action_step INT,
	ecommerce_action_option TEXT		
	);
	
-- command for PSQL due to permission being denied: 'could not open file "/Users/brigitte/desktop/all_sessions.csv" for reading: Permission denied'
-- even after permissions for read & write were granted for everyone for the file, the same error persisted
-- for the PSQL tool, the command is condensed to a single line instead of on multiple lines like in the query tool 
\copy all_sessions (full_visitor_id,channel_grouping,elapsed_time,country,city,total_transactions_revenue,transactions,time_on_site,page_views,session_quality_dim,purchase_date,visit_id,website_type,product_refund_amount,product_quantity,product_price,product_revenue,product_sku,v2_product_name,v2_product_category,product_variant,currency_code,item_quantity,item_revenue,transaction_revenue,transaction_id,page_title,search_keyword,page_path_level1,ecommerce_action_type,ecommerce_action_step,ecommerce_action_option) FROM '/Users/brigitte/desktop/all_sessions.csv' DELIMITER ',' CSV HEADER;


CREATE TABLE analytics(
	visit_number SMALLINT, 
	visit_id VARCHAR,  
	visit_start_time INT, 
	purchase_date DATE, 
	full_visitor_id VARCHAR,
	user_id VARCHAR,
	channel_grouping TEXT,
	social_engagement_type TEXT,
	units_sold SMALLINT,
	page_views SMALLINT,
	time_on_site SMALLINT,
	bounces SMALLINT,
	revenue FLOAT,  
	unit_price FLOAT
	);

--command for PSQL Tool, due to permissions issue
\COPY analytics (visit_number,visit_id,visit_start_time,purchase_date,full_visitor_id,user_id,channel_grouping,social_engagement_type,units_sold,page_views,time_on_site,bounces,revenue,unit_price) FROM '/Users/brigitte/desktop/analytics.csv' DELIMITER ',' CSV HEADER;


CREATE TABLE products(
	product_sku VARCHAR,
	product_name VARCHAR,
	ordered_quantity SMALLINT, 
	stock_level SMALLINT,
	restocking_lead_time SMALLINT,
	sentiment_score FLOAT,
	sentiment_magnitude FLOAT
	);
	
\COPY products (product_sku,product_name,ordered_quantity,stock_level,restocking_lead_time,sentiment_score,sentiment_magnitude) FROM '/Users/brigitte/desktop/products.csv' DELIMITER ',' CSV HEADER;

CREATE TABLE sales_by_sku(
	product_sku VARCHAR, 
	total_ordered SMALLINT
	);

\COPY sales_by_sku(product_sku,total_ordered) FROM '/Users/brigitte/desktop/sales_by_sku.csv' DELIMITER ',' CSV HEADER;


CREATE TABLE sales_report(
	product_sku VARCHAR, 
	total_ordered SMALLINT,
	product_name VARCHAR,
	stock_level SMALLINT, 
	restocking_lead_time SMALLINT,
	sentiment_score FLOAT,
	sentiment_magnitude FLOAT,
	ratio FLOAT
	);
	
\COPY sales_report(product_sku,total_ordered,product_name,stock_level,restocking_lead_time,sentiment_score,sentiment_magnitude,ratio) FROM '/Users/brigitte/desktop/sales_report.csv' DELIMITER ',' CSV HEADER;

--Now that the tables are created and filled, I will clean the data



--PRODUCTS TABLE--------------------------------------------------------------------------------------
--Find product_sku that are less or more than 14 characters long
--Then I will standardize all the product_sku to be length 14
--170 rows 7 char long 
SELECT product_sku
FROM products
WHERE 
	LENGTH (product_sku) = 7;

-- 0 8 char long
SELECT product_sku
FROM Products
WHERE 
	LENGTH (product_sku) = 8;

-- 0 9 char long
SELECT product_sku
FROM products
WHERE 
	LENGTH (product_sku) = 9;

-- 0 10 char long
SELECT product_sku
FROM products
WHERE 
	LENGTH (product_sku) = 10;

-- 0 11 char long
SELECT product_sku
FOM products
WHERE 
	LENGTH (product_sku) = 11;

-- 169 12 char long
SELECT product_sku
FROM products
WHERE 
	LENGTH (product_sku) = 12;

-- none 13 char long
SELECT product_sku
FROM products
WHERE 
	LENGTH (product_sku) = 13;

-- 751 14 char long
SELECT product_sku
FROM products
WHERE 
	LENGTH (product_sku) = 14;

-- 1 15 char long
SELECT product_sku
FROM products
WHERE 
	LENGTH (product_sku) = 15;

-- 1 16 char long
SELECT product_sku
FROM products
where 
	LENGTH (product_sku) = 16;

--omit product_sku 14 and 15 char long 
DELETE FROM products
WHERE
	LENGTH (product_sku) = 15 OR 
	LENGTH (product_sku) = 16;
	
-- PAD 12 char product_sku 14 char
UPDATE products
SET product_sku = CONCAT('GG', product_sku)
WHERE 
	LENGTH (product_sku) = 12;
	
-- PAD 7 char product_sku 14 char
UPDATE products
SET product_sku = CONCAT('GGGGGGG', product_sku)
WHERE 
	LENGTH (product_sku) = 7;

-- check that no other product_sku are less or greater than 14 char long
SELECT product_sku
FROM products
WHERE 
	LENGTH (product_sku) != 14;

-- make product_sku the primary key
ALTER TABLE products
ADD PRIMARY KEY (product_sku);

--Trim leading and trailing spaces from product_name 
UPDATE products
SET product_name = TRIM(BOTH FROM product_name);


-------------------------------------------------------------------
----------SALES_BY_SKU TABLE---------------------------------------

--See if there are product_sku no 14 characters in length 
SELECT product_sku
FROM sales_by_sku
WHERE LENGTH (product_sku) != 14
	ORDER BY product_sku DESC;

-- PAD 7 char product_sku 14 char
UPDATE sales_by_sku
SET product_sku = CONCAT('GGGGGGG', product_sku)
WHERE 
	LENGTH (product_sku) = 7;
	
-- PAD 12 char product_sku 14 char
UPDATE sales_by_sku
SET product_sku = CONCAT('GG', product_sku)
WHERE 
	LENGTH (product_sku) = 12;

--Use product_sku as primary key since all its rows are distinct
ALTER TABLE sales_by_sku
ADD PRIMARY KEY (product_sku);



-------------------------------------------------------------------
----------SALES_REPORT TABLE---------------------------------------

--Find product_sku that are less or more than 14 characters long
SELECT product_sku
FROM sales_report
WHERE LENGTH (product_sku) != 14
ORDER BY product_sku DESC;

-- PAD 7 char product_sku 14 char
UPDATE sales_report
SET product_sku = CONCAT('GGGGGGG', product_sku)
WHERE 
	LENGTH (product_sku) = 7;
	
-- PAD 12 char product_sku 14 char
UPDATE sales_report
SET product_sku = CONCAT('GG', product_sku)
WHERE 
	LENGTH (product_sku) = 12;

SELECT product_sku
FROM sales_report
WHERE 
	LENGTH (product_sku) != 14;

ALTER TABLE sales_report
ADD PRIMARY KEY (product_sku);

UPDATE sales_report
SET product_name = TRIM(BOTH FROM product_name);

--I don't find this column to be useful for the problems I need to answer
ALTER TABLE sales_report 
DROP COLUMN ratio;


-------------------------------------------------------------
----------ALL_SESSIONS---------------------------------------

SELECT product_sku
FROM all_sessions
WHERE 
	LENGTH (product_sku) != 14
ORDER BY 
	product_sku ASC;

--remove spaces within product_sku
UPDATE all_sessions
SET product_sku = REPLACE(product_sku, ' ', '');
	
-- PAD 7 char product_sku 14 char
UPDATE all_sessions
SET product_sku = CONCAT('GGGGGGG', product_sku)
WHERE 
	LENGTH (product_sku) = 7;
	
-- PAD 12 char product_sku 14 char
UPDATE all_sessions
SET product_sku = CONCAT('GG', product_sku)
WHERE 
	LENGTH (product_sku) = 12;

-- Drop columns that are empty, mostly empty, or are not useful for answering our research questions
ALTER TABLE all_sessions
DROP COLUMN total_transactions_revenue,
DROP COLUMN	transactions,
DROP COLUMN	session_quality_dim,
DROP COLUMN	product_refund_amount,
DROP COLUMN	product_quantity,
DROP COLUMN	product_revenue,
DROP COLUMN	item_quantity,
DROP COLUMN	item_revenue,
DROP COLUMN	transaction_revenue,
DROP COLUMN	transaction_id,
DROP COLUMN	search_keyword,
DROP COLUMN	page_path_level1,
DROP COLUMN	ecommerce_action_type,
DROP COLUMN	ecommerce_action_step,
DROP COLUMN	ecommerce_action_option;

ALTER TABLE all_sessions
DROP COLUMN v2_product_category,
DROP COLUMN	product_variant,
DROP COLUMN	currency_code;

ALTER TABLE all_sessions
DROP COLUMN	website_type;

ALTER TABLE all_sessions
DROP COLUMN	time_on_site;

ALTER TABLE all_sessions
ADD COLUMN 
ID SERIAL PRIMARY KEY;

UPDATE all_sessions
SET channel_grouping = TRIM(BOTH FROM channel_grouping)

UPDATE all_sessions
SET product_price = TRIM(BOTH FROM product_price);


-------------------------------------------------------------
----------ANALYTICS---------------------------------------

--Drop columns that are empty, mostly empty, or are not useful for
ALTER TABLE analytics
DROP COLUMN channel_grouping,
DROP COLUMN social_engagement_type,
DROP COLUMN page_views,
DROP COLUMN	time_on_site,
DROP COLUMN	bounces,
DROP COLUMN	revenue,
DROP COLUMN	units_sold,
DROP COLUMN user_id;

ALTER TABLE analytics
ADD COLUMN 
ID SERIAL PRIMARY KEY;

-- divide unit_price by 1000000
UPDATE analytics
SET unit_price = unit_price / 1000000;
