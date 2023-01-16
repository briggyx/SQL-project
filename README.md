# SQL-project
Lighthouse Labs project 1

## Project/Goals

Use a database called 'ecommerce' to combine and practice implementing what we have learned about databases, POSTGRESQL and PGAdmin:

-Extracting data from a SQL database
-Cleaning, transforming and analyzing data
-Loading data into a database
-Developing and implementing a QA process to validate transformed data against raw data

There are 5 tables in the dataset, I will link them using primary keys, and then try to find patterns using data from different tables by joining them.
I will also use groupings and window functions to look for patterns.



## Process

Before cleaning data, the tables need to be created and filled with data. I used the commands CREATE TABLE and \copy in the PSQL tool to create tables and populate them. 

In cleaning the data, I first found product_sku that are less or more than 14 characters long. Then I standardized all the product_sku to be length 14, and starting with G. Then I designated primary keys to the five tables, and they were either an integer series, or product_sku. After, I generated the ERD for the database, indicated how the tables are linked with primary and foreign keys.

I also trimmed leading and trailing spaces in text, and dropped columns that were mostly empty or weren't useful for answering the given questions.
Here, I made the mistake of dropping transaction_revenue, thinking that each row indicated a purchase with product_price as the amount paid.
In actuality, I think that most of the rows of ata are not transactions, and are just from viewers browsing the site. However, when someone makes a purchase, 
the amount paid is recorded under transaction_revenue.

I then used queries to answer 8 questions about the dataset.

Quality Assessment:

Possible risk areas include:
    -product_sku that are not 14 characters long
    -product_sku not in the format 'GG...'
    -INNER joins that only include shared columns values between two tables 
    -NULL values in columns such as visit_id, full_visitor_id
    -product_sku that point to different products on different tables
    -missing values for cities
    -values that don't make sense, such as negative sentiment score

Actions to validate data:
-Check that there are no product_sku that are not 15 characters long.
-Make sure inner joins were done properly, by checking with left join.
-Check that there are no null visit_id in the all_sessions and analytics.
-Get the number of distinct product_sku from join query used in Q8 in starting_with_data.


## Results

The results and resulting tables are shown in starting_with_data and starting_with_questions. There are 8 questions in total, with the queries done to find answers.


## Challenges 

I made the wrong assumption that every row of data reflected a purchase that was made, where in actuality I think a row is generated when someone interacts with or browses the site.
Since the column tota_transactions_revenue- was mostly empty, I thought it was irrelevant, but it's like that because most interactions with the website don't involve purchases, most are just browsing.
It was difficult understanding the meaning of columns and what the values represent. 
There were less distinct full_visitor_id than visitor_id, and I was unsure why.
Lots of values for city were missing, and I didn't know how to efficiently fill them in based on country.
Overall difficulty in understanding how the tables are linked. I would've liked to know more context around the dataset, like where it's from, how it was gathered.



## Future Goals

I would bring back important columns that I deleted, so I can have more accurate results.
Better data cleaning, filling in NULL and missing values, and better quality assessment.
More thoughtful queries and comments for answering the questions.


