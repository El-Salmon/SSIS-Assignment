# Integration Services Project1

## Method

1. Truncate the Staging table
2. Import the entire data in the stagging table
3. Run a script to fetch the list of countries in an object variable.
4. Foreach loop to go thorugh each of the country and create a table if it does not exist.
5. Update the entries in case a customer id is back(since this is the primary key).
6. Insert the remaining new entries.
7. The data cleaning and cast is handled within the Update and insert itself.

## Query for creating the Staging table

```SQL SERVER
create table input_staging 
(
Customer_Name	varchar(255) not null,
Customer_ID	varchar(18) not null,
Customer_Open_Date	varchar(50) not null,
Last_Consulted_Date	varchar(50),
Vaccination_Type	char(5),
Doctor_Consulated	char(255),
States	char(255),
Country	char(50),
Post_Code	char(255),
Date_of_Birth	varchar(50),
Active_Customer char(1),
PRIMARY KEY (Customer_ID)
);
```

## Query for creating the individual tables

```SQL SERVER
DECLARE @TableName NVARCHAR(MAX);
SET @TableName = 'Final_Table_' + CAST(? AS NVARCHAR(MAX)); --? will be replaced by the country name

IF NOT EXISTS (SELECT * FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA = 'dbo' AND TABLE_NAME = @TableName)
BEGIN
    EXEC('CREATE TABLE ' + @TableName + ' (
Customer_Name	varchar(255) not null,
Customer_ID	varchar(18) not null,
Customer_Open_Date	date not null,
Last_Consulted_Date	date,
Vaccination_Type	char(5),
Doctor_Consulated	char(255),
States	char(255),
Country	char(5),
Post_Code	char(255),
Date_of_Birth	date,
Active_Customer char(1),
age int,
days_since_last_consulted int,
PRIMARY KEY (Customer_ID))');
END
```

## Additional DERIVED COLUMNS

1. Age = DATEDIFF(year,Date_of_Birth,GETDATE()) --difference between the date of birth and current date in years
2. days_since_last_consulted = DATEDIFF(day,Last_Consulted_Date ,GETDATE()) > 30 then  1 else 0 end --if the difference between the last consultation date and current date is >30 then flag as 1 else 0
