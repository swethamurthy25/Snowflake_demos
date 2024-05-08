## Data Warehousing with Snowflake

### $\textcolor{red}{Basics\ with\ Database\ and\ Tables\}$

1. Make sure we have SYSADMIN Access
2. Navigate to Data -- Database ---> Create a Database and name it GARDEN_PLANTS
3. Drop the Public Schema
4. Create three new schemas in your GARDEN_PLANTS database. Name them VEGGIES, FRUITS, and FLOWERS.

   ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/2dbdda34-6f52-4da6-b0f8-13223c578626)

* Open the new SQL Worksheet and create the ROOT DEPTH table in the GARDEN_PLANTS.VEGGIES Schema

 ```SQL
create or replace table GARDEN_PLANTS.VEGGIES.ROOT_DEPTH (
   ROOT_DEPTH_ID number(1), 
   ROOT_DEPTH_CODE text(1), 
   ROOT_DEPTH_NAME text(7), 
   UNIT_OF_MEASURE text(2),
   RANGE_MIN number(2),
   RANGE_MAX number(2)
   ); 
```
  ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/36240572-3838-4aad-8263-fa726b8bc1c1)

* INSERT a value into ROOT DEPTH Table. Make sure we are in the correct database and warehouse before inserting the value.

```SQL
USE WAREHOUSE COMPUTE_WH;

INSERT INTO ROOT_DEPTH (ROOT_DEPTH_ID , ROOT_DEPTH_CODE , ROOT_DEPTH_NAME , UNIT_OF_MEASURE , RANGE_MIN , RANGE_MAX)
VALUES (1, 'S', 'Shallow', 'cm', 30, 45);
```
  ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/c92bbacd-8740-4c95-87ee-dcc1054f69c0)

* INSERT Two more rows into the table with the below values

```SQL
USE WAREHOUSE COMPUTE_WH;

INSERT INTO ROOT_DEPTH (ROOT_DEPTH_ID, ROOT_DEPTH_CODE, ROOT_DEPTH_NAME, UNIT_OF_MEASURE, RANGE_MIN, RANGE_MAX)
VALUES (2, 'M', 'Medium', 'cm', 45, 60),(3, 'D', 'Deep', 'cm', 60, 90);
```
  ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/092598d7-cc0a-4f05-97ee-3e201d43eb35)

### $\textcolor{red}{Loading\ csv\ file\ into\ Table Schema\}$

* Create a new table "VEGETABLE_DETAILS" in the VEGGIES Schema
```SQL
create table garden_plants.veggies.vegetable_details
(
plant_name varchar(25)
, root_depth_code varchar(1)    
);
```
* Now navigate to the VEGETABLE_DETAILS table created and click on "LOAD DATA" in the top right corner.
* Drag and Drop the CSV file into the pop-up and click on next
* Then select the file format as CSV and skip-header as skip one line and Field optionality enclosed by double quotes.
* The CSV File with 21 rows will be loaded into the table.

  ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/30482dc9-e1a4-4ee8-bec2-09483e672cb9)

  ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/146277de-ffe4-4d31-b129-589ea9fc0311)

* Create the below two file formats in the VEGGIES Schema

```SQL
create file format garden_plants.veggies.PIPECOLSEP_ONEHEADROW 
    TYPE = 'CSV'
    FIELD_DELIMITER = '|' 
    SKIP_HEADER = 1 
    ;

create file format garden_plants.veggies.COMMASEP_DBLQUOT_ONEHEADROW 
    TYPE = 'CSV'
    SKIP_HEADER = 1 
    FIELD_OPTIONALLY_ENCLOSED_BY = '"' 
    ;
```
  ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/626a2c8b-e6af-435d-a7ac-7279b7cc4984)

### $\textcolor{red}{Creating\ Snowflake\ Stages\}$

* We already have an S3 bucket on AWS in the US-West-2 Region with the name "uni-lab-files."
* Here is the link to the bucket: https://uni-lab-files.s3.us-west-2.amazonaws.com/
* Now create a stage that points to an S3 bucket.
* Navigate to UTIL_DB --> PUBLIC Schema --> On top right corner click on create --> Stages --> Amazon S3
* Provide the stage name and URL in the pop-up and click on Create.

  ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/43f4822e-54fb-4757-bf9b-31c78d547e96)

* View the files from the stage.

   ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/10ee5795-c831-49af-b0ff-2415e44cbe66)

* Loading file from S3 bucket into new table

```SQL
copy into vegetable_details_soil_type
from @util_db.public.like_a_window_into_an_s3_bucket
files = ( 'VEG_NAME_TO_SOIL_TYPE_PIPE.txt')
file_format = ( format_name=GARDEN_PLANTS.VEGGIES.PIPECOLSEP_ONEHEADROW );
```
  ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/1b450fa2-3ecc-4b34-9c50-88c57eb60de1)

### $\textcolor{red}{Create\ and\ Manage\ Sequences\}$

* Create the Database Library_Card_Catalog and Author Table

```SQL
CREATE DATABASE LIBRARY_CARD_CATALOG COMMENT = 'DWW Lesson 9 ';
USE DATABASE LIBRARY_CARD_CATALOG;

CREATE OR REPLACE TABLE AUTHOR (
   AUTHOR_UID NUMBER 
  ,FIRST_NAME VARCHAR(50)
  ,MIDDLE_NAME VARCHAR(50)
  ,LAST_NAME VARCHAR(50)
);

INSERT INTO AUTHOR(AUTHOR_UID,FIRST_NAME,MIDDLE_NAME, LAST_NAME) 
Values(1, 'Fiona', '','Macdonald'),(2, 'Gian','Paulo','Faleschini');

SELECT * FROM AUTHOR;
```
  ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/be95c131-390e-40c1-a6a3-5a2b1d720447)

* Navigate to Library catalog db --> Public Schema --> On top right corner click on create --> Sequence
* A pop-up will appear, create the sequence as below on the respective field. Then run the sequence.
  
```SQL
create sequence SEQ_AUTHOR_UID
    start = 1
    increment = 1
    comment = 'Use this to fill in AUTHOR_ID';
```

 ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/b75eee4c-1324-4e1c-9f64-d76fcbe0bab9)

* Resetting the Sequence to start from 3.
```SQL
//Drop and recreate the counter (sequence) so that it starts at 3 
//Then we'll add the other author records to our author table
CREATE OR REPLACE SEQUENCE "LIBRARY_CARD_CATALOG"."PUBLIC"."SEQ_AUTHOR_UID" 
START 3 
INCREMENT 1 
COMMENT = 'Use this to fill in the AUTHOR_UID every time you add a row';

//Add the remaining author records and use the nextval function instead 
//of putting in the numbers
INSERT INTO AUTHOR(AUTHOR_UID,FIRST_NAME,MIDDLE_NAME, LAST_NAME) 
Values
(SEQ_AUTHOR_UID.nextval, 'Laura', 'K','Egendorf')
,(SEQ_AUTHOR_UID.nextval, 'Jan', '','Grover')
,(SEQ_AUTHOR_UID.nextval, 'Jennifer', '','Clapp')
,(SEQ_AUTHOR_UID.nextval, 'Kathleen', '','Petelinsek');
```
* Creating a 2nd Counter, a Book Table, and a Mapping Table
```SQL
USE DATABASE LIBRARY_CARD_CATALOG;

CREATE OR REPLACE SEQUENCE "LIBRARY_CARD_CATALOG"."PUBLIC"."SEQ_BOOK_UID" 
START 1 
INCREMENT 1 
COMMENT = 'Use this to fill in the BOOK_UID everytime you add a row';

CREATE OR REPLACE TABLE BOOK
( BOOK_UID NUMBER DEFAULT SEQ_BOOK_UID.nextval
 ,TITLE VARCHAR(50)
 ,YEAR_PUBLISHED NUMBER(4,0)
);

INSERT INTO BOOK(TITLE,YEAR_PUBLISHED)
VALUES('Food',2001),('Food',2006),('Food',2008),('Food',2016),('Food',2015);

// Create the relationships table- this is sometimes called a "Many-to-Many table"
CREATE TABLE BOOK_TO_AUTHOR
(  BOOK_UID NUMBER
  ,AUTHOR_UID NUMBER
);

INSERT INTO BOOK_TO_AUTHOR(BOOK_UID,AUTHOR_UID)
VALUES(1,1),(1,2),(2,3),(3,4),(4,5),(5,6);

//Join 3 tables and validate now
select * 
from book_to_author ba 
join author a on ba.author_uid = a.author_uid 
join book b on b.book_uid=ba.book_uid; 
```
  ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/d579b6d1-b5ea-40d2-9091-4d2b2bd4511b)

  ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/6886b9ce-1ea0-496e-b194-ade81330c8d0)

### $\textcolor{red}{Working\ with\ Semi Structured\ Data-\ JSON\}$

* Download the author_with_header.json file
* In LIBRARY_CARD_CATALOG, create a table named AUTHOR_INGEST_JSON and create a file format
* In the file format, set all values to FALSE by default, and set STRIP_OUER_ARRAY as TRUE as it ignores the square brackets
  and load each author into a separate row.

```SQL
USE LIBRARY_CARD_CATALOG;

CREATE TABLE LIBRARY_CARD_CATALOG.PUBLIC.AUTHOR_INGEST_JSON 
(
  RAW_AUTHOR VARIANT
);

CREATE FILE FORMAT LIBRARY_CARD_CATALOG.PUBLIC.JSON_FILE_FORMAT 
TYPE = 'JSON' 
COMPRESSION = 'AUTO' 
ENABLE_OCTAL = FALSE
ALLOW_DUPLICATE = FALSE 
STRIP_OUTER_ARRAY = TRUE
STRIP_NULL_VALUES = FALSE 
IGNORE_UTF8_ERRORS = FALSE;
```
* Use a COPY INTO statement to push the data from the stage to the AUTHOR_INGEST_JSON Table

```SQL
COPY INTO LIBRARY_CARD_CATALOG.PUBLIC.AUTHOR_INGEST_JSON (RAW_AUTHOR)
FROM '@util_db.public.like_a_window_into_an_s3_bucket/author_with_header.json'
FILE_FORMAT = (FORMAT_NAME = LIBRARY_CARD_CATALOG.PUBLIC.JSON_FILE_FORMAT);

SELECT raw_author FROM AUTHOR_INGEST_JSON;
```
  ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/58968ee0-1747-43d3-aeaf-4f22ccb264f6)

* Query the JSON data

```SQL
select raw_author:AUTHOR_UID
from author_ingest_json;

SELECT 
 raw_author:AUTHOR_UID
,raw_author:FIRST_NAME::STRING as FIRST_NAME
,raw_author:MIDDLE_NAME::STRING as MIDDLE_NAME
,raw_author:LAST_NAME::STRING as LAST_NAME
FROM AUTHOR_INGEST_JSON;
```
 ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/a2475e45-6d06-4c3b-9765-5900302b0e90)

* Load and Query Nested JSON - Nested Author and Book JSON data
* Download the 'json_book_author_nested.json' data
* Create an ingestion table in util_db.public schema
* Upload the downloaded data "json_book_author_nested.json" into the table 'NESTED_INGEST_JSON' 

```SQL
CREATE OR REPLACE TABLE LIBRARY_CARD_CATALOG.PUBLIC.NESTED_INGEST_JSON 
(
  "RAW_NESTED_BOOK" VARIANT
);
COPY INTO LIBRARY_CARD_CATALOG.PUBLIC.NESTED_INGEST_JSON (RAW_NESTED_BOOK)
FROM '@util_db.public.like_a_window_into_an_s3_bucket/json_book_author_nested.json'
FILE_FORMAT = (FORMAT_NAME = LIBRARY_CARD_CATALOG.PUBLIC.JSON_FILE_FORMAT);
```
* Query the JSON file and validate the columns

```SQL
SELECT RAW_NESTED_BOOK
FROM NESTED_INGEST_JSON;

SELECT RAW_NESTED_BOOK:year_published
FROM NESTED_INGEST_JSON;

SELECT RAW_NESTED_BOOK:authors
FROM NESTED_INGEST_JSON;

//Use these example flatten commands to explore flattening the nested book and author data
SELECT value:first_name
FROM NESTED_INGEST_JSON
,LATERAL FLATTEN(input => RAW_NESTED_BOOK:authors);

SELECT value:first_name
FROM NESTED_INGEST_JSON
,table(flatten(RAW_NESTED_BOOK:authors));

//Add a CAST command to the fields returned
SELECT value:first_name::VARCHAR, value:last_name::VARCHAR
FROM NESTED_INGEST_JSON
,LATERAL FLATTEN(input => RAW_NESTED_BOOK:authors);

//Assign new column  names to the columns using "AS"
SELECT value:first_name::VARCHAR AS FIRST_NM
, value:last_name::VARCHAR AS LAST_NM
FROM NESTED_INGEST_JSON
,LATERAL FLATTEN(input => RAW_NESTED_BOOK:authors);

### $\textcolor{red}{Use\ Case:\ Working\ with\ Semistructured\ Twitter\ data\}$
JSON File: nutrition_tweets.json

```SQL
CREATE DATABASE SOCIAL_MEDIA_FLOODGATES;

USE DATABASE SOCIAL_MEDIA_FLOODGATES;
CREATE TABLE SOCIAL_MEDIA_FLOODGATES.PUBLIC.TWEET_INGEST 
("RAW_STATUS" VARIANT) 
COMMENT = 'Bring in tweets, one row per tweet or status entity';

//Creating a JSON file format
CREATE FILE FORMAT SOCIAL_MEDIA_FLOODGATES.PUBLIC.JSON_FILE_FORMAT 
TYPE = 'JSON' 
COMPRESSION = 'AUTO' 
ENABLE_OCTAL = FALSE 
ALLOW_DUPLICATE = FALSE 
STRIP_OUTER_ARRAY = TRUE 
STRIP_NULL_VALUES = FALSE 
IGNORE_UTF8_ERRORS = FALSE;
```
* Loading the JSON File into the new database from staging 

```SQL
COPY INTO SOCIAL_MEDIA_FLOODGATES.PUBLIC.TWEET_INGEST (RAW_STATUS)
FROM '@util_db.public.like_a_window_into_an_s3_bucket/nutrition_tweets.json'
FILE_FORMAT = (FORMAT_NAME = SOCIAL_MEDIA_FLOODGATES.PUBLIC.JSON_FILE_FORMAT);
```
* Query the nested JSON Tweet data
```SQL
SELECT RAW_STATUS
FROM TWEET_INGEST;

SELECT RAW_STATUS:entities
FROM TWEET_INGEST;

SELECT RAW_STATUS:entities:hashtags
FROM TWEET_INGEST;

//Explore looking at specific hashtags by adding bracketed numbers- This query returns just the first hashtag in each tweet
SELECT RAW_STATUS:entities:hashtags[0].text
FROM TWEET_INGEST;

//This version adds a WHERE clause to get rid of any tweet that does not include any hashtag
SELECT RAW_STATUS:entities:hashtags[0].text
FROM TWEET_INGEST
WHERE RAW_STATUS:entities:hashtags[0].text is not null;

//Perform a simple CAST on the created_at key
//Add an ORDER BY clause to sort by the tweet's creation date
SELECT RAW_STATUS:created_at::DATE
FROM TWEET_INGEST
ORDER BY RAW_STATUS:created_at::DATE;

//Flatten statements that return the whole hashtag entity
SELECT value
FROM TWEET_INGEST
,LATERAL FLATTEN
(input => RAW_STATUS:entities:hashtags);

SELECT value
FROM TWEET_INGEST
,TABLE(FLATTEN(RAW_STATUS:entities:hashtags));

//Flatten statement that restricts the value to just the TEXT of the hashtag
SELECT value:text
FROM TWEET_INGEST
,LATERAL FLATTEN
(input => RAW_STATUS:entities:hashtags);


//Flatten and return just the hashtag text, CAST the text as VARCHAR
SELECT value:text::VARCHAR
FROM TWEET_INGEST
,LATERAL FLATTEN
(input => RAW_STATUS:entities:hashtags);

//Flatten and return just the hashtag text, CAST the text as VARCHAR
// Use the AS command to name the column
SELECT value:text::VARCHAR AS THE_HASHTAG
FROM TWEET_INGEST
,LATERAL FLATTEN
(input => RAW_STATUS:entities:hashtags);

//Add the Tweet ID and User ID to the returned table
SELECT RAW_STATUS:user:id AS USER_ID
,RAW_STATUS:id AS TWEET_ID
,value:text::VARCHAR AS HASHTAG_TEXT
FROM TWEET_INGEST
,LATERAL FLATTEN
(input => RAW_STATUS:entities:hashtags);
 ```


