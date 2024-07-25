# Hospital Performance Insight
 
## Project Overview 

This project aims to provide, insights into hospital performance of Massachusetts General Hospital(MGH) over the past year. By analyzing a subset of patients data, i seek to build a high level KPI report for the executive team and also giving stakeholders visibility into the hospitals's recent performance. 

## Audience 

Executive

## Data Sources 

- "patients.csv" : contains detailed information about patient demographic 
- "encounters.csv" : details of patient encounters 
- "organizations.csv" : information about the organisations involved 
- "procedures.csv": details of medical procedures performed"
- "payers.csv" : insurance and coverage information

## Data Structure
- Multiple Tables: 5
- Number of Records: 75,592
- Number of Fields: 55
  
## Tool used for the analysis 
PowerBI- for cleaning, analyzing and building reports. 

## Data Preparation 

The data was mostly clean,so there was not much cleaning required. However, the following steps were taken to ensure data validity:
1. Data loading and inspection
2. Changed type of some columns
3. Removed unnecessary columns
4. Handled missing values: There were about 15% missing valus in the patient location data, but these missing values did not significantly affect the analysis, so they were left as it was.
5. Created a date table using Dax to facilitate time-based analysis.

## Creating a Date Table Using DAX
The date was created using the following Dax code. 

```dax
DateTable = ADDCOLUMNS(
    CALENDAR(DATE(2011,1,1), DATE(2022,12,31)),
"Month", FORMAT([Date],"MMM"),
"Month Number", MONTH([Date]),
"Year", YEAR([Date]),
"Day Name", FORMAT([Date],"ddd"),
"Days", 1
)
```
## Data Modelling 
The star schema method was to structure the data model. The main fact tables are "encounters" and "procedures". 

<img width="400" alt="snapshot" src="https://github.com/user-attachments/assets/401d2e86-c5ff-434f-b19c-a62031eeb908">


## Exploratory Data Analysis
EDA involved exploring the dataset to answer key questions such as: 
- How many patients have been admitted or readmitted over time?
- how long are patients staying in the hospital, on average?
- how much is the average cost per visit?
- how many procedures are covered by insurance?
- what percentage of the total patients were readmitted during the specific period?
- how many procedures were performed during the specific period?
- what is the total amount covered by insurance for the patients treated?
- which five cities had the highest number of patients admitted to the hospital?

## Data Analysis 
1. How many Patients have been admitted and readmitted over time?

paients admitted
```
Total Patients = DISTINCTCOUNT(encounters[PATIENT])
```
patients readmitted

First create a calculated column to get multiple admission
```dax
Multiple Admissions = 
VAR CurrentPatient = encounters[PATIENT]
VAR AdmissionsCount = 
    CALCULATE(
        COUNTROWS(encounters),
        FILTER(encounters, encounters[PATIENT] = CurrentPatient)
    )
RETURN
    IF(AdmissionsCount > 1, 1, 0)
```
Then dax to calculate readmission
```dax
Readmission = 
CALCULATE(
    DISTINCTCOUNT(encounters[PATIENT]),
    FILTER(Encounters, [Multiple Admissions] = 1)
)
```
2. How long are patients staying in the hospital on average
   first create a calculated column using dax to get the length of stay of patient
   ```dax
   Length_of_stay = DATEDIFF([START],[STOP].[Date],HOUR)
   ```
   Average of length of stay in hours
   ```dax
   Avg_Length = AVERAGE([Length_of_stay])
   ```
3. How much is the average cost per visit
   ```dax
   avg_base = AVERAGE(encounters[TOTAL_CLAIM_COST])
   ```
4. How many Procedures are covered by insurance
   ```dax
   ProceduresCoveredByIns = CALCULATE(COUNTROWS(procedures),FILTER(RELATEDTABLE(encounters),encounters[PAYER_COVERAGE]>0))
   ```
5. How many procedures were performed during the specific period
   ```
   Total Procedures = CALCULATE(COUNTROWS(procedures))
   ```
6. What is the total amount covered by insurance for the patients treated?
   ```
   Total_Insurace = SUM(encounters[PAYER_COVERAGE])
   ```
7.  which five cities had the highest number of patients admitted to the hospital?
   <img width="237" alt="city" src="https://github.com/user-attachments/assets/45ff4610-0716-4700-b569-63c77e41dee4">
     

