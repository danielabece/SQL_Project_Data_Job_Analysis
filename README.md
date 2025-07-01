# Introduction

Dive into the data job market! Focusing on data analysis roles, this project explores top-paying jobs, in-demand skills, and where hgh demand meets high salary in data analytics.

SQL queries? Check the here: [project_sql folder](/project_sql/).

# Background

This project reflects my desire to develop stronger data analysis skills, specifically in SQL. It is based on the course SQL for Data Analytics by Luke Barousse.

Data hails from [Luke Barousse's SQL course](https://lukebarousse.com/sql). It's packed with insights on job titles, salaries, locations, and essential skills.

### The questions answered through SQL queries were:

1. What are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most in demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

# Tools I Used

For my deep dive into the data analyst job market, I harnessed the power of several key tools:

- **SQL:** The backbone of my analysis, allowing me to query the database and unearth critical insights.
- **PostgreSQL:** The chosen database managment system, ideal for handling the job posting data.
- **Visual Studio Code:** My go-to for database management and executing SQL queries.
- **Git & GitHub:** Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.
# The Analysis
Each query for this project aimed at investigating specific aspects of the data analyst job market. Here's how I approached each question:

### 1. Top Paying Data Analyst Jobs

To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location, focusing on remote jobs. This query highlights the high paying opportunities in the field.

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
Here's the breakdown of the top data analyst jobs in 2023:

- **Wide Salary Range:** Top 10 paying data analyst roles span from $184,000 to $650,000, indicating significant salary potential in the field.
- **Diverse Employers:** Companies like SmartAsset, Meta and AT&T are among those offering high salaries, showing a bread interest across different industries.
- **Job Title Variety:** There's a high diversity in job titles, from Data Analyst to Director of Analytics, reflecting varied roles and specializations within data analytics.

![Top Paying Roles](/assets/Top_Paying_Jobs_by_Average_Annual_Salary.jpeg)

### 2. Top Paying Skills

In the following code, I filtered only for Data Analyst jobs and ordered them by yearly average salary, displaying the top 10 highest-paying positions along with the skills each job requires.

```sql 
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        salary_year_avg,
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
    LIMIT 10
)

SELECT
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC
```

Here's the breakdown of the most demanded skills for data analysts in 2023, based on the top 10 highest-paying job posted:

- **SQL** is leading with a bold count of 8.
- **Python** follows closely with a bold count of 7.
- **Tableau** is also highly sought after, with a bold count of 6.
- Other skills like **R, Snowflake, Pandas and Excel** show varying degrees of demand.

| Skill     | Count | Description                                      |
| :-------- | :---- | :----------------------------------------------- |
| SQL       | 8     | Structured Query Language for database management|
| Python    | 7     | General-purpose programming language for data    |
| Tableau   | 6     | Data visualization and business intelligence tool|
| R         | 4     | Statistical computing and graphics               |
| Snowflake | 3     | Cloud data warehousing platform                  |
| Pandas    | 3     | Python library for data manipulation and analysis|
| Excel     | 3     | Spreadsheet software for data analysis           |

### 3. Top Demanded Skills

To explore the most in-demand skills, I joined two tables to retrieve the skill names. I filtered the data to include only Data Analyst jobs that offer remote work using a WHERE clause. Finally, I grouped the results by skill and ordered them from the most to the least required.

```sql
SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_work_from_home = True
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5;
```
The results of the code above display the following:

- **SQL is clearly dominant,** appearing in more than 7,000 job postings nearly 60% more than the next skill.
- **Excel and Python are nearly tied,** showing the importance of both spreadsheet-based and programmatic analysis.
- **Visualization tools (Tableau and Power BI) round out the top 5,** emphasizing the value of being able to communicate data insights visually.
- This ranking suggests that employers expect data analysts to be versatile capable of querying data, analyzing it with code or tools, and presenting results clearly.

| Skill    | Demand Count |
| :------- | :----------- |
| SQL      | 7291         |
| Excel    | 4611         |
| Python   | 4330         |
| Tableau  | 3745         |
| Power BI | 2609         |

### 4. Top Paying Skills

To discover the top-paying skills, I joined two tables to retrieve the skill names. Based on that, I calculated the average yearly salary for each skill. I then filtered the data to include only Data Analyst jobs with available salary information that offer remote work. Finally, I grouped the results by skill and ordered them from the highest to the lowest average salary.

```sql
SELECT
    skills,
    ROUND(AVG(salary_year_avg),0) AS avg_salary
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
Here's a breakdown of the results for top paying skills:
- **Engineering-heavy and cloud-based skills (like PySpark, Databricks, Kubernetes, GCP)** lead to higher salaries — data analysts with big data & cloud experience are in demand.
- **Python data stack (Pandas, Numpy, Scikit-learn, Jupyter)** remains a core foundation, but adding machine learning or automation boosts value.
- **DevOps & software engineering practices (GitLab, Jenkins, Bitbucket, Linux)** are increasingly expected — analysts who can contribute to production-level systems earn more.

| Skills         | Average Salary |
| :------------- | :------------- |
| pyspark        | 208172         |
| bitbucket      | 189155         |
| couchbase      | 160515         |
| watson         | 160515         |
| datarobot      | 155486         |
| gitlab         | 154500         |
| swift          | 153750         |
| jupyter        | 152777         |
| pandas         | 151821         |
| elasticsearch  | 145000         |
| golang         | 145000         |
| numpy          | 143513         |
| databricks     | 141907         |
| linux          | 136508         |
| kubernetes     | 132500         |
| atlassian      | 131162         |
| twilio         | 127000         |
| airflow        | 126103         |
| scikit-learn   | 125781         |
| jenkins        | 125436         |
| notion         | 125000         |
| scala          | 124903         |
| postgresql     | 123879         |
| gcp            | 122500         |
| microstrategy  | 121619         |

### 5. Optimal Skills

As the last analysis, to get the most optimal skills depending on demand and salary; helped by WITH clause I made a LEFT JOIN of the last two analysis above (Top demanded skills and Top paying skills), after combining the results of those analysis I ordered the results considering the most demanded skills and the skills with the highest average salaries.

```sql
WITH skills_demand AS (
    SELECT
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short = 'Data Analyst'
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = True
    GROUP BY
        skills_dim.skill_id
), average_salary AS (
    SELECT
        skills_job_dim.skill_id,
        ROUND(AVG(salary_year_avg),0) AS avg_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short = 'Data Analyst'
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = True
    GROUP BY
        skills_job_dim.skill_id
)

SELECT
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    avg_salary
FROM
    skills_demand
INNER JOIN average_salary ON skills_demand.skill_id = average_salary.skill_id
WHERE
    demand_count > 10
ORDER BY
    demand_count DESC,
    avg_salary DESC
LIMIT 15;
```
- **SQL is the clear leader in demand,** appearing in nearly 400 job postings.
- **Python, R, Looker, and Snowflake all offer $100K+ average salaries,** showing a strong return on investment for those skills.
- **Snowflake and Azure stand out with very high salaries despite lower demand** — ideal for specialization.
- **Excel and PowerPoint are in high demand but relatively lower paid,** reflecting their accessibility and general use.


![Optimal Skills](/assets/average_salary_per_skill.jpeg)

# What I Learned

Considering this is my first SQL project, I learned the most important clauses such as SELECT, FROM, WHERE, GROUP BY, HAVING, and others. I also learned how to work with joins, subqueries, and CTEs.

The best part is that I didn’t just learn the concepts and when to use them, I practiced applying them in different scenarios until I truly understood how they work.

# Conclusions

This project explored the 2023 data analyst job market, revealing key insights:

- **SQL and Python** are essential skills, combining high demand and strong salaries.
- Tools like **Tableau, Power BI,** and **Excel** remain widely used, especially for data visualization and reporting.
- Specialized skills such as **Snowflake, Azure,** and **big data tools** offer higher salaries, making them valuable for career growth.
- Top-paying roles vary widely, showing opportunities across industries and job titles.

To stand out, data analysts should focus on a versatile skill set that blends technical ability, analytical thinking, and communication.