# README for Case Study and Next Solutions

## Table of Contents
1. [Introduction](#introduction)
2. [Case Study](#case-study)
3. [Next Solutions](#next-solutions)
    1. [Solution 1](#solution-1)
    2. [Solution 2](#solution-2)
4. [Usage](#usage)
5. [Files](#files)
6. [Contact](#contact)

## Introduction
This README provides an overview of the provided files, `Case Study.ipynb` and `next solution_1 and solution_2.docx`, detailing their contents and usage instructions.

## Case Study
The `Case Study.ipynb` file is a Jupyter Notebook containing an analysis of a specific business scenario or data set. This file includes data exploration, visualization, and possibly some machine learning or statistical analysis. It is designed to be executed in a Jupyter Notebook environment.

### Contents
- **Data Loading:** How to load and preprocess the data.
- **Data Analysis:** Exploratory data analysis including visualizations.
- **Modeling:** If applicable, this section covers any predictive modeling performed.
- **Conclusion:** Summary of findings and potential recommendations.

## Next Solutions
The `next solution_1 and solution_2.docx` file contains solutions to specific SQL and data processing problems using Google Sheets and BigQuery.

### Solution 1
**Objective:** Extract keywords from URLs in a Google Sheet using SQL (Query Function) and Regex without any formulas.

#### Instructions:
1. **Setup:**
   - Assuming data is in Google Sheets in the following structure:
     - Column A: URL
     - Column B: Count of search results
2. **Query Function:**
   - Insert the following query function in cell C2:
     ```sql
     =QUERY(A2:B, "SELECT A, B, REGEXREPLACE(REGEXEXTRACT(A, 'q=([^&]+)'), '%20', ' ') AS keywords", 1)
     ```
   - This will extract keywords from the URL, replace `%20` with spaces, and display the results along with the original URL and count of search results.

### Solution 2
**Objective:** Process raw data of website visitors using BigQuery to identify the number of users from certain referrer URLs and convert visit start times to IST.

#### Instructions:
1. **Query to Count Users by Referrer URL:**
   ```sql
   SELECT
     IFNULL(h.page.referralPath, 'null') AS referral_path,
     COUNT(DISTINCT fullVisitorId) AS num_users
   FROM
     `bigquery-public-data.google_analytics_sample.ga_sessions_*`
     UNNEST(hits) AS h
   GROUP BY
     referral_path
   ORDER BY
     num_users DESC;
   ```
2. **Query to Convert visitStartTime to IST:**
   ```sql
   SELECT
     fullVisitorId,
     visitId,
     visitStartTime,
     TIMESTAMP_SECONDS(visitStartTime) AS visit_start_time_utc,
     TIMESTAMP_SECONDS(visitStartTime) + INTERVAL 5 HOUR + INTERVAL 30 MINUTE AS visit_start_time_ist
   FROM
     `bigquery-public-data.google_analytics_sample.ga_sessions_*`
   LIMIT 100;
   ```
3. **Combined Query:**
   ```sql
   WITH session_data AS (
     SELECT
       fullVisitorId,
       visitId,
       visitStartTime,
       TIMESTAMP_SECONDS(visitStartTime) AS visit_start_time_utc,
       TIMESTAMP_SECONDS(visitStartTime) + INTERVAL 5 HOUR + INTERVAL 30 MINUTE AS visit_start_time_ist,
       IFNULL(h.page.referralPath, 'null') AS referral_path
     FROM
       `bigquery-public-data.google_analytics_sample.ga_sessions_*`
       UNNEST(hits) AS h
   )
   SELECT
     referral_path,
     COUNT(DISTINCT fullVisitorId) AS num_users,
     MIN(visit_start_time_ist) AS first_visit_ist,
     MAX(visit_start_time_ist) AS last_visit_ist
   FROM
     session_data
   GROUP BY
     referral_path
   ORDER BY
     num_users DESC;
   ```

## Usage
1. **Case Study:**
   - Open the `Case Study.ipynb` in a Jupyter Notebook environment.
   - Run the cells sequentially to reproduce the analysis and visualizations.

2. **Next Solutions:**
   - Open the `next solution_1 and solution_2.docx` file to view the solutions.
   - Follow the instructions provided to implement the SQL queries in Google Sheets and BigQuery.

## Files
- `Case Study.ipynb`: Jupyter Notebook containing the case study analysis.
- `next solution_1 and solution_2.docx`: Document containing SQL queries and solutions for data processing tasks.

