## Data Engineering Workshop (Construction of ETL Snowpipe)

### $\textcolor{red}{Creating\ Project\ Infrastructure\}$
* Make sure we have authorization to SYSADMIN and Create a database named AGS_GAME_AUDIENCE
* Drop the PUBLIC schema and Create a schema named RAW.
* Now navigate into AGS_GAME_AUDIENCE database --> RAW Schema --> Create new Table named GAME_LOGS
```SQL
CREATE DATABASE AGS_GAME_AUDIENCE;
USE AGS_GAME_AUDIENCE;
DROP SCHEMA IF EXISTS PUBLIC;
CREATE SCHEMA RAW;
```

 ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/5a758118-deb0-4795-9021-b7625e6a80ef)

* Now create the external stage to load the data from Amazon S3 into Snowflake table. So mention the stage name and the bucker URL to fetch the data source.
* Stage name: uni-swetha and URL: s3://uni_swetha
* This operation may not workout in SYSADMIN, so transfer ownership to ACCOUNTADMIN, do the data loading and transfer back to SYSADMIN.
  
 ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/d181d9f1-8440-414a-82f2-1392540ad8d9)

* Check & Validate whether data is loaded properly to the snowflake
```SQL
USE AGS_GAME_AUDIENCE.RAW;
list @uni_swetha/kickoff
```
 ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/56845f65-3ea1-46c4-814d-64f2b9e296ba)

* Now create the required JSON File format to define the structure of the data
```SQL
USE AGS_GAME_AUDIENCE.RAW;
CREATE OR REPLACE FILE FORMAT FF_JSON_LOGS
  TYPE = 'JSON'
  STRIP_OUTER_ARRAY = TRUE;
```
* Load the FF_JSON_LOGS file in to the GAME_LOGS table using COPY INTO Command.
* Then build the SELECT statement that seperates every column attribute.
* 
```SQL
COPY INTO AGS_GAME_AUDIENCE.RAW.GAME_LOGS
FROM '@uni_swetha/kickoff'
FILE_FORMAT = (FORMAT_NAME = FF_JSON_LOGS);

SELECT 
    RAW_LOG:agent::text AS AGENT,
    RAW_LOG:user_event::text AS USER_EVENT,
    *
FROM GAME_LOGS;
```
 ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/b5f2dec0-fb7e-44a0-9ad7-18b733692e00)

* Create a View named LOGS and parse the columns accordingly shown below.
```SQL
CREATE OR REPLACE VIEW RAW.LOGS AS
SELECT 
    PARSE_JSON(RAW_LOG):agent::string AS AGENT,
    PARSE_JSON(RAW_LOG):user_event::string AS USER_EVENT,
    PARSE_JSON(RAW_LOG):user_login::string AS USER_LOGIN,
    TO_TIMESTAMP_NTZ(PARSE_JSON(RAW_LOG):datetime_iso8601::TEXT) AS DATETIME_ISO8601,
    RAW_LOG
FROM AGS_GAME_AUDIENCE.RAW.GAME_LOGS;

SELECT * FROM LOGS;
```
 ![image](https://github.com/swethamurthy25/Snowflake_demos/assets/112581595/6bc355ed-281c-4a99-8091-3fc3c7657cec)

* 

