# Part II: Data Cleaning

<br>

## Excel Data Cleaning 
![Microsoft_Office_Excel_(2019–present) 1](https://github.com/mauriciolarroque/Bikeshare-Case-Study/assets/172843436/09220318-e788-4161-a9fa-16114d470271)


<br>

The first phase of this project was to clean up all the datasets. As a starting point, Excel was the ideal option to carry out the initial cleaning tasks. 

To make sure that all changes were saved, I converted the CSVs into **Excel Workbooks** and went through the following steps in each spreadsheet: 

### Link Here: [Data Cleaning Excel Full Steps](https://github.com/mauriciolarroque/Bikeshare-Case-Study/blob/main/Data-Cleaning.md)

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
 
   - Although this took an incredibly long time (more than 24 hours in total), I was able to successfully import all of the records for each month. The next step was to process the data into a format that was easy to work with and streamlined as much as possible for data analysis:

<br>

<br>

### Link: (Data Processing in MySQL)
