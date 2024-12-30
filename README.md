# Introduction
Dive into the data job market! Focusing on data analyst roles, this project explores top paying jobs, in-demand skills, and where high demand meets high slary in data analytics.

🔍SQL queries? Check them out here: [project_sql folder](/project_sql/)

This project followed the structured learning path provided in an SQL course, where I practiced applying concepts such as joins, aggregation, and filtering to real-world data scenarios. You can explore the course details here: https://www.lukebarousse.com/sql

# Background
Motivated by the goal of better understanding the data analyst job market, this project aimed to identify high-paying and sought-after skills, making it easier for others to target ideal job opportunities.

The questions I wanted to answer through my SQL queries were:
- 1. What are the top-paying data analyst jobs?
- 2. What skills are required for these top-paying jobs?
- 3. What skills are most in demand for data analysts?
- 4. Which skills are associated with higher salaries?
- 5. What are the most optimal skills to learn?

# Tools I Used
- **SQL:** The backbone of my analysis, allowing me to query the database and unearth critical insights.
- **PostgreSQL:** The chosen database management system, ideal for handling the job posting data.
- **Visual Studio Code:** My go-to for database management and executing SQL queries.
- **Git & GitHub:** Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.

# The Analysis
Each query for this project aimed at investigating specific aspects of the data analyst job market. Here’s how I approached each question:

## 1. Top Paying Data Analyst Jobs

To identify the highest paying roles, I created a query that filtered data analyst positions by salary, focusing on remote jobs. This query highlights the high paying opportunities.

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
    salary_year_avg IS NOT NULL AND
    job_work_from_home = TRUE
ORDER BY 
    salary_year_avg DESC
LIMIT 10;
```
Here's the breakdown of the top highest-pay data analyst jobs in 2023:

- Wide Salary Range: Top 10 paying data analyst roles span from $184,000 to $650,000, indicating significant salary potential in the field.
- Diverse Employers: Companies like SmartAsset, Meta, and AT&T are among those offering high salaries, showing a broad interest across different industries.
- Job Title Variety: There's a high diversity in job titles, from Data Analyst to Director of Analytics, reflecting varied roles and specializations within data analytics.

## 2. Skills for Top Paying Jobs
To understand what some of the most important skills are for the top paying jobs, I created a query that displays the top-ten highest paying jobs along with the skills that are required for each role.
```sql
WITH top_paying_jobs AS (
    SELECT  
        job_postings_fact.job_id AS "Job ID",
        job_postings_fact.job_title AS "Job Title",
        job_postings_fact.salary_year_avg AS "Average Salary",
        company_dim.name AS "Company Name"
    FROM
        job_postings_fact
    LEFT JOIN company_dim 
        ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_postings_fact.job_title_short = 'Data Analyst' 
        AND job_postings_fact.job_location = 'Anywhere' 
        AND job_postings_fact.salary_year_avg IS NOT NULL
    ORDER BY 
        job_postings_fact.salary_year_avg DESC
    LIMIT 10
)

SELECT  
    top_paying_jobs.*,
    skills_dim.skills AS "Skills"
FROM 
    top_paying_jobs
INNER JOIN skills_job_dim 
    ON top_paying_jobs."Job ID" = skills_job_dim.job_id
INNER JOIN skills_dim 
    ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY 
    "Average Salary" DESC;
```
Here's the breakdown of the most demanded skills for the top-ten highest paying jobs 2023, based on job postings:
- SQL is leading with a count of 8
- Python follows closely with a count of 7
- Tableua is also highly sought after, with a count of 6
- Other skills like R, Snowflake, pandas, and Excel show varying degrees of demand

## 3. In-Demand Skills for Data Analysts
To gain insights for the most in demand skills, I created a query that gives the count for the top 5 most used skills based on job postings.
```sql
SELECT 
    skills AS "Skills",
    COUNT(*) AS "Skill Count"
FROM job_postings_fact 

INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst'
GROUP BY skills
ORDER BY COUNT(*) DESC

LIMIT 5;
```
Here are the results for the most in demand skills based on 2023 job postings:
| Rank | Skill   | Skill Count    |
|------|---------|----------------|
| 1    | SQL     | 385,750        |
| 2    | Python  | 381,863        |
| 3    | AWS     | 145,718        |
| 4    | Azure   | 132,851        |
| 5    | R       | 131,285        |


## 4. Skills Based on Salary
To find which the top skills based on average salary, I created a query that displays the average salary for each skill that appear in at least 10 job postings.
```sql
SELECT 
    ROUND(AVG(salary_year_avg), 2) AS "Average Salary",
    COUNT(salary_year_avg) AS "Count",
    skills AS "Skills"
FROM job_postings_fact 
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst' AND salary_year_avg IS NOT NULL
GROUP BY skills
HAVING COUNT(salary_year_avg) > 10
ORDER BY AVG(salary_year_avg) DESC
LIMIT 25;
```
| Rank | Skill         | Average Salary ($) |
|------|---------------|--------------------|
| 1    | Kafka         | 129,999           |
| 2    | Pyrotorch     | 125,226           |
| 3    | Perl          | 123,686           |
| 4    | Tensorflow    | 120,647           |
| 5    | Cassandra     | 118,407           |
| 6    | Atlassian     | 117,966           |
| 7    | Airflow       | 116,387           |
| 8    | Scala         | 115,480           |
| 9    | Linux         | 114,883           |
| 10   | Confluence    | 114,153           |


Key insights from ChatGPT:

High-paying skills are a mix of specialized tools, programming languages, and foundational technologies, with Kafka, Pyrotorch, and Tensorflow leading the list.
- AI and Infrastructure Dominate: AI/ML frameworks like Pyrotorch and Tensorflow highlight the demand for expertise in these fields, while tools like Cassandra, Airflow, and Linux emphasize the importance of DevOps and data management.
- Programming and Niche Roles: Perl and Scala illustrate the value of niche programming skills, showing strong salary potential in scripting, backend development, and big data processing.

## 5. Most Optimal Skills to Learn
Combining insights from demand and salary data, this query aimed to pinpoint skills that are both in high demand and have high salaries, offering a strategic focus for skill development.

```sql
WITH skills_demand AS (
SELECT 
    skills_dim.skill_id,
    skills_dim.skills AS "Skills",
    COUNT(*) AS "Skill Count"
FROM job_postings_fact 

INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst' AND salary_year_avg IS NOT NULL
GROUP BY skills_dim.skill_id
ORDER BY COUNT(*) DESC

 ),

average_salary AS (
SELECT 
    ROUND(AVG(salary_year_avg),2) AS "Average Salary",
    skills_dim.skill_id
FROM job_postings_fact 
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst' 
AND salary_year_avg IS NOT NULL 
AND job_work_from_home = TRUE
GROUP BY skills_dim.skill_id

)

SELECT
    skills_demand.skill_id,
    "Skills",
    "Skill Count",
    "Average Salary"
FROM skills_demand
INNER JOIN average_salary ON skills_demand.skill_id = average_salary.skill_id
WHERE "Skill Count" > 200
ORDER BY "Average Salary" DESC
LIMIT 25
```

| Rank | Skill       | Average Salary ($) |
|------|-------------|--------------------|
| 1    | Go          | 115,320           |
| 2    | Snowflake   | 112,948           |
| 3    | Azure       | 111,225           |
| 4    | AWS         | 108,317           |
| 5    | Oracle      | 104,534           |
| 6    | Looker      | 103,795           |
| 7    | Python      | 101,397           |
| 8    | R           | 100,499           |
| 9    | Tableau     | 99,288            |
| 10   | SAS         | 98,902            |

Key Insights From ChatGPT:

The most optimal skills balance high salaries and broad job availability, with Go, Snowflake, and Azure standing out as top contenders for lucrative and in-demand roles.

- Cloud and Data Technologies Lead: Cloud platforms like Azure and AWS dominate, reflecting the growing importance of cloud infrastructure expertise. Data-focused tools like Snowflake, Looker, and Tableau highlight the value of skills in managing and analyzing large datasets.

- Programming and Statistical Skills Shine: Programming languages like Go and Python maintain relevance for their versatility and demand across industries, while statistical tools like R and SAS demonstrate strong opportunities in data science and analytics.

# What I Learned
Throughout this project, I enhanced my SQL toolkit tremendously:
- 🧩 Complex Query Crafting: Mastered the art of advanced SQL, merging tables like a pro and wielding WITH clauses for ninja-level temp table maneuvers.
- 📊 Data Aggregation: Got cozy with GROUP BY and turned aggregate functions like COUNT() and AVG() into my data-summarizing sidekicks.
- 💡 Analytical Wizardry: Leveled up my real-world puzzle-solving skills, turning questions into actionable, insightful SQL queries.

# Conclusions
### Insights

From the analysis, several general insights emerged:

1. Top Paying Data Analyst Jobs: The highest-paying jobs for data analysts that allow remote work offer a wide range of salaries, the highest at $650,000!
2. Skills for Top Paying Jobs: High-paying data analyst jobs require advanced proficiency in SQL, suggesting it’s a critical skill for earning a top salary.
3. In-Demand Skills for Data Analysts: SQL and python are the most demanded skills in the data analysis market, thus making it essential for job seekers. 
4. Skills Based on Salary: High-paying skills reflect a balance of cutting-edge AI/ML frameworks, essential infrastructure tools, and niche programming languages. Expertise in technologies like Pyrotorch, Tensorflow, and Kafka leads the market, while foundational tools like Linux and specialized languages like Perl and Scala offer competitive salaries in their domains.
5. Most Optimal Skills to Learn: The most optimal skills offer a blend of high salaries and wide job availability, with Go, Snowflake, and Azure leading the way. Cloud platforms and data technologies dominate, while programming languages like Go and Python, alongside statistical tools like R and SAS, continue to drive demand across industries.

### Closing Thoughts

This project significantly improved my SQL skills, boosting my confidence in data analysis. The findings from this analysis provide valuable insights for prioritizing skill development and guiding job search efforts. Aspiring data analysts can leverage this data to position themselves for high-paying roles in the future, while also emphasizing the importance of continuous learning and adapting to emerging trends in data analytics.






