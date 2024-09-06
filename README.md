# Introduction
📊 **Dive into the dynamic data job market!** This project centers around data analyst roles, focusing on 💰 top-paying positions, 🔥 in-demand skills, and 📈 where high demand meets high salaries in the world of data analytics.

🔍 Curious about the SQL queries used in this analysis? You can explore them here: [project_sql folder](/project_sql/)

# Background
This project was initiated to provide insights into navigating the data analyst job market more efficiently. By identifying top-paid roles and in-demand skills, it aims to streamline the job search process and help others discover the most promising career opportunities in data analytics.

### Key Questions Addressed:

1. **What are the top-paying data analyst jobs?**
2. **What skills are required for these top-paying roles?**
3. **What skills are most in demand for data analysts?**
4. **Which skills are associated with higher salaries?**
5. **What are the most strategic skills to learn for career advancement?**

# Tools I Used
To gain a deep understanding of the data analyst job market, I employed several essential tools:

- **SQL:** The foundation of the analysis, enabling me to query and extract critical insights from the dataset.
- **PostgreSQL:** My chosen database management system, well-suited for handling the large volume of job posting data.
- **Visual Studio Code:** The primary platform for database management and executing SQL queries.
- **Git & GitHub:** Integral for version control and collaboration, ensuring all SQL scripts and analyses were well-tracked and shareable.

# The Analysis
Each query in this project was crafted to explore specific facets of the data analyst job market. Below is a detailed breakdown of how I approached each question:

### 1. Top Paying Data Analyst Jobs
To uncover the highest-paying data analyst roles, I filtered job listings by average yearly salary and location, with a focus on remote positions. This query provided insights into the most lucrative opportunities available in the field.

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
    (job_location = 'Anywhere' OR job_location = 'Germany') AND
    salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10;
```

**Here's the breakdown of the top data analyst jobs in 2023:**

- **Wide Salary Range:** The top 10 paying data analyst roles offer salaries ranging from $184,000 to $650,000, indicating substantial earning potential in the field.
- **Diverse Employers:** Companies such as SmartAsset, Meta, and AT&T are among those offering high salaries, showcasing demand across various industries.
- **Variety in Job Titles:** There is significant diversity in job titles, from Data Analyst to Director of Analytics, reflecting the broad range of roles and specializations within data analytics.
- **Remote Opportunities:** A majority of these roles offer remote work, highlighting the growing flexibility within high-paying data analyst positions.

\![Top Paying Roles](assets/1_top_paying_roles.png)
*Bar graph visualizing the count of skills for the top 10 paying jobs for data analysts; ChatGPT generated this graph from my SQL query results*

### 2. Skills for Top Paying Jobs
To determine the skills required for the top-paying data analyst roles, I joined the job postings data with the skills data. This provided valuable insights into which skills are most critical for high-paying positions.

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
    salary_year_avg DESC;
```

**Key findings about the most demanded skills for the top 10 highest-paying data analyst jobs in 2023:**

- **SQL** is the most requested skill, appearing in 8 out of the top 10 roles.
- **Python** closely follows, being required for 7 of these high-paying positions.
- **Tableau** remains a crucial tool, appearing in 6 roles. Other notable skills such as **R**, **Snowflake**, **Pandas**, and **Excel** also show varying degrees of demand across these roles.

![Top Paying Roles Skills](assets/2_top_paying_roles_skills.png)
*Bar graph visualizing the count of skills for the top 10 paying jobs for data analysts; ChatGPT generated this graph from my SQL query results*

### 3. In-Demand Skills for Data Analysts
This query identified the skills most frequently requested in data analyst job postings, helping to pinpoint which areas hold the most promise for job seekers.

```sql
SELECT 
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' 
    AND job_work_from_home = True 
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5;
```

**Here’s the breakdown of the most in-demand skills for data analysts in 2023:**

- **SQL** and **Excel** remain foundational, underscoring the need for strong skills in data processing and spreadsheet management.
- **Programming Languages** and **Visualization Tools** such as **Python**, **Tableau**, and **Power BI** are in high demand, highlighting the increasing importance of technical skills for data storytelling and decision-making.

\| Skills   \| Demand Count \|
\|----------\|--------------\|
\| SQL      \| 7,291        \|
\| Excel    \| 4,611        \|
\| Python   \| 4,330        \|
\| Tableau  \| 3,745        \|
\| Power BI \| 2,609        \|

*Table of the demand for the top 5 skills in data analyst job postings.*

### 4. Skills Based on Salary
This query explored the relationship between average salaries and specific skills, revealing which skills are most likely to lead to higher earnings in data analyst roles.

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

**Top-paying skills for data analysts in 2023:**

- **Big Data & Machine Learning Expertise:** Skills like PySpark, Couchbase, and DataRobot command top salaries, reflecting the growing importance of big data and predictive modeling in the field.
- **Development & Deployment Skills:** Proficiency in tools such as GitLab, Kubernetes, and Airflow is highly valued, emphasizing the crossover between data analytics and engineering.
- **Cloud Computing Proficiency:** Cloud-based tools (Elasticsearch, Databricks, GCP) significantly boost earning potential, highlighting the shift towards cloud-driven analytics.

\| Skills       \| Average Salary (\$) \|
\|--------------\|--------------------\|
\| pyspark      \| 208,172            \|
\| bitbucket    \| 189,155            \|
\| couchbase    \| 160,515            \|
\| watson       \| 160,515            \|
\| datarobot    \| 155,486            \|
\| gitlab       \| 154,500            \|
\| swift        \| 153,750            \|
\| jupyter      \| 152,777            \|
\| pandas       \| 151,821            \|
\| elasticsearch\| 145,000            \|

*This table format is markdown-friendly and will render properly when uploaded to GitHub.*

### 5. Most Optimal Skills to Learn
By combining data on demand and salary, this query sought to identify the skills that offer both high demand and high salary potential, making them optimal for career growth.

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

\| Skill ID \| Skills      \| Demand Count \| Average Salary (\$) \|
\|----------\|-------------\|--------------\|--------------------\|
\| 8        \| go          \| 27           \| 115,320            \|
\| 234      \| confluence  \| 11           \| 114,210            \|
\| 97       \| hadoop      \| 22           \| 113,193            \|
\| 80       \| snowflake   \| 37           \| 112,948            \|
\| 74       \| azure       \| 34           \| 111,225            \|
\| 77       \| bigquery    \| 13           \| 109,654            \|
\| 76       \| aws         \| 32           \| 108,317            \|
\| 4        \| java        \| 17           \| 106,906            \|
\| 194      \| ssis        \| 12           \| 106,683            \|
\| 233      \| jira        \| 20           \| 104,918            \|

*Table of the most optimal skills for data analysts, sorted by salary*

**Key insights from this analysis:**

- **Programming Languages in High Demand:** Python and R are heavily sought after, with high demand counts of 236 and 148, respectively. However, their widespread availability has slightly tempered their average salaries.
- **Cloud Technologies & Big Data Tools:** Skills in Snowflake, Azure, AWS, and BigQuery are in high demand and command competitive salaries, reflecting the growing relevance of cloud platforms and big data analytics.
- **Business Intelligence & Visualization Tools:** Tableau and Looker are in demand for translating data into actionable insights, offering good salary potential for those proficient in these tools.
- **Database Management:** Strong demand for database skills, particularly in NoSQL and traditional databases like Oracle and SQL Server, ensures steady demand and competitive salaries for database professionals.

# What I Learned

Throughout this project, I enhanced my SQL proficiency and deepened my understanding of the data analyst job market:

🧩 **Advanced Query Techniques:** Mastered complex SQL queries, including JOINs, CTEs, and using WITH clauses for advanced data manipulation.
📊 **Data Aggregation:** Strengthened my use of GROUP BY and aggregate functions like COUNT() and AVG() to extract key insights from the data.
💡 **Real-World Problem Solving:** Improved my ability to translate job market questions into actionable SQL queries, generating valuable career-related insights.

# Conclusions

### Key Insights
The analysis led to several critical findings:

1. **High-Paying Data Analyst Jobs:** The highest-paying remote data analyst roles offer a broad salary range, with the top salary reaching up to $650,000.
2. **Skills for Top-Paying Jobs:** Advanced SQL proficiency is key for securing high-paying data analyst positions, making it a critical skill for job seekers aiming for top salaries.
3. **Most In-Demand Skills:** SQL remains the most in-demand skill, making it indispensable for data analysts.
4. **Emerging Skills in Demand:** Cloud platforms (Snowflake, AWS, BigQuery) and data engineering tools are growing in importance, offering lucrative opportunities for data professionals with these competencies.
