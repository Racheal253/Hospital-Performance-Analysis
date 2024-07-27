# Hospital Performance Insights

## Table Of Contents

- [Project Overview](#project-overview)
- [Audience](#audience)
- [Data Sources](#data-sources)
- [Data Structure](#data-structure)
- [Tool used for the analysis](#tool-used-for-the-analysis)
- [Data Preparation](#data-preparation)
- [Creating a Date Table Using DAX](#creating-a-date-table-using-dax)
- [Data Modelling](#data-modelling)
- [Exploratory Data Analysis](exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Key Findings](#key-findings)
- [Recommendations](#recommendations)
 
## Project Overview 

This project aims to provide, insights into hospital performance of Massachusetts General Hospital(MGH) over the past year. By analyzing a subset of patients data, i seek to build a high level KPI report for the executive team and also giving stakeholders visibility into the hospitals's recent performance. 


![Hospital Report](https://github.com/user-attachments/assets/87b5d5df-5382-46c9-b05b-5b5411161bbe)


[Dashboard Link](https://app.powerbi.com/links/Gm3IEyatVB?ctid=04ade39f-5160-4faa-bcff-26d243a2b402&pbi_source=linkShare)

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

<img width="610" alt="snapshot" src="https://github.com/user-attachments/assets/bede9991-eaf4-4025-a7be-378a4923069c">

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
     
## Key Findings
The analysis results are summarized as follows 
1. The total number of patients from 2011 to 2022 is 974, with PYTD at 649 and YOY at -84.13%. Patients readmitted over this period totaled 854, with PYTD at 636 and YOY at -83.81%. Total admissions are approximately 28,000, with PYTD at 3,531 and YOY at -93.9%.
2. In 2021, there was an increase in the number of patients, which resulted in more readmitted patients and admissions.However, there was a noticeable decrease in procedures, but the procedures covered by insurance increased, leading to a decrease in the average cost per visit.
3. In 2022, there was a drastic drop in hospital performance, which led to an increase in the average cost per visit due to higher procedure costs. This increase in procedure costs might have contributed to the decrease in patient numbers.
4. The cities with the highest patient numbers are Boston with 541 patients, followed by Quincy, Cambridge, Revere, and Chelsea.
   
## Recommendations 
1. Investigate Causes of Patient Decrease:
   - Conduct a thorough analysis to understand the reasons behind the significant drop in patient numbers from 2011 to 2022. Identify any external factors (e.g., changes in healthcare policies, economic conditions) or internal factors (e.g., hospital service quality, patient satisfaction) contributing to this trend.
2. Improve Patient Retention:
   - Develop and implement strategies to improve patient retention and readmission rates. This could include enhancing patient care quality, follow-up procedures, and patient engagement programs to ensure better patient outcomes and satisfaction.
3. Optimize Procedure Costs:
   - Review and optimize the costs of procedures to make them more affordable for patients. Explore ways to reduce operational costs without compromising the quality of care, and negotiate better rates with insurance providers.
4. Enhance Insurance Coverage:
   - Work with insurance companies to increase the number of procedures covered by insurance. This could help lower the out-of-pocket expenses for patients and potentially increase the number of patients seeking care at the hospital.
5. Focus on High-Impact Areas:
   - Given the high number of patients in Boston, Quincy, Cambridge, Revere, and Chelsea, prioritize outreach and marketing efforts in these areas. Tailor healthcare services to meet the specific needs of these communities to attract and retain more patients.
6. Increase Awareness and Accessibility:
   - Increase awareness of the hospital’s services and improve accessibility for patients. This could include community outreach programs, partnerships with local healthcare providers, and expanding telemedicine services to reach more patients.
7. Monitor and Adapt to Trends:
   - Continuously monitor patient trends, procedure costs, and insurance coverage. Use data-driven insights to adapt strategies and make informed decisions to improve hospital performance and patient satisfaction.

 
