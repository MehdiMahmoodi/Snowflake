# Snowflake

**Snowflake for data warehousing**

## Prerequisites:
You must already have access to Snowflake on your computer, and you need to have basic knowledge of SQL queries.

## Introduction

This repo contains all step-by-step training sessions on Snowflake.

## What I expect to see

I wrote this repo based on my online courses plus my real-time experience as a Data Engineer/Scientist. In this course, you will find the fast way for starting Data warehousing on Snowflake. For having more in-depth knowledge of Snowflake, you need to spend a lot more time on it. Have fun:

Currently, this repo has 3 major parts.

## Step one: Creating virtual warehouse:

-   Go to warehouses and click on create. [You can see that here](https://github.com/MehdiMahmoodi/Snowflake/blob/master/1.png)
-  In the create warehouse window you need to specify the required information like:
  - Name: Name of the virtual warehouse
  - Size: X_small or Small suggested

AS AN ALTERNATIVE: you can write the below query in your worksheets to create the virtual warehouse named TRAINING_WH for you:
CREATE WAREHOUSE TRAINING_WH
WITH
    WAREHOUSE_SIZE = XSMALL
    AUTO_SUSPEND =  60
    AUTO_RESUME = TRUE
    INITIALLY_SUSPENDED = TRUE
    STATEMENT_QUEUED_TIMEOUT_IN_SECONDS = 300
    STATEMENT_TIMEOUT_IN_SECONDS = 600;

## Step two: Creating a Database:
-  Simply run this query in your worksheets:
CREATE  DATABASE SALES_MEHDI_DB 
DATA_RETENTION_TIME_IN_DAYS = 0
COMMENT = 'CREATED FOR TRAINING';

    - Or you can do it by clicking on [Databases tab following by clicking on creating](https://github.com/MehdiMahmoodi/Snowflake/blob/master/2.png)

### After creating the database and virtual warehouse, [make sure you set the context appropriately](https://github.com/MehdiMahmoodi/Snowflake/blob/master/3.png)

## Step three: Creating a schema:
- Simply just type the “create schema {schema name}” in your worksheets. As an example:
create schema sales_data;
create schema Sales_Views;
create schema sales_stage;

**Check the [Context again](https://github.com/MehdiMahmoodi/Snowflake/blob/master/4.png)**
- Also, you can activate the Warehouse, database, and schema by running the following query:
USE WAREHOUSE TRAINING_WH;
USE DATABASE SALES_MEHDI_DB;
USE SCHEMA Sales_stage;
## Step four: Creating a Table statement:
***You need to make sure you have the grant access for creating the table***
- You can go to Databases -> Choose the database which you want to create a table.
- Then click on [create](https://github.com/MehdiMahmoodi/Snowflake/blob/master/5.png) icon [and](https://github.com/MehdiMahmoodi/Snowflake/blob/master/6.png):  
    - Choose the name for that table
    - Select the schema for this table
    - Click on the plus sign (+) in front of the columns for specifying the columns
        - Choose the name of that column
        - Choose the right data type for each column
***Or you can simply run the following queries to create the table:***
create or replace table empt_basic (
first_name string ,
last_name string , 
email string , 
address string ,
city string , 
start_date date
);

***you can add a column by following query: 
alter table empt_basic
add address string;***


## Step five: Loading files into the created table:
1) Click on Databases
2) Choose your [table](https://github.com/MehdiMahmoodi/Snowflake/commit/1dc1552ebd024b0cd56705a529d8e7493556250c)
3) Click on [Load Table](https://github.com/MehdiMahmoodi/Snowflake/blob/master/17.png)
4) Choose the desire warehouse
5) you can choose the data source which it can be loaded from your local PC or external resources(Amazon S3, Azhure, google Cloud Platform,Snowflake Managed)
    - in this case we are going to load from local pc
6) select all files which need to be uploaded then Load

### As an alternative you can do this(not recomended):


#### For this part you need to install SnowSQL first. 
- For that reason you need to click on the [help tab](https://github.com/MehdiMahmoodi/Snowflake/blob/master/11.png) and then click on [Download](https://github.com/MehdiMahmoodi/Snowflake/blob/master/11.png)
- On the downloads menu, you need to choose or directly go to the [repository]( https://sfc-repo.snowflakecomputing.com/snowsql/bootstrap/1.2/index.html)
- Save and default Install the snowSQL
- For log in snowSQL:
 -  Open cmd
 -  Type snowsql -a your_account_name -u username [{see your account name}](https://github.com/MehdiMahmoodi/Snowflake/blob/master/12.png) then inter as an example snowsql –a ipa91849.us-east-1
 -  Then cmd would ask you for user name and password. These are the username and password you are using to log in to the [Snowfalke](https://github.com/MehdiMahmoodi/Snowflake/blob/master/13.png). Example: user: MEHDI , pass: Sal***14
***If you insert user and password correctly, you need to see the [username, database, and schema](https://github.com/MehdiMahmoodi/Snowflake/blob/master/14.png)***
- You can set warehouse, database, and schema by running the following code in snowsql:
USE WAREHOUSE TRAINING_WH;
USE DATABASE SALES_MEHDI_DB;
USE SCHEMA SALES_DATA;

- If you did it in the right way, you will be notice that the status of warehouse, datatable, and schema have been [changed](https://github.com/MehdiMahmoodi/Snowflake/blob/master/15.png)
    - Also you can check your status by running the following queries in snosql:
    select CURRENT_WAREHOUSE(),CURRENT_DATABASE(),CURRENT_SCHEMA(); 
    the result should be a table like this:
    +---------------------+--------------------+------------------+
| CURRENT_WAREHOUSE() | CURRENT_DATABASE() | CURRENT_SCHEMA() |
|---------------------+--------------------+------------------|
| TRAINING_WH         | SALES_MEHDI_DB     | SALES_DATA       |
+---------------------+--------------------+------------------+

##### Put data files in staging area
***Snow flake has 3 kinds of staging areas: the user stage, the table stage, and the name stage. In this repo we are going to load our data in table stage***
- you need to run the pu command in your snowsql as below:
put file://<path_to_file>/<filename> @[<namespace>.]<int_stage_name>[/<path>]  | @[<namespace>.]%<table_name>[/<path>]  | @~[/<path>];
As an example you can use the [sample data](7) in this repo and run the following code. Keep in mind you need to update the path-to-file,

put file://C:\Users\mmahmoo\Desktop\DataEngineering\Snowflake\Repo\Sample_Data(7)\employees0*.csv @SALES_MEHDI_DB.SALES_DATA.%emp_basic;

##### Copy data into tabel:
- by running the followin query you can transfer your data to your table:
copy into emp_basic
  from @%emp_basic
  file_format = (type = csv field_optionally_enclosed_by='"')
  pattern = '.*employees0[1-5].csv.gz'
  on_error = 'skip_file';

  ## Run the query to make sure you have your data in your table:
  - in worksheets run the following query:

select * from emp_basic;

# Have Fun!!












