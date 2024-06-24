
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

<br>

<br>

<br>

<br>

<br>

# Part II: Data Cleaning

<br>


## Excel Data Cleaning 
![Microsoft_Office_Excel_(2019–present) 1](https://github.com/mauriciolarroque/Bikeshare-Case-Study/assets/172843436/09220318-e788-4161-a9fa-16114d470271)


<br>

The first phase of this project was to clean up all the datasets. As a starting point, Excel was the ideal option to carry out the initial cleaning tasks. 

To make sure that all changes were saved, I converted the CSVs into **Excel Workbooks** and went through the following steps in each spreadsheet: 

### Link Here: [Data Cleaning Excel Full Steps](https://github.com/mauriciolarroque/Bikeshare-Case-Study/blob/main/Data-Cleaning.md)

<br>

<br>

<br>

<br>

<br>

<br>

# Part III: Data Processing in MySQL

<br>

![Sql_data_base_with_logo 1](https://github.com/mauriciolarroque/Bikeshare-Case-Study/assets/172843436/c198b878-c74d-4157-bc2c-6130ec576e3b)

<br>

> [!IMPORTANT]
> For a `complete breakdown` of all the different data processing, data manipulation and data definition tasks I carried out in MySQL, **[check out](README2.md) this page here.**
   
<br>

<br>

<br>

<br>

<br>

<br>

<br>

<br>

<br>

<br>


# Part IV: MySQL Data Analysis

<br>

![512px-Antu_mysql-workbench svg 1](https://github.com/mauriciolarroque/Bikeshare-Case-Study/assets/172843436/6db7e63e-5202-4880-a61a-c1de9ac872cc)

<br>

<br>

  * Our original business question was to find out `how member behavior differs from casual rider behavior.` So, to kick things off, let's see `how many rides` are attributed to annual members vs. casual riders.

    * We'll also be taking the percentages of each category of user – as well as dividing the data by quarterly results – to get a more complete picture of what's going on:

<br>

<br>

## Count of Annual Member vs. Casual User Rides (Full Year and Q1-Q4)

<br>

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

## Output: 

| count_of_casual_rides_2023 | count_of_member_rides_2023  | percent_casual_rides_2023 | percent_member_rides_2023 |
|----------------------------|-----------------------------|---------------------------|---------------------------|
| 2059106                    | 3660637                     | 36.00                     | 64.00                     |
--------------------------------------------------------------------------------------------------------------------


| count_of_casual_rides_q1   | count_of_member_rides_q1    | percent_casual_rides_q1   | percent_member_rides_q1   |
|----------------------------|-----------------------------|---------------------------|---------------------------|
| 145225                     | 494198                      | 22.71                     |  77.29                    |
--------------------------------------------------------------------------------------------------------------------


| count_of_casual_rides_q2   | count_of_member_rides_q2    | percent_casual_rides_q2   | percent_member_rides_q2   |
|----------------------------|-----------------------------|---------------------------|---------------------------|
| 682694                     | 1068335                     | 38.99                     |  61.01                    |
--------------------------------------------------------------------------------------------------------------------

| count_of_casual_rides_q3   | count_of_member_rides_q3    | percent_casual_rides_q3   | percent_member_rides_q3   |
|----------------------------|-----------------------------|---------------------------|---------------------------|
| 904086                     | 1301567                     | 40.99                     |  59.01                    |
--------------------------------------------------------------------------------------------------------------------


| count_of_casual_rides_q4   | count_of_member_rides_q4    | percent_casual_rides_q4   | percent_member_rides_q4   |
|----------------------------|-----------------------------|---------------------------|---------------------------|
| 327101                     | 796537                      | 29.11                     |  70.89                    |
--------------------------------------------------------------------------------------------------------------------

<br>

* The first thing that jumps out is the fact that there are more member rides than casual rides.
  
* There is also a noticeable spike in `casual` user rides during the spring and `summer` months.

  * Although the number of member rides also increases – and the number of member rides indeed remains greater than casual rides throughout each quarter – this difference is notably less pronounced. Likewise, the percentages show that the gap between member and casual usage significantly narrows during Q2 and Q3. 

    * This suggests that `seasonality` plays a key role in the difference between casual rider and member behavior; members make up the bulk of winter/fall bike rides, while casual users become much more active during tourist season.
   
<br>

#### Next, let's review summary statistics for the whole table:

<br>

<br>

## Max, Min, and Mean Values for Members vs. Casual Riders

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

### Output (Scroll Right): 

| member_casual| rideable_type   | avg_ride_min | avg_ride_distance | max_ride_min | max_ride_hours | min_ride_min |min_ride_hours | max_ride_distance | min_ride_distance |
|--------------|-----------------|--------------|-------------------|--------------|----------------|--------------|---------------|-------------------|-------------------|
| member       | classic_bike    | 13.93        | 1.18              | 1560         | 26.0000        | 0            | 0             | 16.03             | 0
| member       | electric_bike   | 11.14        | 1.43              | 482          | 8.0333         | 0            | 0             | 25.37             | 0
| casual       | classic_bike    | 32.06        | 1.29              | 1560         | 26.0000        | 0            | 0             | 20.56             | 0
| casual       | docked_bike     | 182.81       | 1.35              | 98489        | 1641.4833      | 0            | 0             | 19.03             | 0
| casual       | electric_bike   | 14.26        | 1.34              | 480          | 8.0000         | 0            | 0             | 30.4              | 0 


<br>

<br>

 * On average, we can see that `casual` users ride about `twice` as long as member users when it comes to classic bikes. On electric bikes, there was only a slight difference between both groups. 

   * In terms of `miles traveled`, both members and casual riders travel about the same distance on an average trip.


<br>

* Now, let's calculate the `median` data for ride duration: 
  
<br>

## Median for Ride Duration

<br>

```sql
-- Change casual to member in WHERE clause to return member ride medians
-- Change ride_duration_min to ride_miles to get ride_miles median

(SELECT MAX(ride_duration_min) AS "median"
FROM (
 SELECT ride_duration_min,
 NTILE(4) OVER(ORDER BY ride_duration_min) AS quartile 
 FROM temp_q1  
 WHERE member_casual = "casual"    -- Change to "member" as needed
) median_table
WHERE quartile = 2)
UNION ALL 
(SELECT MAX(ride_duration_min) AS "median"
FROM (
 SELECT ride_duration_min,
 NTILE(4) OVER(ORDER BY ride_duration_min) AS quartile 
 FROM temp_q2  
 WHERE member_casual = "casual"    -- Change to "member" as needed 
) median_table
WHERE quartile = 2)
UNION ALL 
(SELECT MAX(ride_duration_min) AS "median"
FROM (
 SELECT ride_duration_min,
 NTILE(4) OVER(ORDER BY ride_duration_min) AS quartile 
 FROM temp_q3  
 WHERE member_casual = "casual"    -- Change to "member" as needed 
) median_table
WHERE quartile = 2)
UNION ALL
(SELECT MAX(ride_duration_min) AS "median"
FROM (
 SELECT ride_duration_min,
 NTILE(4) OVER(ORDER BY ride_duration_min) AS quartile 
 FROM temp_q4   
 WHERE member_casual = "casual"    -- Change to "member" as needed 
) median_table
WHERE quartile = 2);
```

<br>

* During Q2 and Q3 (spring and summer), the `median` ride time for `casual` users was 13 minutes, while it was `8` minutes for Q1 and `10` minutes for Q4.

  * The median results for members were slightly less, with 7 minutes in Q1, 9 minutes in Q2 and Q3, and 8 minutes in Q4.

* Both members and casual users show a trend of increasing ride time towards the summer months, but `casual` users have longer ride times throughout the year. 
    
<br>

<br>

### * Now that we've gotten these insights, it would also be helpful to know which days of the week are most popular with members vs. casual users.

<br>

* This will give us a better sense of when they choose to go on rides and whether they use them for work or for recreational purposes: 

<br> 

## Count of Member vs. Casual Rides Per Weekday

<br>

<br>

```sql
WITH ride_counts AS (
    SELECT
        member_casual,
        ride_day,
        COUNT(ride_id) AS number_of_rides
    FROM cyclistic_2023
    GROUP BY member_casual, ride_day
),
		-- This CTE counts the number of rides during each weekday

total_rides AS (
    SELECT
        member_casual,
        SUM(number_of_rides) AS total_rides
    FROM ride_counts
    GROUP BY member_casual
)

	-- This CTE counts the total number of rides for each member category

SELECT
    rc.member_casual,
    rc.ride_day,
    rc.number_of_rides,
    tr.total_rides,
    ROUND((rc.number_of_rides * 100.0 / tr.total_rides),2) AS percent_of_total_rides
FROM ride_counts rc
JOIN total_rides tr                        -- We'll use a JOIN statement so we can get the percentage of rides on each weekday
    ON rc.member_casual = tr.member_casual
ORDER BY rc.member_casual, rc.ride_day; 
```

<br>

### Output (Full-Year 2023):

| member_casual | ride_day | number_of_rides | total_rides    | percent_of_total_rides  |
|---------------|----------|-----------------|----------------|-------------------------|
| member        | Fri      | 531593          | 3660637        | 14.52                   |
| member        | Mon      | 494571          | 3660637        | 13.51                   |
| member        | Sat      | 472855          | 3660637        | 12.92                   |
| member        | Sun      | 408835          | 3660637        | 11.17                   |
| member        | Thu      | 589582          | 3660637        | 16.11                   |
| member        | Tue      | 576750          | 3660637        | 15.76                   |
| member        | Wed      | 586451          | 3660637        | 16.02                   |
| casual        | Fri      | 311914          | 2059106        | 15.15                   |
| casual        | Mon      | 234824          | 2059106        | 11.40                   |
| casual        | Sat      | 410693          | 2059106        | 19.95                   |
| casual        | Sun      | 335684          | 2059106        | 16.30                   |
| casual        | Thu      | 270608          | 2059106        | 13.14                   |
| casual        | Tue      | 246222          | 2059106        | 11.96                   |
| casual        | Wed      | 249161          | 2059106        | 12.10                   |

<br>

### Our results show us a few interesting insights:

<br>

* While `24.09%` of member rides occur on the weekend (Saturday and Sunday), that figure rises to `36.25%` among casual riders. 

  * Saturday and Sunday were the `least` active days for members, while casual users were `most` active on these two days.

<br>

<br>

<br>


<br>

 * When adjusted for `Q1` ([Q1 data here](Q1-01.md)), Sunday was the most popular ride day for `casual` users, but Saturday was surprisingly outperformed by Tuesday and Wednesday.

   * This raises the possibility that casual users in the off-season tend to use bike rides for their routine commute more often than for recreational purposes.

   * Meanwhile, member users also reflect this tendency; Tuesday and Wednesday were their most active days, while Saturday was their `least` popular day. 

<br>

* During `Q2` and `Q3`, however, we can see an uptick in the number of weekend rides among both member and casual users, although the increase is significantly more prominent among casual users.

  * `35.9%` of casual rides in Q2 took place on the weekend, and this number steadily increased to `38.2%` during Q3.

<br>

To dive deeper into these trends, we can use the query below to figure out which `hours of the day` are most popular to start rides: 

<br>

<br>

<!--  * Overall, we can deduce from this data that `weekends` were most popular with casual riders (particularly during the spring and summer months) while weekdays were the most popular ride days for members. -->

## How Many Rides per Member Category Occur at Different Times of Day

<br>

```sql
WITH ride_hour_counts AS (
SELECT
	member_casual,
    ride_hour,
   COUNT(ride_id) AS number_of_rides
FROM cyclistic_2023
WHERE ride_month IN ("01", "02", "03")
GROUP BY member_casual, ride_hour
ORDER BY  member_casual, ride_hour),

total_rides AS (
SELECT 
	member_casual,
	SUM(number_of_rides) AS total_rides 
FROM ride_hour_counts
GROUP BY member_casual)

SELECT 
	rhc.member_casual,
    rhc.number_of_rides,
    ride_hour,
    tr.total_rides,
    ROUND((rhc.number_of_rides/tr.total_rides *100),2) AS ride_count_percentage
FROM ride_hour_counts rhc
JOIN total_rides tr ON rhc.member_casual = tr.member_casual
ORDER BY rhc.member_casual, rhc.ride_hour;


-- QUERY: Insights on Hourly Percentages 

WITH ride_hour_counts AS (
SELECT
	member_casual,
    ride_hour,
   COUNT(ride_id) AS number_of_rides
FROM cyclistic_2023
WHERE ride_month BETWEEN "06" AND "08"
GROUP BY member_casual, ride_hour
ORDER BY  member_casual, ride_hour),

total_rides AS (
SELECT 
	member_casual,
	SUM(number_of_rides) AS total_rides 
FROM ride_hour_counts
GROUP BY member_casual),

hourly_percentages AS (
SELECT 
	rhc.member_casual,
    rhc.number_of_rides,
    ride_hour,
    tr.total_rides,
    (rhc.number_of_rides/tr.total_rides *100) AS ride_count_percentage
FROM ride_hour_counts rhc
JOIN total_rides tr ON rhc.member_casual = tr.member_casual
ORDER BY rhc.member_casual, rhc.ride_hour
) 

SELECT 
	member_casual,
	ROUND(SUM(ride_count_percentage),1) AS percentage
FROM 
	hourly_percentages
WHERE ride_hour IN ("22", "23", "00", "01", "02")
GROUP BY member_casual;
```

<br>

<br>

* During morning commute hours (6:00AM to 8:59AM), `7%` of casual users started rides, compared to `14.4%` of members. Members also had a slighty higher percentage of rides from 4:00pm to 5:59PM, which is the end of a typical workday. 

  * From 11:00AM to 4:59 PM, `40.3%` of rides started were casual users, while members lagged slightly behind at `35.2%`

    * Casual users also seemed to favor `late night` rides. `10%` started rides between 10:00PM and 2:59AM, compared to `6.4%` in the member category.


<br>

* Overall, we can deduce from this data that `weekends` were most popular with casual riders, weekdays were most popular with member riders, casual riders tend to ride bikes more often outside of working hours, and members are more active than casual riders during the times that people usually `commute` to and from work.

<br>

<br>

* Finally, let's find out which start stations are most popular with members and casual users: 

<br>

<br> 

## Top 10 Start Stations for Each User Category

<br>

```sql

-- This query returns the top 10 start stations associated with the most ride ids

SELECT 
	start_station_name,
    COUNT(ride_id) AS "number_of_rides"
FROM 
	(SELECT
		start_station_name, 
        ride_id,
        member_casual
	FROM cyclistic_2023
	WHERE start_station_name IS NOT NULL
    AND member_casual = "casual"    -- Change to member for member results
	) start_stations
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10;
```

<br>

### Casual User Output: 
 
 |           start_station_name       | number_of_rides |
 -------------------------------------|------------------
 |Streeter Dr & Grand Ave             | 46030
 |DuSable Lake Shore Dr & Monroe St   | 30487
 |Michigan Ave & Oak St               | 22664
 |DuSable Lake Shore Dr & North Blvd  | 20338
 |Millennium Park                     | 20227
 |Shedd Aquarium                      | 17781
 |Theater on the Lake                 | 16359
 |Dusable Harbor                      | 15491
 |Wells St & Concord Ln               | 12171
 |Montrose Harbor                     | 11987



<br>

### Map View: Streeter Dr. & Grand Ave. 

![Screenshot 2024-06-20 at 5 15 05 PM 1](https://github.com/mauriciolarroque/Bikeshare-Case-Study/assets/172843436/d2b2f829-bc5b-4f86-948d-e101b1a56b8c)


<br>

* As we can see, the most popular station for casual users (`Streeter Dr. & Grand Ave.`) is a short bike ride away from `tourist` attractions such as the Navy Pier, the Chicago River, city parks and several local eateries. All of the other start stations are also near the waterfront, near hotels or next to other popular tourist attractions throughout the city.

  * Now, let's check out results for `members`:

<br>

| start_station_name                    | number_of_rides |
|---------------------------------------|-----------------|
| Clinton St & Washington Blvd          | 26216           |
| Kingsbury St & Kinzie St              | 26172           |
| Clark St & Elm St                     | 25001           |
| Wells St & Concord Ln                 | 21419           |
| Clinton St & Madison St               | 20596           |
| Wells St & Elm St                     | 20400           |
| University Ave & 57th St              | 20038           |
| Broadway & Barry Ave                  | 18959           |
| Loomis St & Lexington St              | 18901           |
| State St & Chicago Ave                | 18485           |

<br>

### Map View: Clinton St & Washington Blvd 

![Screenshot 2024-06-20 at 5 11 11 PM 2](https://github.com/mauriciolarroque/Bikeshare-Case-Study/assets/172843436/79913b65-d07c-49e7-8691-1b33ecd165d7)


<br>

* The top start station for members (`Clinton St. & Washington Blvd`) is located near the freeway and several commercial buildings. Unlike casual users, rides that are attributed to members don't have the same tendency to circulate around tourist areas. It seems as if their rides are for more routine purposes, such as running errands, getting home, and going to work.

<br>

<br>
  






  



