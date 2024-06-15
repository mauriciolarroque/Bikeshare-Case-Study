
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

  * ### Step 1: Assessing Data Structure

    * Used ```=COUNTA()``` function to count all rows in each column.
    * Used ```=UNIQUE(ROWS())``` function to confirm that all **ride_ids** were unique values (thus making **ride_id** our primary key).
      * Results: The ride_id, rideable_type, started_at, ended_at, start_lat, start_lng, and member_casual columns had zero blank values across all datasets.
        * This meant that every ride has its own unique id and there was complete information on which type of bike wa sused, how long the ride lasted, which coordinates were associated with the ride's start 
          station, and which category of user (member or casual) was linked to each ride.
        * However, in all datasets, there were significant blanks in the columns for the names and ids of both the start and end stations. There were also missing values (albeit fewer) in the coordinate columns for         the end stations.
          
          * Given this information, I decided to ```keep the rows with blank values```, as deleting all of them might significantly impact the final analysis. The next step was to replace the blank values with                NULLS:

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
   



