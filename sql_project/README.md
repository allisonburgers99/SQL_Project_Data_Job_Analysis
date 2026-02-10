# Introduction
📊 Dive into the data job market! Focusing on data analyst roles, this project explores 💰 top-paying jobs, 🔥 in-demand skills, and 📍 where high demand meets high salary in data analytics.

💻 SQL queries? Check them out here: [sql_project](/sql_files/)

# Course Followed
[Luke Barousse](https://lukebarousse.com/sql)

# Tools I used

For my deep dive into the data analyst job market, I harnessed the power of several key tools:
- **SQL**: The backbone of my analysis, allowing me to query the database and unearth critical insights.
- **PostgreSQL**: The chosen database management system, ideal for handling the job posting data.
Visual Studio Code: My go-to for database management and executing SQL queries.
- **Git & GitHub**: Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.

# The analysis

Each query for this project aimed at investigating specific aspects of the data analyst job market. Here's how I approached each question:
### 1. Top Paying Data Analyst Jobs
To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location, focusing on remote jobs. This query highlights the high paying opportunities in the field.

![Top Paying Data Analyst Jobs](image-1.png)
*Bar graph visualizing salary for the top 10 Data Analyst companies.*

```sql
SELECT job_title_short,
    job_location,
    name as company_name,
    salary_year_avg,
    job_posted_date,
    job_work_from_home
FROM job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE job_title_short = 'Data Analyst' AND
    salary_year_avg IS NOT NULL AND
    job_location = 'Anywhere'
ORDER BY salary_year_avg DESC
LIMIT 10
```

### 2. Top Paying Skills
To identify top paying skills, I used SQL techniques like Common Expression Table and Left Join to get the desired insights. This query highlight the skills prominent in high paying Data Analyst jobs.

```sql
WITH top_paying_jobs AS (
SELECT    
    job_id,
    name as company_name,
    salary_year_avg,
    job_posted_date,
    job_work_from_home
FROM 
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE 
    job_title_short = 'Data Scientist' AND
    salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10
) 
SELECT top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id=skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id=skills_dim.skill_id  
ORDER BY salary_year_avg
```

### 3. Top Demanded Skills
To tackle this problem I used basic queries like COUNT and Inner Join to get the result. This query highlights the Skills asked by most of the recuriters.
```sql
SELECT 
    skills,
    COUNT(skills) as skill_count
FROM 
    job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id=skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id=skills_dim.skill_id
WHERE
    job_title_short='Data Analyst'
Group BY
    skills
ORDER BY
    skill_count DESC
LIMIT 5
```
### 4. Top Paying Skills
This query highlights skills that job postings with highest salary listed.
```sql
SELECT 
    skills,
    ROUND(AVG(salary_year_avg),0) as avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
GROUP BY
    skills
ORDER BY avg_salary DESC
LIMIT 25
```

### 5. Optimal Skills
This query provides insight into the skills that are high in demand and have high salary.

![alt text](image-2.png)
*Bubble Chart visualizing Skill Demand and Average Salary*

```sql
WITH skills_demand AS
    (
    SELECT 
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) as demand_count
    FROM 
        job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id=skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id=skills_dim.skill_id
    WHERE
        job_title_short='Data Analyst'
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = TRUE
    Group BY
        skills_dim.skill_id
    ),
average_salary AS
(   SELECT 
        skills_job_dim.skill_id,
        ROUND(AVG(job_postings_fact.salary_year_avg),0) as avg_salary
    FROM 
        job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short = 'Data Analyst'
        AND salary_year_avg IS NOT NULL
        AND job_work_from_home = TRUE
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
INNER JOIN average_salary ON skills_demand.skill_id=average_salary.skill_id
WHERE demand_count > 10
ORDER BY 
    avg_salary DESC
```

# What I learned
Throughout this adventure, I've turbocharged my SQL toolkit with some serious firepower:
- **Complex Query Crafting**: Mastered the art of advanced SQL, merging tables like a pro and wielding WITH clauses for ninja-level temp table maneuvers.
- **Data Aggregation**: Got cozy with GROUP BY
and turned aggregate functions like COUNT() and AVG
() into my data-summarizing sidekicks.
- **Analytical Wizardry**: Leveled up my real-world puzzle-solving skills, turning questions into actionable, insightful SQL queries.

# Conclusions
1. **Top-Paying Data Analyst Jobs**: The highest-paying jobs for data analysts that allow remote work offer a wide range of salaries, the highest at $650,000! (Y Combinator)
2. **Skills for Top-Paying Jobs**: High-paying
data analyst jobs require advanced proficiency in SQL and Python suggesting it's a critical skill.
3. **Most In-Demand Skills**: SQL is also the most
demanded skill in the data analyst job market, thus making it essential for job seekers.
4. **Skills with Higher Salaries**: Specialized
skills, such as SVN and Solidity, are associated with the highest average salaries, indicating a premium on niche expertise.
5. **Optimal Skills for Job Market Value**: SQL, Python and Excel leads in demand and offers for a high average salary, positioning it as one of the most optimal skills for data analysts to learn to maximize their market value.
