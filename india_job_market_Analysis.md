# SQL-For-Data-Analysis
Here is all my SQL with MYSQL projects for data analysis and data science

SELECT * FROM pro.india_job_market_dataset;
use pro;

1. Basic SQL Questions
These help understand data structure and perform simple queries.

Basic Exploration

1) List all unique job titles available in the dataset.
   
SELECT DISTINCT "Job Title" FROM jobs ;

2) Find the total number of job postings in the dataset.

SELECT COUNT(*) AS total_jobs FROM jobs;


3) List the top 5 job postings with the most applicants.

SELECT * FROM jobs ORDER BY "Number of Applicants" DESC LIMIT 5;


4) Find the average number of applicants per job posting.

SELECT AVG("Number of Applicants") AS avg_applicants FROM jobs;

5) Count the number of postings by each job type
SELECT `Job Type`, COUNT(*) AS count 
FROM jobs GROUP BY `Job Type`;

2. Intermediate Analytical Questions
These involve grouping, joining, and slightly complex queries.
Grouping and Aggregations

6) Find the number of job postings for each company.
SELECT "Company Name", COUNT(*) AS job_count 
FROM jobs 
GROUP BY "Company Name";

7) Calculate the average salary range for jobs requiring more than 5 years of experience.
SELECT "Experience Required", AVG(CAST(SUBSTRING_INDEX("Salary Range", '-', 1) AS UNSIGNED)) AS avg_salary 
FROM jobs 
WHERE "Experience Required" LIKE '%5-10 years%'
GROUP BY "Experience Required";

8) Get all job postings in "jaipur" offering salaries above 20 LPA.
SELECT * 
FROM jobs 
WHERE `Job Location` = 'jaipur' 
  AND `Salary Range` LIKE '20+ LPA';
  


9) Calculate the average salary range for jobs requiring more than 5 years of experience.
SELECT `Experience Required`, AVG(CAST(SUBSTRING_INDEX("Salary Range", '-', 1) AS UNSIGNED)) AS avg_salary 
FROM jobs 
WHERE `Experience Required` LIKE '5-10 years'
GROUP BY `Experience Required`;

Filtering on Dates
11) Find job postings with deadlines in the next 10 days.
SELECT * 
FROM jobs 
WHERE DATE(`Application Deadline`) <= DATE_ADD(CURDATE(), INTERVAL 10 DAY);

Skill Analysis
12) List the most required skills across all job postings.
SELECT `Skills Required`, COUNT(*) AS skill_count 
FROM jobs 
GROUP BY `Skills Required` 
ORDER BY skill_count DESC;

3. Advanced Analysis & Forecasting
These questions use advanced SQL techniques like window functions or integration with predictive analytics.
Window Functions

13) Rank job postings based on the number of applicants.
SELECT *, RANK() OVER (ORDER BY `Number of Applicants` DESC) AS rank
FROM jobs;

Trend Analysis

14) Analyze the trend in job postings over time.
SELECT YEAR(`Posted Date`) AS year, MONTH(`Posted Date`) AS month, COUNT(*) AS job_count 
FROM jobs 
GROUP BY year, month 
ORDER BY year, month;

Forecasting

15) Predict the number of applicants for upcoming jobs based on historical data:
This typically involves exporting data for use in tools like Python or R, but SQL prep might look like:

SELECT "Job Title", `Experience Required`, `Skills Required`, AVG(`Number of Applicants`) AS avg_applicants
FROM jobs 
GROUP BY `Job Title`, `Experience Required`, `Skills Required`;

Performance Comparison

16) Compare the number of applicants for jobs in remote vs. onsite roles.
SELECT `Remote/Onsite`, AVG(`Number of Applicants`) AS avg_applicants 
FROM jobs 
GROUP BY `Remote/Onsite`;


Skill-Based Forecasting

17) Predict the demand for a skill like "Python" by analyzing historical patterns:
SELECT `Skills Required`, COUNT(*) AS skill_demand, YEAR(`Posted Date`) AS yearr
FROM jobs 
WHERE `Skills Required` LIKE '%Python%'
GROUP BY yearr 
ORDER BY yearr;


Self-Join Based Questions

18) Jobs with Same Skills and Different Companies
Find jobs that require the same skills but are offered by different companies

SELECT a.`Job Title` AS job_a, b.`Job Title` AS job_b, a.`Skills Required`
FROM jobs a
JOIN jobs b
  ON a.`Skills Required` = b.`Skills Required`
 AND a.`Company Name` != b.`Company Name`;



19)  Salary Comparison Between Jobs with Similar Experience
Compare the salary ranges of jobs requiring the same experience but in different job types.

 SELECT a.`Job Title` AS job_a, b.`Job Title` AS job_b, a.`Experience Required`, a.`Salary Range` AS salary_a, b.`Salary Range` AS salary_b
FROM jobs a
JOIN jobs b
  ON a.`Experience Required` = b.`Experience Required`
 AND a.`Job Type` != b.`Job Type`;


Finding Overlapping Deadlines

20) List job postings where the application deadlines overlap.
SELECT a.`Job ID` AS job_a, b.`Job ID` AS job_b, a.`Application Deadline`
FROM jobs a
JOIN jobs b
  ON a.`Application Deadline` = b.`Application Deadline`
 AND a.`Job ID` != b.`Job ID`;

 Subquery-Based Questions
Jobs with Above-Average Applicants

21) Retrieve all jobs with more applicants than the average number of applicants.

 SELECT * FROM jobs WHERE `Number of Applicants` > (SELECT AVG(`Number of Applicants`) FROM jobs);


22) Finding Companies with the Most Job Postings
List companies with more than 5 job postings.

 SELECT `Company Name`
FROM jobs
GROUP BY `Company Name`
HAVING COUNT(*) > 5;


23) Highest Paying Job for Each Experience Level
Find the highest salary range for each "Experience Required" category.

SELECT `Job Title`, `Experience Required`, `Salary Range`
FROM jobs
WHERE `Salary Range` IN (
    SELECT MAX(`Salary Range`)
    FROM jobs
    GROUP BY `Experience Required`
);


24) Most Popular Job Location
Identify the job location with the highest number of postings.

SELECT `Job Location`
FROM jobs
GROUP BY `Job Location` ORDER BY COUNT(*) DESC LIMIT 1;


25) Skill Demand Analysis
Find the top 3 most in-demand skills

SELECT `Skills Required`, COUNT(*) AS skill_count
FROM jobs
GROUP BY `Skills Required` ORDER BY skill_count DESC LIMIT 3;


Advanced Use of Subqueries with Aggregation

26) Jobs with Above-Median Applicants Retrieve jobs where the number of applicants is greater than the median.
    
SELECT AVG(`Number of Applicants`) AS median_value
FROM (
    SELECT `Number of Applicants`
    FROM jobs
    ORDER BY `Number of Applicants`
    LIMIT 2 OFFSET (
        SELECT FLOOR((COUNT(*) - 1) / 2)
        FROM jobs
    )
) AS median_table;


27) Jobs by Largest Companies List jobs posted by companies with the largest size (500+ employees).
SELECT * 
FROM jobs 
WHERE `Company Size` = (
    SELECT MAX(`Company Size`) FROM jobs);


28)Jobs with the Same Skills but Different Companies

SELECT a.`Job Title` AS job_a, b.`Job Title` AS job_b, a.`Skills Required`
FROM jobs a
JOIN jobs b
  ON a.`Skills Required` = b.`Skills Required`
 AND a.`Company Name` != b.`Company Name`;


29) Highest Paying Jobs for Each Job Type
    
    SELECT `Job Type`, MAX(`Salary Range`) AS max_salary
FROM jobs
GROUP BY `Job Type`;

30) Forecast: Predict Demand for Python Skills
    
SELECT YEAR(`Posted Date`) AS year, MONTH(`Posted Date`) AS month, COUNT(*) AS python_jobs
FROM jobs
WHERE `Skills Required` LIKE '%Python%'
GROUP BY year, month
ORDER BY year, month;

    


    



























