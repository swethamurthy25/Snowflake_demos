## Data Warehousing with Snowflake

### $\textcolor{red}{Create\ and\ Insert\ Function}$

* Make sure we have SYSADMIN Access
* Navigate to Data -- Database ---> Create a Database and name it GARDEN_PLANTS
* Drop the Public Schema
* Create three new schemas in your GARDEN_PLANTS database. Name them VEGGIES, FRUITS, and FLOWERS.

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

* Create the Database Library_Card_Catalog and

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



