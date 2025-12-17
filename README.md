# Overview
Welcome my analysis of the data job market, specifically focusing on data engineer roles in Germany. This project was conducted to navigate the European market more effectively, identifying top paying and in demand skills to uncover optimal career oppurtunities.

The analysis utilizes the Luke Barousse Job Postings Dataset, which provides a robust foundation containing detailed information on job titles, salaries, locations, and essential skills. Through a series of Python scripts, I explore key market questions such as the most demanded skills, salary trends, and the intersection of demand and value for data engineers in Germany.

# Questions
Below are the core questions guiding this project:
1. What are the skills most in demand for the top 3 most popular data roles?
2. How are in demand skills trending for Data Engineers in Germany?
3. How well do jobs and skills pay for Data Engineers in Germany?
4. What are the optimal skills for data engineers to learn?
   
# Tools Used
To dive deep into the data engineer job market, I leveraged several key tools:
- **Python:** The backbone of the analysis for data processing and insight extraction.
    -**Pandas:** For data manipulation and aggregation.
    -**Matplotlib & Seaborn:** For data visualization and plotting trends.
- **Jupyter Notebooks:** Used for interactive development and documenting the analysis workflow.
- **Visual Studio Code:** The primary IDE for executing Python scripts.
- **Git & GitHub:** For version control and project hosting.

# Data Preparation and Clean Up
This section outlines the workflow for preparing the data, ensuring accuracy and usability for analysis.

## Import & Clean Up Data
I started by importing the necessary libraries and loading the dataset, followed by standart cleaning tasks to ensure data quality.
```python
import ast
import pandas as pd
import seaborn as sns
from datasets import load_dataset
import matplotlib.pyplot as plt

# Loading Data
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()

# Data Clean Up
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)
```

## Filter Germany Jobs
To align the analysis with the target market, I filtered the dataset to focus exclusively on roles based in Germany.

```python
df_GR = df[df['job_country'] == 'Germany']
```

# The Analysis

Each Jupyter Notebook in this project investigates specific aspects of the job market. Here is the breakdown of the findings:

## 1. What are the most demanded skills for the top 3 most popular data roles?
I identified the top 3 most popular data roles and filtered for their top 5 demanded skills. This highlights the primary technical requirements for each distinct role in the German market.

Technical Deep Dive: [2_Skill_&_Demand](2_Skill_&_Demand.ipynb)

### Visualize Data

```python
fig, ax = plt.subplots(len(job_titles), 1)

for i, job_title in enumerate(job_titles):
    df_plot = df_skills_perc[df_skills_perc['job_title_short'] == job_title].head(5)[::-1]
    sns.barplot(data=df_plot, x='skill_percent', y='job_skills', ax=ax[i], hue='skill_count', palette='dark:b_r')

plt.show()
```

### Results
![Likelihood of Skills Requested in the German Job Postings](assets\1.png)

*Bar graph visualizing the salary for the 3 data roles and their top 5 skills associated with each in Germany*

#### Insights:
- **SQL and Python** are the dominant languages, appearing in the majority of job postings for Data Engineers and Data Scientists.
- Data Engineers in Germay face high demand for Cloud Platforms (AWS,Azure) and Big Data technologies (Spark, Kafka) reflecting a shift towards cloud native infrastructure.
- Compared to Analysts, Data Engineers require less focus on visualization tools and more ETL/ELT pipelines and orchestration tools like Airflow.


## 2. How are in demand skills trending for Data Engineers in Germany?
To track market shifts, I grouped data engineering skills of job posting. This reveals how skill popularity fluctated throughout 2023.

Technical Deep Dive: [3_Trend_Skills](3_Trend_Skills.ipynb)

### Visualize Data

```python
from matplotlib.ticker import PercentFormatter

df_plot = df_DE_Germany_percent.iloc[:, :5]
sns.lineplot(data=df_plot, dashes=False, legend='full', palette='tab10')

plt.gca().yaxis.set_major_formatter(PercentFormatter(decimals=0))

plt.show()
```

### Results
![Trending Top Skills for Data Engineers in Germany](assets\2.png)

*Bar graph visualizing the trending top skills for data engineers in Germany*

#### Insights
- **Python and SQL** remain the most consistently demanded skills, showing stability throughout the year.
- **Cloud Skills** (AWS/Azure) are showing an upward trend, indicating that German companies are increasingly migrating to or expanding their cloud infrastructreç
- Specialized tools like **Spark and Airflow** maintain steady and demand, solidifying their place in the modern data engineering stack.

## 3. How well do jobs and skills pay for Data Engineers in Germany?
I analyzed salary distrubutions for commmon data jobs to set a baseline, then drilled down into specific skills for Data Engineers to identify high value tools

Technical Deep Dive: [4_Salary_Analysis](4_Salary_Analysis.ipynb)

### Visualize Data

```python
sns.boxplot(data=df_Germany_top6, x='salary_year_avg', y='job_title_short', order=job_order)

ticks_x = plt.FuncFormatter(lambda y, pos: f'€{int(y/1000)}K')
plt.gca().xaxis.set_major_formatter(ticks_x)
plt.show()
```

### Results
![Salary Distrubutions of Data Jobs in Germany](assets\3.png)
*Box plot visualizing the salary distributions for the top 6 data job titles.*

#### Insights
- Data Engineers in Germany normally command higher median salaries compared to Data Analysts.
- Senior roles and specialized positions (e.g, Cloud Data Engineer, Machine Learning Engineer) show significant upside
- Location plays a role, with hubs like Munich and Berlin often offering more salaries at the higher end distribution.


### Highest Paid & Most Demanded Skills for Data Engineers 
I further narrowed the analysis to distinguish between skills that are highest paid versus those that are most in demand.

#### Visualize Data
```python
fig, ax = plt.subplots(2, 1)  

# Top 10 Highest Paid Skills for Data Engineers
sns.barplot(data=df_DE_top_pay, x='median', y=df_DE_top_pay.index, hue='median', ax=ax[0], palette='dark:b_r')

# Top 10 Most In-Demand Skills for Data Engineers
sns.barplot(data=df_DE_skills, x='median', y=df_DE_skills.index, hue='median', ax=ax[1], palette='light:b')

plt.show()
```