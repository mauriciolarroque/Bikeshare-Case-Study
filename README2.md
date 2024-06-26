# Part III: Further Data Cleaning & Data Processing in MySQL

<br>

![Sql_data_base_with_logo 1](https://github.com/mauriciolarroque/Bikeshare-Case-Study/assets/172843436/c198b878-c74d-4157-bc2c-6130ec576e3b)


<br>

   # Step 1: Resolving Issues with Duplicated Values 

<br>

<br>

  * Although the initial data cleaning showed that all `ride_id`s were unique values, I ran another check for duplicates in `MySQL`: 

<br>

```mysql
SELECT 
    COUNT(ride_id) AS total_values,               -- Represents all the ride_id values
    COUNT(DISTINCT ride_id) AS unique_values      -- Represents every UNIQUE ride_id value
FROM february_2023                                -- We'll use the February data as an example
```

<br>

   * Unfortunately, some of the ids in the original datasets `were duplicated` after I converted the Excel files back into CSVs to load them into the database.
     
     * To find out more about how this happened, I ran this query to show every ride_id in each table which was present more than once in the ride_id column.
       
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

  • Since all others `ride_ids` were 16-digit combinations of 8 letters and 8 numbers, it was best to make every `ride_id` match this format.
   
    * However, I first ran the query **below** to check if there were any other ids with these odd characters:
  
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

• As it turns out, there were, in fact, other non-duplicate `ride_id` values with this format. 

* This gave me the opportunity to solve both problems at once; using the query below, I could convert all the `ride_id`s into the same format while also (most likely) getting rid of any duplicate values:

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

All of the ids were now in the same format, so all I had to do was check again to see that `none` of them were `duplicate`:

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

The query above confirmed that all of the duplicated ride_ids had been sucessfully removed. 

Now that the duplicates were sorted out, the next step was to process the data through various tasks that would make it much easier to run queries and get all the data I needed in the `same place`:

<br>

<br>

# Step 2: Changing Data Types

<br>


• To get a good sense of the data types in each table, I ran this super simple query: 

```sql
DESCRIBE january_2023; -- And february_2023, march_2023, etc.
```

<br> 

• The datasets looked mostly good, but the `started_at` and `ended_at` columns needed to be changed to `DATETIME` format. This would make it possible to run DATE functions and get vital insights:

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

    * To avoid any issues, I `dropped the primary keys` in each table. After the full-year table was made, I would reinstate `ride_id` as the primary key. 

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


* Now, it was time to go through the process of combining all 12 tables into a single table for the `full year of 2023`:

<br>

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

* With the table structure created, these statements in MySQL would `transfer` all the data from the existing tables into the new empty table:

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

* Now that all the data was stored in a single source, I wanted to check that none of the `ride_ids` were duplicate. Since I had previously changed the `ride_ids` individually in all 12 tables, this could be a concern:

<br>

<br>

```sql

SELECT 
    COUNT(DISTINCT ride_id) AS unique_values,
    COUNT(ride_id) AS total_values
FROM 
cyclistic_2023
```
<br>

<br>

* Luckily, the total number of ride_ids in the new table (`5719877`) was `equal` to the number of `unique` ride_ids.

* Now that all `5.7 million` bike-ride had their own unique identifiers, it looked like the full-year table was complete.

* However, little did I know that there would be a few more bumps in the road before getting the data fully ready for the data analysis process: 

<br>

# Step 5: Cleaning Up Ride Duration Column 

<br>

<br>

After running some queries on the `ride duration` column, I got some surprising results:

<br>

<br>

```sql
SELECT
  DISTINCT(ride_duration_min) AS ride_times,
  member_casual,
  ride_miles
FROM cyclistic_2023
WHERE ride_month = "01" AND member_casual = "member"
```

<br>

<br>

* After running the above query, several ride times showed up as `negative` amounts of time, which was obviously impossible (unless the bike riders had somehow invented time travel). 
  
  * Therefore, I wrote up a few queries to find out more details about the affected rows and count exactly how many rows had ride times `less than zero`:

<br>

<br>

```sql
SELECT 
started_at, 
ended_at
FROM cyclistic_2023
WHERE started_at > ended_at; -- selecting timestamps where start time is later than the end time

SELECT 
  COUNT(ride_id)
FROM cyclistic_2023
WHERE started_at > ended_at
```

<br>

<br>

As it turns out, some of the `end times` were actually earlier than the start times. A COUNT function showed that `134 rows` were affected by this bad data. 

* After looking at the timestamps, it seemed to have been a case of the system mixing up the start times with the end times. However, with no one to consult about the data source or what might have gone wrong, there was no way to know for sure.

* Since there were only `134` affected rides (which is a negligible amount considering that there are almost `6 million rows` in the database) I decided to `delete` the rows using this statement:

<br>

<br>

```sql
DELETE FROM cyclistic_2023
WHERE ride_duration < 0;
```

<br>

<br>

* Then, I rechecked every column in the new table:

<br>

<br>

```sql
SELECT COUNT(ride_id) AS ride_id,
        COUNT(rideable_type) AS rideable_type,
        COUNT(started_at) AS started_at,
        COUNT(ended_at) AS eneded_at,
        COUNT(start_station_name) AS start_station_name,
        COUNT(start_station_id) AS start_station_id,
        COUNT(end_station_name) AS end_station_name,
        COUNT(end_station_id) AS end_station_id,
        COUNT(start_lat) AS start_lat,
        COUNT(start_lng) AS start_lng,
        COUNT(end_lat) AS end_lat,
        COUNT(end_lng) AS end_lng,
        COUNT(member_casual) AS member_casual,
        COUNT(ride_duration_min) AS ride_duration_min,
        COUNT(ride_month) AS ride_month,
        COUNT(ride_miles) AS ride_miles,
        COUNT(ride_hour) AS ride_hour,
        COUNT(ride_day) AS ride_day
    FROM cyclistic_2023

-- Counting all values in each column to confirm that each column contains all expected values.

SELECT 
    COUNT(ride_id) AS count_errors
FROM 
    cyclistic_2023 
WHERE ride_miles < 0;

-- Selecting all rows where the distance traveled is less than zero. 


SELECT ride_id, ride_miles
FROM cyclistic_2023
WHERE ride_miles NOT REGEXP '^[0-9]+(\.[0-9]{1,2})?$'; 

-- Filtering for any ride distance values which may be non-numerical.


SELECT COUNT(started_at) AS count_valid_start
FROM cyclistic_2023
WHERE started_at REGEXP '^[0-9]{4}-[0-9]{2}-[0-9]{2} [0-9]{2}:[0-9]{2}(:[0-9]{2})?$';

-- Confirming all values in the started_at column are DATETIME values 


SELECT COUNT(ended_at) AS count_valid_end
FROM cyclistic_2023
WHERE ended_at REGEXP '^[0-9]{4}-[0-9]{2}-[0-9]{2} [0-9]{2}:[0-9]{2}(:[0-9]{2})?$';

-- Confirming all values in the ended_at column are DATETIME values
```

<br>

<br>

* The first query counted all of the values in each column. Fortunately, the output indicated that all columns had their expected values, minus the fields which had previously been identified as containing nulls during the initial phases of data cleaning. 

  * The second query checked for `any columns` that had a negative ride distance, while the third used a `REGEXP` function to filter for any values in the ride_miles column which were non-numerical.
    
    * The fourth and fifth queries also leveraged the REGEXP function to count all values in the started_at and ended_at columns which were in `YYYY-MM-DD HH:MM:SS` format.

<br>

#### The next step was to run some summary statistics to check out how the data in the new table looked: 

<br>

<br>


# Step 6: Indexing Columns

<br> 

* In order to make it easier to process queries, I added a few `indexes` on certain columns. Since `ride_id` was already indexed as a primary key, the next step was to index the columns I would use most often.

  * The `ride_duration_min`, `ride_month`, and `member_casual` (each rider’s membership status) columns were great candidates for indexing because they would be in the majority of queries executed during data analysis:

 <br>

 ```sql
CREATE INDEX idx_ride_duration ON cyclistic_2023 (ride_duration_min); 

CREATE INDEX idx_ride_month ON cyclistic_2023 (ride_month); 

CREATE INDEX idx_member_status ON cyclistic_2023 (member_casual);
```

<br>

* These indexes would make it more time and resource-efficient to carry out the data analysis phase of this project:

   * Next up, I took a few more steps to check that all the data was ready for analysis: 

<br>

<br>


# Step 7: Running Summary Statistics to Check for Outliers

<br>

* To get a better idea of the data ranges in the new table, I calculated some summary statistics:

  <br>

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

* From these results, several red flags popped up. 
  
  * The maximum values were surprising, to say the least. According to the data, the longest distance covered on a bike trip was well over `6000 miles`. 

    * Given that this was more than half the length of the United States, there was no way this ride distance could be right. So I decided to investigate these cases by running a query to see how many ride distances were `greater than` 100 miles.
    
    *  Fortunately, there were only `three` entries greater than 100 miles; all of which were roughly 6000 miles in length. The rest of the `ride_miles` columns were under 30 miles.
    
       * The problem occurred because the end station coordinates of the affected rows were 0, 0.
       
       * Earlier on, I had used the `Haversine formula` to create the `ride_miles` column by measuring the distance between the start and end station coordinates for each row. During this process, SQL entered the distance between the start station coordinates and `0°N, 0°E`, which is a random geographic point off the coast of South America. This explains why the rides were thousands of miles long. 

    * Because of this, I just changed the affected `ride_miles` columns to `NULL` values. 
         
<br>

<br>

* The next concerning data points were related to `docked bikes.`

  * Given that the average ride time on a docked bike was `182.8` minutes – while the longest ride distance on a docked bike was only about 19 miles – the data points were not adding up.
    
    * After querying the ride times in SQL, I found that well over `4000` rides on docked bikes were much longer than the average of 2 hours. One ride even lasted a jaw-dropping `68` days.

    * These events could have transpired for a lot of reasons. Maybe the bikes were lost, the customer forgot to end the bike ride, the customer mistakenly thought that they had ended the bike ride, or maybe there was some kind of technical issue on the bikesharing app or on the bike's geo-tracking hardware.

         * In any case, without further background information regarding these issues, we cannot arrive at any definite conclusions as to what caused these rides to last so long.
           
<br>

#### * In conclusion, this issue would have to be discussed and reviewed with the rest of the Cyclistic team in order to determine the cause and origin of the aberrant data. 

   
<br>

<br> 

<br>

 Now that all the data cleaning was done, I created some temporary tables for each quarter so I could quickly pull up data without overloading the database:

<br>

<br>

```sql
CREATE TEMPORARY TABLE temp_q1
SELECT 
    *
FROM cyclistic_2023
WHERE ride_month IN ("01", "02", "03");


CREATE TEMPORARY TABLE temp_q2
SELECT 
    *
FROM cyclistic_2023
WHERE ride_month IN ("04", "05", "06");

-- Repeat process for the following quarters 
```

<br>

<br>

With these temporary tables created, everything was ready to start `analyzing` the data: 

<br>

### Link: [Back to Main Page](https://github.com/mauriciolarroque/Bikeshare-Case-Study/blob/main/README.md#part-iv-mysql-data-analysis)

<br>

<br>
