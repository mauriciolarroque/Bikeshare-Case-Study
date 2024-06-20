# Part III: Further Data Cleaning, Data Processing & Data Manipulation in MySQL

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

# Step 2: Changing Data Types

<br>


• To get a good sense of the data types in each table, I ran this super simple query: 

```sql
DESCRIBE january_2023; -- And february_2023, march_2023, etc.
```

<br> 

• Most of the content in the datasets looked good, but the `started_at` and `ended_at` columns needed to be changed to `DATETIME` format: 

```sql
ALTER TABLE february_2023
MODIFY COLUMN started_at DATETIME,
MODIFY COLUMN ended_at DATETIME;
```

<br>

Also, given that the `member_casual` column only had two values, I changed it to `ENUM` in order to maximize database efficiency: 

<br>

```sql
ALTER TABLE february_2023
MODIFY COLUMN member_casual ENUM('member', 'casual') NOT NULL;
```

<br>

# Step 3: Adding New Columns 

<br>

These columns would contain how many `minutes` each ride lasted, which `month` the ride took place in, how many `miles` were covered during the ride, which `hour` of the day the ride took place in, and which `day of the week` the ride took place in:

```sql
ALTER TABLE january_2023 ADD COLUMN ride_duration_min INT;

ALTER TABLE january_2023 ADD COLUMN ride_month CHAR(2);

ALTER TABLE january_2023 ADD COLUMN ride_miles DOUBLE; 

ALTER TABLE january_2023 ADD COLUMN ride_hour CHAR(2);

ALTER TABLE january_2023 ADD COLUMN ride_day CHAR(3);


ALTER TABLE february_2023 ADD COLUMN ride_duration_min INT;

ALTER TABLE february_2023 ADD COLUMN ride_month CHAR(2);

ALTER TABLE february_2023 ADD COLUMN ride_miles DOUBLE;

ALTER TABLE february_2023 ADD COLUMN ride_hour CHAR(2);

ALTER TABLE february_2023 ADD COLUMN ride_day CHAR(3);


ALTER TABLE march_2023 ADD COLUMN ride_duration_min INT;

ALTER TABLE march_2023 ADD COLUMN ride_month CHAR(2);

ALTER TABLE march_2023 ADD COLUMN ride_miles DOUBLE; 

ALTER TABLE march_2023 ADD COLUMN ride_hour CHAR(2);

ALTER TABLE march_2023 ADD COLUMN ride_day CHAR(3);
```

<br> 

### To add data to the empty new columns, I executed these queries:

<br>

```sql
UPDATE january_2023 SET ride_duration_min = TIMESTAMPDIFF(MINUTE, started_at, ended_at);

      -- Calculates ride duration by taking the difference in minutes of the started_at and ended_at columns 

UPDATE january_2023 SET ride_month = DATE_FORMAT(started_at, "%m"); 

      -- ride_month is equal to the two-digit numerical month associated with the value in the started_at column

UPDATE january_2023 SET ride_miles = ROUND((3959 * acos(
        cos(radians(start_lat)) * cos(radians(end_lat)) * cos(radians(end_lng) - radians(start_lng)) +
        sin(radians(start_lat)) * sin(radians(end_lat))
    )),2);

      -- Applies the Haversine formula to calculate the distance (in miles) between the start and end station coordinates

UPDATE january_2023 SET ride_hour = DATE_FORMAT(started_at, "%H");

UPDATE january_2023 SET ride_day = DATE_FORMAT(started_at, "%a");
```

<br>

These new columns would be especially useful after all the data was merged into a single table, which would be the next step in processing the bike-share data:

<br>

<br>

# Step 4: Merging All Tables

<br>

* The next task was to `combine all 12 tables` (each of which represented a month) into a single table that covered all of Cyclistic’s 2023 bikeshare data. However, as I was preparing to merge the data, I realized that there might be some potential concerns.

  * Since I had previously defined ride_id as the primary key individually in all 12 tables, this might cause complications during the merging process.

    * Just to be safe, I decided to temporarily `drop the primary keys` in each table:

<br>

<br>

```sql
ALTER TABLE january_2023 DROP PRIMARY KEY;
ALTER TABLE february_2023 DROP PRIMARY KEY;
ALTER TABLE march_2023 DROP PRIMARY KEY;
ALTER TABLE april_2023 DROP PRIMARY KEY;
ALTER TABLE may_2023 DROP PRIMARY KEY;
ALTER TABLE june_2023 DROP PRIMARY KEY;
ALTER TABLE july_2023 DROP PRIMARY KEY;
ALTER TABLE august_2023 DROP PRIMARY KEY;
ALTER TABLE september_2023 DROP PRIMARY KEY;
ALTER TABLE october_2023 DROP PRIMARY KEY;
ALTER TABLE november_2023 DROP PRIMARY KEY;
ALTER TABLE december_2023 DROP PRIMARY KEY;
```

<br>


* With each primary key dropped, it was now totally safe to merge the tables.

  * Although we could technically work with all 12 tables at the same time, this would lead to a lot of complicated JOIN statements down the line, which would reduce overall efficiency. 

     * Therefore, to simplify the data analysis process, it was a more optimal solution to combine all 12 months of data into a single source.

#### To do this, I created a new table - which we’ll call `cyclistic_2023` - into which I could transfer all the data:

<br>

<br>

<br>

```sql
CREATE TABLE cyclistic_2023 (
    ride_id VARCHAR(16) PRIMARY KEY, -- Ride_id will be our primary key
    rideable_type TEXT,
    started_at DATETIME,
    ended_at DATETIME,
    start_station_name TEXT,      
    start_station_id TEXT, -- Due to most station ids containing letters, we are setting text as the data type
    end_station_name TEXT, 
    end_station_id TEXT, 
    start_lat DOUBLE, 
    start_lng DOUBLE, 
    end_lat DOUBLE, 
    end_lng DOUBLE, 
      member_casual ENUM('member','casual'),
    ride_duration_min INT,
    ride_month CHAR(2), 
    ride_miles DOUBLE,
    ride_hour CHAR(2),
    ride_day CHAR(3))
```

<br>

Once the table was created, I executed these statements in MySQL to transfer all the data into the new table:

<br>

<br>

<br>

```sql
INSERT INTO cyclistic_2023 (ride_id, rideable_type, started_at, ended_at, start_station_name, 
                            start_station_id, end_station_name, end_station_id, start_lat, 
                            start_lng, end_lat, end_lng, member_casual, ride_duration_min, ride_month,
                            ride_miles, ride_hour, ride_day)
SELECT ride_id, rideable_type, started_at, ended_at, start_station_name, 
        start_station_id, end_station_name, end_station_id, start_lat, 
        start_lng, end_lat, end_lng, member_casual, ride_duration_min, ride_month, 
    ride_miles, ride_hour, ride_day
FROM january_2023;

-- The above query selects all data from the january_2023 table and inserts it into cyclistic_2023  

INSERT INTO cyclistic_2023 (ride_id, rideable_type, started_at, ended_at, start_station_name, 
                            start_station_id, end_station_name, end_station_id, start_lat, 
                            start_lng, end_lat, end_lng, member_casual, ride_duration_min, ride_month, 
                            ride_miles, ride_hour, ride_day)
SELECT ride_id, rideable_type, started_at, ended_at, start_station_name, 
        start_station_id, end_station_name, end_station_id, start_lat, 
        start_lng, end_lat, end_lng, member_casual, ride_duration_min, ride_month, 
    ride_miles, ride_hour, ride_day
FROM february_2023;

-- This statement will be repeated for all twelve tables 
```


<br>

<br>

# Step 3: Running Summary Statistics

<br>

## Max, Min, & Mean Values for Members vs. Casual Riders

<br>

<br>

```sql
SELECT 
   member_casual,
   rideable_type,
   ROUND(AVG(ride_duration_min),2) avg_ride_min,
   ROUND(AVG(ride_miles),2) avg_ride_distance,
   MAX(ride_duration_min) max_ride_min,
   (MAX(ride_duration_min)/60) max_ride_hours,
   MIN(ride_duration_min) min_ride_min,
   (MIN(ride_duration_min)/60) min_ride_hours,
   MAX(ride_miles) max_ride_distance,
   MIN(ride_miles) min_ride_distance	
FROM 
   cyclistic_2023    -- Modify to Q1, Q2, etc.
GROUP BY member_casual, rideable_type
ORDER BY member_casual, rideable_type;
```

<br>


<br>

| member_casual| rideable_type   | avg_ride_min | avg_ride_distance | max_ride_min | max_ride_hours | min_ride_min |min_ride_hours | max_ride_distance | min_ride_distance |
|--------------|-----------------|--------------|-------------------|--------------|----------------|--------------|---------------|-------------------|-------------------|
| member       | classic_bike    | 13.93        | 1.18              | 1560         | 26.0000        | 0            | 0             | 16.03             | 0
| member       | electric_bike   | 11.14        | 1.43              | 482          | 8.0333         | 0            | 0             | ~~6099.4~~ 25.37  | 0
| casual       | classic_bike    | 32.06        | 1.29              | 1560         | 26.0000        | 0            | 0             | ~~6096.86~~ 20.56 | 0
| casual       | docked_bike     | 182.81       | 1.35              | 98489        | 1641.4833      | 0            | 0             | 19.03             | 0
| casual       | electric_bike   | 14.26        | 1.34              | 480          | 8.0000         | 0            | 0             | 30.4              | 0 


<br>

### Part I: Addressing Outliers and Possible Errors 

<br>

* Before we can even begin to analyze this data, there are some problematic data points that we need to address. 
  
  * The maximum values were surprising, to say the least. Two of our entries here are greater than `6000` miles!

    * Since 6000 miles seems excessive (given that this is more than half the length of North America), I decided to investigate these cases.
    
    *  Fortunately, there were only `three` of these erroneous values. The problem occurred because the end station coordinates for these entries was `0°N, 0°E`.
    
    *  This caused the `Haversine formula` we used earlier to measure the distance between the start station coordinates and the coordinates for a random geographic point off the coast of South America.

       * Because this was unquestionably a dataset error (unless users ended their bike trip in the middle of the ocean), I updated these three `ride_miles` entries to **NULL** values.
         
<br>

<br>

* The next concerning data points were related to `docked bikes.`

  * Given that the average ride time on a docked bike was `182.8` minutes – while the longest ride distance on a docked bike was only about 19 miles – the data points were not adding up.
    
    * After querying the ride times in SQL, I found that well over 4000 docked bike rides were greater than the average, and one ride had even lasted a whopping `68` days!

    * The most rational explanation for this is that the bike rides were never officially "ended" after having been initiated. This could be because the bikes were lost, the customer forgot to end the bike ride (or they were under the impression that the bike ride would end automatically), or perhaps because of a technical error on Cyclistic's end.

         * In any case, without further background information regarding these issues, we cannot arrive at any definite conclusions as to what caused these rides to last so long. 
<br>

#### * In conclusion, this issue would have to be discussed and reviewed with the rest of the Cyclistic team in order to determine the cause and origin of the aberrant data. 

   
<br>

<br> 

<br>




