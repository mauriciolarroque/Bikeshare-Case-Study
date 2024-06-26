
![Frame 2(1)](https://github.com/mauriciolarroque/Bikeshare-Case-Study/assets/172843436/96fe9b02-8e7d-4605-b85b-16f91240479e)


# Bikesharing Data Analysis Project: Excel, SQL & Tableau



## Overview 

<br>

This case study from the `Google Data Analytics Certificate program` explores the 2023 data of Cyclistic: a `fictional` **bikesharing** company. 


This project will use **Microsoft Excel, MySQL** and **Tableau**  for all data cleaning, data processing, data analysis and data visualization tasks. Its aim will be to answer the business questions of a hypothetical stakeholder at this fictional company. All of the datasets I used are stored [here](https://divvy-tripdata.s3.amazonaws.com/index.html) and they're made available by Motivate International, Inc.  under [this license](https://divvybikes.com/data-license-agreement).

<br>

## Background 
<br>

Our fictional company, `Cyclistic`, is a Chicago-based bikesharing service that currently owns and operates a fleet of over **15,000** bicycles, which are linked to roughly **800** bike stations. 

Cyclistic users are grouped into two categories: `annual members and casual riders`. 

`Members` pay a yearly subscription in exchange for substantial ride discounts, while `casual` riders are those who either purchase a day pass, or pay per minute as they ride. 

Since 2016, the company's marketing strategy has been to offer flexible pricing plans to appeal to as many users as possible. However, a new report from the finance department shows that annual members are by far the most profitable customer segment. 

<br>

Thus, Cyclistic's **Chief Marketing Officer** - Lucy Moreno - wants to `convert existing casual users` into annual members, which she believes is much easier than acquiring brand-new users with a big marketing campaign.

However, the marketing team wants to know more about `how` casual users behave compared to members, which might hold the key to converting them into annual members. 

<br>

That's where we come in. 

<br>

Our job is to look through the data and see if we can find any insights that will help the marketing team turn more casual riders into paying annual members. 

<br>

## Business Tasks

<br>

The CMO has ****three main questions**** she wants us to answer:

<br>

  > 1. How does casual rider behavior compare and contrast to annual member behavior?
 
  > 2. What reasons do casual riders have to want to purchase an annual membership?
 
  > 3. How can Cyclistic use digital media to convert casual users into annual members?

<br>


# Part I: Downloading and Accessing Datasets

<br>

To get started, I downloaded all of the datasets for Cyclistic's `2023` data:

![Group 1](https://github.com/mauriciolarroque/Bikeshare-Case-Study/assets/172843436/1c2e4a28-1100-4fc0-80bc-13fd9aa52173)

After the ZIPs were downloaded, I made two separate folders. One had the original files for future reference, while the other had the CSVs I would be editing and cleaning. 
> [!NOTE]
> The data did **not** include information about individual users. Each row just represented a bike ride that had taken place, and a column called `member_casual` indicated whether the person who went on that ride was a member or a casual user. Thus, I was unable to analyze individual user habits or count the number of members compared to casual riders, which introduced some limitation to the analysis. 

<br>

Each one of the 12 datasets (one for each month) had the exact same **13 columns**. Here's a full breakdown of them:  

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

The first part of this project was to clean up all the datasets. As a starting point, Excel was the most practical option to do the initial data cleaning. 

After converting the CSVs into **Excel Workbooks**, I went through the following steps in each spreadsheet: 

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

  * Due to the extensive nature of the data analysis that was carried out in **MySQL**, I've attached a `link below` that contains the entire, comprehensive walkthrough of how I retrieved the data:

<br>

### [Link Here] to MySQL Data Analysis:](MySQL.md)

<br>

For now, to save some time and space, here are `all` the summarized `key insights` I got from SQL:

<br>

<br>

## Key Insights I: Number of Total Rides in 2023  

   1. Throughout all of 2023, more rides were attributed to `members` than casual users.
      
   2. `Casual` rides increased sharply during the spring and summer.

   3. Member rides made up the bulk of all total rides during the `winter` and fall months.

<br>

<br>

## Key Insights II: Summary Statistics   

  1. On average, casual rides on `classic` bikes were roughly `twice` as long as member rides.

  2. On electric bikes, ride duration was about the same for both member categories.

  3. Both members and casual users traveled about the same average distance on a single trip. 

  4. There were `significant` outliers with regards to docked bikes, which would have to be investigated further with the Cyclistic team before arriving at any conclusions.

<br>

<br>

## Key Insights III: Median Ride Times

  1. The median ride times for member and casual users revealed `longer` ride times for casual users than members.

  2. The medians of `both` member and casual rides `increased` during the spring and summer months.

<br>

<br>

## Key Insights IV: Which Days of the Week Were Most Popular 

  1. **Saturday** was most popular with `casual` users.

  2. **Thursday** was most popular with `members`.

  3. `Casual` users were more likely to ride on the weekend, while weekdays were most popular with members.

  4. Both member and casual weekend rides increased from Q2 to Q3.

  5. <br>

  <br>

### Key Insights V: Which Hours of the Day Were Most Popular

  1. `Casual` riders went on more `late night` rides (10PM-3AM) more than members.

  2. Members were more active than casual users during typical `morning commute` hours.

  3. Casual users went on more rides during typical working hours.

  4. `Members` went on slightly more rides than casual users from 5PM to 6PM, which is when people usually leave their 9-5.

<br>

<br>

### Key Insights VI: Most Popular Start Stations 

  1. `Member` rides were more active around `commercial areas`, which supports the previous insight that they may have been primarily for routine commuting. 

  2. `Casual `rides tended to start around some of the city's popular tourist centers, which suggests that these rides are primarily `recreational.`



