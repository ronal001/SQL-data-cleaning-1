# SQL-data-cleaning-1
Using SQL to clean data
<img width="512" height="338" alt="image" src="https://github.com/user-attachments/assets/46547955-764a-4388-8559-cf4562aa4b5b" />
-------------
This is an educational project on data cleaning and preparation using SQL. The original database in CSV format is located in the file club_member_info.csv. Here, we will explore the steps that need to be applied to obtain a cleansed version of the dataset.

## 1. View data
Using querry to show data:
```sql
SELECT * FROM club_member_info cmi 
LIMIT 5;
```
Result:
|full_name|age|martial_status|email|phone|full_address|job_title|membership_date|
|---------|---|--------------|-----|-----|------------|---------|---------------|
|addie lush|40|married|alush0@shutterfly.com|254-389-8708|3226 Eastlawn Pass,Temple,Texas|Assistant Professor|7/31/2013|
|      ROCK CRADICK|46|married|rcradick1@newsvine.com|910-566-2007|4 Harbort Avenue,Fayetteville,North Carolina|Programmer III|5/27/2018|
|Sydel Sharvell|46|divorced|ssharvell2@amazon.co.jp|702-187-8715|4 School Place,Las Vegas,Nevada|Budget/Accounting Analyst I|10/6/2017|
|Constantin de la cruz|35||co3@bloglines.com|402-688-7162|6 Monument Crossing,Omaha,Nebraska|Desktop Support Technician|10/20/2015|
|  Gaylor Redhole|38|married|gredhole4@japanpost.jp|917-394-6001|88 Cherokee Pass,New York City,New York|Legal Assistant|5/29/2019|

## 2. Creating a cleaned table
Using querry to create cleaned table from original table
```sql
CREATE TABLE club_member_info_cleaned (
	full_name VARCHAR(50),
	age INTEGER,
	martial_status VARCHAR(50),
	email VARCHAR(50),
	phone NVARCHAR(50),
	full_address NVARCHAR(50),
	job_title VARCHAR(50),
	membership_date NVARCHAR(50)
);

INSERT INTO club_member_info_cleaned 
SELECT * FROM club_member_info;
```

## 3. Cleaning data
3.1 Delete space 
```sql
UPDATE club_member_info_cleaned 
SET 	
	full_name = TRIM(full_name )
	,age = TRIM(age )
	,martial_status = TRIM(martial_status )
	,email = TRIM(email )
	,phone = TRIM(phone )
	,full_address = TRIM(full_address ) 
	,job_title  = TRIM(job_title )
	,membership_date = TRIM(membership_date );
```


3.2 Convert the name column to lowercase and capitalize the first letter of each name
```sql
UPDATE club_member_info_cleaned 
SET full_name = LOWER(full_name );

UPDATE club_member_info_cleaned  
SET full_name  = UPPER(SUBSTR(full_name,1,1)) || LOWER(SUBSTR(full_name,2)); 
```


3.3 Check whether there are any ages greater than 100 or NULL.
If there are, replace them with the MEAN value.
Export the age column to a CSV file (readable by Excel).
Use the MEDIAN function to calculate the median value ⇒ MEDIAN = 40
Create a log table
to store the rows that will be updated
and perform the update.
```sql
SELECT * FROM club_member_info_cleaned 
WHERE age > 100 or age IS NULL;

SELECT age from club_member_info_cleaned;

CREATE TABLE club_member_info_cleaned_log AS
SELECT * FROM club_member_info_cleaned 
WHERE 1 = 0;

INSERT INTO club_member_info_cleaned_log 
SELECT * FROM club_member_info_cleaned 
WHERE age > 100 OR age IS NULL;

UPDATE club_member_info_cleaned 
SET age = 40 
WHERE age > 100 OR age IS NULL;
```


3.4 Cleaning duplicate values
	3.4.1 Cheking duplicate values
```sql
SELECT 
	full_name 
	,age 
	,martial_status 
	,email 
	,phone 
	,full_address 
	,job_title 
	,membership_date 
	,COUNT(*) AS duplicated_value
FROM club_member_info_cleaned 
GROUP BY
	full_name 
	,age 
	,martial_status 
	,email 
	,phone 
	,full_address 
	,job_title 
	,membership_date 
HAVING COUNT(*) > 1;
```
	3.4.2 Create a temporary table containing unique values
```sql
Create a temporary table containing unique values
CREATE TABLE club_member_info_cleaned_unique AS
SELECT 
	full_name 
	,age 
	,martial_status 
	,email 
	,phone 
	,full_address 
	,job_title 
	,membership_date	
FROM club_member_info_cleaned 
GROUP BY 
	full_name 
	,age 
	,martial_status 
	,email 
	,phone 
	,full_address 
	,job_title 
	,membership_date;
```

	3.4.3 Check whether this temporary table contains duplicate values
```sql
SELECT 
	full_name 
	,age 
	,martial_status 
	,email 
	,phone 
	,full_address 
	,job_title 
	,membership_date 
	,COUNT(*) AS duplicated_value
FROM club_member_info_cleaned_unique  
GROUP BY
	full_name 
	,age 
	,martial_status 
	,email 
	,phone 
	,full_address 
	,job_title 
	,membership_date 
HAVING COUNT(*) > 1;
```

	3.4.4 Delete the old table and rename the temporary table containing unique values to the old table’s name.
```sql
DROP TABLE club_member_info_cleaned;

ALTER TABLE club_member_info_cleaned_unique 
RENAME TO club_member_info_cleaned;
```










```
