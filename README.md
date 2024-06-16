
![Frame 2(1)](https://github.com/mauriciolarroque/Bikeshare-Case-Study/assets/172843436/96fe9b02-8e7d-4605-b85b-16f91240479e)


# Bikesharing Data Analysis Project: Excel, SQL & Tableau



## Overview 

<br>

This case study from the Google Data Analytics Certificate program explores the 2023 data of Cyclistic: a fictional **bikesharing** company. 


For this project, we'll be using **Microsoft Excel, MySQL** and **Tableau**  to do all of our data analysis and answer our hypothetical stakeholder's business questions. All of the datasets I used are stored [here](https://divvy-tripdata.s3.amazonaws.com/index.html) and they're made available by Motivate International, Inc.  under [this license](https://divvybikes.com/data-license-agreement).

<br>

## Background 
<br>

Our fictional company, **Cyclistic**, is a Chicago-based bikesharing service that currently owns and operates a fleet of over **15,000** bicycles, which are linked to roughly **800** bike stations. Cyclistic users are grouped into two categories: annual members and casual riders. Members pay a yearly subscription in exchange for substantial ride discounts, while casual riders are those who either purchase a day pass, or pay per minute as they ride. 

Since 2016, the company's marketing strategy has been to leverage the company's flexible pricing plans to appeal to as many users as possible, with the goal of maximizing revenue. However, a new report from the finance department shows that annual members are by far the most profitable customer segment. 

<br>

Cyclistic's **Chief Marketing Officer** - Lucy Moreno - has thus decided that it would be most advantageous to **convert existing casual users** into annual members, rather than launch a marketing campaign to acquire brand-new users.

However, the marketing team has asked for a little more info on current users to inform their efforts. 

<br>

## Business Tasks

<br>

The CMO would like us to answer ****three main questions**** to guide the upcoming marketing program:

<br>

  > 1. How does casual rider behavior differ from annual member behavior?
 
  > 2. Why would casual riders want to buy an annual membership?
 
  > 3. How can Cyclistic leverage digital media to convert casual users into annual members?

<br>


# Part I: Downloading and Accessing Datasets

<br>

For this project, I decided to analyze a full year of ridesharing data. After taking a look at the ZIP files, I located the data for 2023 and downloaded it: 

![Group 1](https://github.com/mauriciolarroque/Bikeshare-Case-Study/assets/172843436/1c2e4a28-1100-4fc0-80bc-13fd9aa52173)

After the ZIPs were downloaded, I created two separate folders. One would contain the original files for future reference, while the other contained the CSVs that I would directly edit. 
> [!NOTE]
> The data did **not** include information about individual users. Each row simply represented a bike ride that had taken place. Although this meant it was impossible to analyze individual user habits or count the number of members compared to casual riders, this would do little to impact my success in answering the business question of how casual rider behavior differed from member behavior.

<br>

Each one of the 12 datasets (one for each month) contained **13 columns**, which we'll break down below: 

<br>

  1. ```ride_id:``` The unique ID code assigned for each ride.
  2. ```rideable_type:``` Which of the three types of Cyclistic bikes was used (classic, electric, or docked)
  3. ```started_at:``` The day, month, year, hour, and minute that the bike ride started.
  4. ```ended_at:``` The day, month, year, hour, and minute that the bike ride ended.
  5. ```start_station_name:``` The name of the station where the ride began.
  6. ```start_station_id:``` The unique ID of the station where the ride began.
  7. ```end_station_name:``` The name of the station where the ride was completed.
  8. ```end_station_id:``` The unique ID of the station where the ride was completed.
  9. ```start_lat:``` The latitude coordinates associated with the ride's start station.
  10. ```start_lng:``` The longitude coordinates associated with the ride's start station.
  11. ```end_lat:``` The latitude coordinates associated with the ride's end station.
  12. ```end_lng:``` The longitude coordinates associated with the ride's end station.
  13. ```member_casual:``` This column indicates whether the rider was a member or a casual user.

<br>

# Part II: Data Cleaning

<br>

## Excel Data Cleaning 
![Microsoft_Office_Excel_(2019–present) 1](https://github.com/mauriciolarroque/Bikeshare-Case-Study/assets/172843436/09220318-e788-4161-a9fa-16114d470271)


<br>

The first phase of this project was to clean up all the datasets. As a starting point, Excel was the ideal option to carry out the initial cleaning tasks. 

To make sure that all changes were saved, I converted the CSVs into **Excel Workbooks** and went through the following steps in each spreadsheet: 

<br>

  * ### Step 1: Basic Data Cleaning

    * Used ```=COUNTA()``` function to count all records in each column and check for blanks.
    * Used ```=UNIQUE()``` function to spell-check and look through the set of unique values in each column. 
    * Compared ```=UNIQUE(ROWS())``` function with results from the =COUNTA() function to confirm that all **ride_ids** were non-duplicates.
      * This meant that the **ride_id** column was the ```primary key 🔑``` for all of the datasets.
    * Ran ```=TRIM()``` function to get rid of leading or trailing spaces in each column.

      * Results: The ride_id, rideable_type, started_at, ended_at, start_lat, start_lng, and member_casual columns had zero blank values across all datasets.
        * This meant that every ride has its own unique id and there was complete information on which type of bike was used, how long the ride lasted, which coordinates were associated with the ride's start 
          station, and which category of user (member or casual) was linked to each ride.
        * However, in all datasets, there were significant blanks in the columns for the names and ids of both the start and end stations. There were also missing values (albeit fewer) in the coordinate columns for the end stations.
          
          * Given this information, I decided to ```keep the rows with blank values```, as deleting all of them might significantly impact the final analysis. The next step was to replace the blank values with NULLS:

<br>

  * ### Step 2: Handling Blank Values

    * Implemented the **Excel Macro** function (shown below) to replace all blank values with ```NULL```:

<br>

```vb

Sub ReplaceBlanksWithNulls()
    Dim ws As Worksheet
    Dim rng As Range
    Dim cell As Range

    ' Set worksheet (modify to jan_2023, feb_2023, etc. as needed) and range here
    On Error Resume Next
    Set ws = ThisWorkbook.Sheets("jan_2023") 
    On Error GoTo 0
    
    ' Check if worksheet is found
    If ws Is Nothing Then
        MsgBox "Worksheet 'jan_2023' not found. Please check the sheet name.", vbCritical
        Exit Sub
    End If
    
    ' Set the range
    Set rng = ws.UsedRange

    ' Loop through each cell in the range
    For Each cell In rng
        ' Check if the cell is empty or contains only spaces
        If Trim(cell.Value) = "" Then
            cell.Value = "NULL" ' or use NA() or #N/A
        End If
    Next cell
    
    MsgBox "Blanks replaced with 'NULL' successfully.", vbInformation
End Sub
```
      
<br>

  * ### Step 3: Reformatting Dates

    * Changed dates in the **started_at** and **ended_at** columns to ```YYYY-MM-DD HH:MM:SS``` format using this process:
      * Home > General > More Number Formats > Custom > Format as YYYY-MM-DD HH:MM
      * This would make the data **SQL-friendly** for the upcoming analysis.

<br>

 - Due to the fact that I was dealing with massive datasets (containing almost `6 million` rows in total), my laptop was having a hard time handling the spreadsheets. 

 - Therefore, I converted the Excel Workbooks back into their original CSV file format, created a new **local connection** in `MySQL Workbench`, and used `Table Data Import Wizard` to upload each dataset individually.
 
   - Although this took an incredibly long time (more than 24 hours in total), I was able to successfully import all of the records for each month. The next step was to process the data into a format that was easy to work with and streamlined as much as possible for data analysis. 

<br>

# Part III: Data Processing in MySQL

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

<br> 

<br>

<br>

# Part IV: MySQL Data Analysis

<br>

![512px-Antu_mysql-workbench svg 1](https://github.com/mauriciolarroque/Bikeshare-Case-Study/assets/172843436/6db7e63e-5202-4880-a61a-c1de9ac872cc)

<br>

<br>

* Now, we're getting to the fun part of this project.

  * Our original business question was to find out `how member behavior differs from casual rider behavior.` So, to kick things off, let's see `how many rides` are attributed to annual members vs. casual riders.

    * We'll also be taking the percentages of each category of user – as well as dividing the data by quarterly results – to get a more complete picture of what's going on:

<br>

# Creating `Temporary Tables` for Q1-Q4:

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

# Finding Ride Counts by Member Category and % of Total for Each Category (Full Year and Q1-Q4)

<br>

```sql
WITH ride_counts AS
(
SELECT 
    COUNT(CASE WHEN member_casual = "casual" THEN ride_id ELSE NULL END) AS casual,
    COUNT(CASE WHEN member_casual = "member" THEN ride_id ELSE NULL END) AS "member"
FROM cyclistic_2023        -- Modify to Q1, Q2, etc.
)

SELECT 
	casual AS count_of_casual_rides_2023,
        member AS count_of_member_rides_2023,
	ROUND(casual/(casual + member)*100,2) AS percent_casual_rides_2023,
	ROUND(member/(casual + member)*100,2) AS percent_member_rides_2023
FROM ride_counts;
```

<br>
## Results: 

![Frame 4 (2)](https://github.com/mauriciolarroque/Bikeshare-Case-Study/assets/172843436/2d045bfc-e0a7-4457-9e9c-f29c983127f9)


<br>








  



