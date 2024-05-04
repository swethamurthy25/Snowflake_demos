## Data Warehousing with Snowflake

### $\textcolor{red}{Creating\ Database\ and\ Data\ Containers\}$

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

* INSERT Two more rows in to the table with below values

```SQL
USE WAREHOUSE COMPUTE_WH;

INSERT INTO ROOT_DEPTH (ROOT_DEPTH_ID, ROOT_DEPTH_CODE, ROOT_DEPTH_NAME, UNIT_OF_MEASURE, RANGE_MIN, RANGE_MAX)
VALUES (2, 'M', 'Medium', 'cm', 45, 60),(3, 'D', 'Deep', 'cm', 60, 90);
```
  ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/092598d7-cc0a-4f05-97ee-3e201d43eb35)


