# Introduction
Dive into the data job market! Focusing on data analyst roles, this project explores top-paying jobs, in-demand skills, and where high demand meets high salary in data analytics.

SQL queries? Check them out here: [project_sql folder](/project_sql/)

# Background
Driven by a quest to navigate the data analyst job market more effectively, this project was born from a desire to pinpoint top-paid and in-demand skills, streamlining others work to find optimal jobs.

### The questions I wanted to answer through my SQL queries were:

1. What are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most in demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

# Tools I Used
For my deep dive into the data analyst job market, I harnessed the power of several key tools:

-**SQL:** The backbone of my analysis, allowing me to query the database and unearth critical insights.

-**PostgreSQL:** The chosen database management system, ideal for handling the job posting data.

-**Visual Studio Code:** My go-to for database management and executing SQL queries.

-**Git & GitHub:** Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.

# The Analysis
Each query for this project aimed at investigating specific aspects of the data analyst job market. Here's how I approached each question:

### 1. Top Paying Data Analyst Jobs:
To identify the highest-paying roles I filtered data analyst positions by average yearly salary and location, focusing on remote jobs. This query highlights the high paying opportunities in the field.

```sql
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_location = 'Anywhere' AND
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT 10;
```
### Output:
| Job ID   | Job Title                                  | Job Location | Schedule  | Avg Salary ($) | Posted Date          | Company Name        |
|----------|---------------------------------------------|--------------|-----------|----------------|-----------------------|---------------------|
| 226942   | Data Analyst                                | Anywhere     | Full-time | 650000         | 2023-02-20 15:13:33   | Mantys              |
| 547382   | Director of Analytics                       | Anywhere     | Full-time | 336500         | 2023-08-23 12:04:42   | Meta                |
| 552322   | Associate Director- Data Insights           | Anywhere     | Full-time | 255829.5       | 2023-06-18 16:03:12   | AT&T                |
| 99305    | Data Analyst, Marketing                     | Anywhere     | Full-time | 232423         | 2023-12-05 20:00:40   | Pinterest Job Ads   |
| 1021647  | Data Analyst (Hybrid/Remote)                | Anywhere     | Full-time | 217000         | 2023-01-17 00:17:23   | Uclahealthcare      |
| 168310   | Principal Data Analyst (Remote)             | Anywhere     | Full-time | 205000         | 2023-08-09 11:00:01   | SmartAsset          |
| 731368   | Director, Data Analyst - HYBRID             | Anywhere     | Full-time | 189309         | 2023-12-07 15:00:13   | Inclusively         |
| 310660   | Principal Data Analyst, AV Performance      | Anywhere     | Full-time | 189000         | 2023-01-05 00:00:25   | Motional            |
| 1749593  | Principal Data Analyst                      | Anywhere     | Full-time | 186000         | 2023-07-11 16:00:05   | SmartAsset          |
| 387860   | ERM Data Analyst                            | Anywhere     | Full-time | 184000         | 2023-06-09 08:01:04   | Get It Recruit      |

### 2. Skills Required for Top-Paying Remote Data Analyst Jobs:
This query builds on the top 10 highest-paying remote Data Analyst roles and identifies the specific skills associated with each job.

```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        salary_year_avg,
        job_posted_date,
        name AS company_name
    FROM job_postings_fact
    LEFT JOIN company_dim 
        ON job_postings_fact.company_id = company_dim.company_id
    WHERE job_title_short = 'Data Analyst'
      AND job_location = 'Anywhere'
      AND salary_year_avg IS NOT NULL
    ORDER BY salary_year_avg DESC
    LIMIT 10
)
SELECT
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim 
    ON top_paying_jobs.job_id = skills_job_dim.job_id
LEFT JOIN skills_dim 
    ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC;
```

### Output snippet:
| job_id  | job_title                        | salary_year_avg | job_posted_date        | company_name | skills     |
|---------|-----------------------------------|------------------|-------------------------|--------------|------------|
| 552322  | Associate Director- Data Insights | 255829.5         | 2023-06-18 16:03:12     | AT&T         | sql        |
| 552322  | Associate Director- Data Insights | 255829.5         | 2023-06-18 16:03:12     | AT&T         | python     |
| 552322  | Associate Director- Data Insights | 255829.5         | 2023-06-18 16:03:12     | AT&T         | r          |
| 552322  | Associate Director- Data Insights | 255829.5         | 2023-06-18 16:03:12     | AT&T         | azure      |
| 552322  | Associate Director- Data Insights | 255829.5         | 2023-06-18 16:03:12     | AT&T         | databricks |
| 552322  | Associate Director- Data Insights | 255829.5         | 2023-06-18 16:03:12     | AT&T         | aws        |
| 552322  | Associate Director- Data Insights | 255829.5         | 2023-06-18 16:03:12     | AT&T         | pandas     |
| 552322  | Associate Director- Data Insights | 255829.5         | 2023-06-18 16:03:12     | AT&T         | pyspark    |
| 552322  | Associate Director- Data Insights | 255829.5         | 2023-06-18 16:03:12     | AT&T         | jupyter    |
| 552322  | Associate Director- Data Insights | 255829.5         | 2023-06-18 16:03:12     | AT&T         | excel      |


### What It Does:
-Creates a CTE with the top 10 highest-paying remote Data Analyst jobs.

-Joins skill tables to list required skills for each job.

-Returns jobs along with their associated skills sorted by salary.

### 3. Top 5 Most In-Demand Skills for Remote Data Analyst Roles:
This query identifies the skills that appear most frequently in remote Data Analyst job postings.
```sql
SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim 
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim 
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst'
  AND job_work_from_home = TRUE
GROUP BY skills
ORDER BY demand_count DESC
LIMIT 5;
```

### Output:
| skills    | demand_count |
|-----------|--------------|
| sql       | 7291         |
| excel     | 4611         |
| python    | 4330         |
| tableau   | 3745         |
| power bi  | 2609         |

### What It Shows:
-Counts how often each skill appears in remote Data Analyst job listings.

-Returns the top 5 skills with the highest demand.

### 4. Highest-Paying Skills for Remote Data Analyst Roles:
This query finds the top 25 highest-paying skills associated with remote Data Analyst positions by calculating the average yearly salary for jobs requiring each skill.

```sql
SELECT
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = True
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 25;
```

### Output:
| skills        | avg_salary |
|---------------|------------|
| pyspark       | 208172     |
| bitbucket     | 189155     |
| couchbase     | 160515     |
| watson        | 160515     |
| datarobot     | 155486     |
| gitlab        | 154500     |
| swift         | 153750     |
| jupyter       | 152777     |
| pandas        | 151821     |
| elasticsearch | 145000     |
| golang        | 145000     |
| numpy         | 143513     |
| databricks    | 141907     |
| linux         | 136508     |
| kubernetes    | 132500     |
| atlassian     | 131162     |
| twilio        | 127000     |
| airflow       | 126103     |
| scikit-learn  | 125781     |
| jenkins       | 125436     |
| notion        | 125000     |
| scala         | 124903     |
| postgresql    | 123879     |
| gcp           | 122500     |
| microstrategy | 121619     |


### What It Shows:
-Calculates the average salary for each skill across remote Data Analyst job postings.

-Highlights the top 25 skills that correspond to the highest-paying roles.

### 5. Most Optimal skills to learn
This SQL query analyzes remote Data Analyst job postings to identify the most valuable technical skills.
It joins job, skill, and salary data to calculate demand frequency and average salary for each skill.
```sql
SELECT
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = True
GROUP BY
    skills_dim.skill_id
HAVING
    COUNT(skills_job_dim.job_id) > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
```

### Output snippet:
| skill_id | skills     | demand_count | avg_salary |
|----------|------------|--------------|------------|
| 8        | go         | 27           | 115320     |
| 234      | confluence | 11           | 114210     |
| 97       | hadoop     | 22           | 113193     |
| 88       | snowflake  | 37           | 112948     |
| 74       | azure      | 34           | 111225     |
| 77       | bigquery   | 13           | 109654     |
| 76       | aws        | 32           | 108317     |
| 4        | java       | 17           | 106906     |
| 194      | ssis       | 12           | 106683     |
| 233      | jira       | 20           | 104918     |

### What It Shows:
- It lists the top 25 most in-demand and high-paying skills for Data Analysts in remote roles.

- Skills with fewer than 10 job mentions are filtered out to ensure reliable, meaningful insights.

# Conclusion:
This project uses SQL to explore the remote Data Analyst job market, revealing clear patterns in salary, demand, and required skills. The analysis shows that senior and director-level positions consistently offer the highest compensation, while core skills such as SQL, Python, Excel, Tableau, and Power BI remain essential across most roles. Additionally, specialized technologies—including PySpark, Snowflake, Hadoop, and various cloud platforms—are strongly associated with higher salaries, highlighting their value in advanced data workflows.

Overall, the findings provide meaningful insight into which skills are most valuable for aspiring Data Analysts and offer a data-driven roadmap for aligning one’s skillset with high-paying remote opportunities.