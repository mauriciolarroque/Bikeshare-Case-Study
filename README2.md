# Part III: Further Data Cleaning & Data Processing in MySQL

<br>

![Sql_data_base_with_logo 1](https://github.com/mauriciolarroque/Bikeshare-Case-Study/assets/172843436/c198b878-c74d-4157-bc2c-6130ec576e3b)


<br>

   # Step 1: Resolving Issues with Duplicated Values 

<br>

<br>

  * Before proceeding further, I double-checked for duplicates by comparing how many ride_ids were unique vs. the total number of `ride_ids` in each table:

<br>

```mysql
SELECT 
    COUNT(ride_id) AS total_values,               -- Represents all the ride_id values
    COUNT(DISTINCT ride_id) AS unique_values      -- Represents every UNIQUE ride_id value
FROM february_2023                                -- We'll use the February data as an example
```

<br>

   * Although every `ride_id` showed up as unique in Excel, some of the ids were duplicated after converting the Excel workbooks into CSVs.
     
     * To find out more about them, I ran this query to show every ride_id in each table that was present more than once in the ride_id column.
<br>

```mysql
USE cyclistic_bikeshare_2023;
SELECT 
    ROW_NUMBER() OVER (ORDER BY ride_id) AS row_num, -- This returns a row number, so we can count the ride_ids
    "January 2023" AS month_name, 
    ride_id,                                  -- By selecting ride_id, we can see what the ride_ids look like
    COUNT(*) AS total
FROM january_2023
GROUP BY ride_id 
HAVING COUNT(*) > 1; -- This gives us all the ride_ids in the table that appear more than once
```

<br>

* This showed that every duplicate had the exact same format; they were all numbers in scientific notation (ie. `3.21E+15`, `4.56E+15`, etc.).

  • Since all others `ride_ids` were 16-digit combinations of 8 letters and 8 numbers, I decided to make every `ride_id` match this format.
   
    * However, I first ran the code **below** to check if there were any other ids with these odd characters:
  
<br>

```sql
SELECT 
    ROW_NUMBER() OVER (ORDER BY ride_id) AS row_num,
    "January 2023" AS month_name, 
    ride_id,
    COUNT(*) AS total
FROM january_2023
WHERE ride_id LIKE "%E+%" -- Returns all records containing the characters "E+"
GROUP BY ride_id
```

<br>

• As it turns out, there were, in fact, other non-duplicate `ride_id` values with this format. Which gave me the opportunity to change all the affected ride_ids at once: 

```sql
UPDATE january_2023
SET ride_id = CONCAT(
    -- Generate 8 random uppercase letters
    SUBSTRING(CONCAT(
        CHAR(FLOOR(65 + (RAND() * 26))),
        CHAR(FLOOR(65 + (RAND() * 26))),
        CHAR(FLOOR(65 + (RAND() * 26))),
        CHAR(FLOOR(65 + (RAND() * 26))),
        CHAR(FLOOR(65 + (RAND() * 26))),
        CHAR(FLOOR(65 + (RAND() * 26))),
        CHAR(FLOOR(65 + (RAND() * 26))),
        CHAR(FLOOR(65 + (RAND() * 26)))
    ), 1, 8),
    -- Generate 8 random numbers
    LPAD(FLOOR(RAND() * 100000000), 8, '0')
)
WHERE ride_id REGEXP 'E+'
```

<br>

Finally, I ran this query to check that all `ride_ids` were unique: 

```sql
SELECT
  (SELECT COUNT(DISTINCT ride_id) FROM january_2023) AS unique_ids, 
    COUNT(ride_id) AS properly_formatted_ids,
    (SELECT COUNT(ride_id) FROM january_2023) AS total_ids
FROM 
    january_2023
WHERE ride_id REGEXP '^(?=.*[A-Z])(?=.*[0-9]).*$' 
AND LENGTH(ride_id) = 16


-- The conditions narrow down to ride_ids that are 16 characters long and contain
-- only capitalized letters and numbers.
```

<br>

After confirming that all the `ride_ids` were unique, I moved on to processing the data into an easy-to-use format: 

<br>

<br>

# Step 2: Reviewing All Tables 

<br>


• To get a good sense of the data types in each table, I ran this super simple query: 

```sql
DESCRIBE january_2023; -- And february_2023, march_2023, etc.
```

<br> 

• Most of the content in the datasets looked good, but the `started_at` and `ended_at` columns needed to be changed to DATETIME format: 

```sql
ALTER TABLE february_2023
MODIFY COLUMN started_at DATETIME,
MODIFY COLUMN ended_at DATETIME;
```

