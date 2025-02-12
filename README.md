# Overview

This project focuses on analyzing the data job market, specifically targeting data analyst roles. It was driven by a desire to better understand and navigate career opportunities in this field. The analysis highlights top-paying and in-demand skills, helping identify optimal prospects for aspiring data analysts.

The data is sourced from ***Luke Barousse's*** website, which offers a comprehensive foundation with detailed insights into job titles, salaries, locations, and essential skills. Using Python scripts, the project explores key aspects such as the most in-demand skills, salary trends, and the correlation between demand and compensation in data analytics.

Additionally, this project allowed me to enhance my technical skills in data handling and manipulation with Pandas and NumPy in Python. It also provided valuable hands-on experience with real-world data for the first time.  

# The Questions
Below are the questions I want to answer in my project:

1. What are the skills most in demand for the top 3 most popular data roles?
2. How are in-demand skills trending for Data Analysts?
3. How well do jobs and skills pay for Data Analysts?
4. Highest Paid & Most Demanded Skills for Data Analysts
5. What are the optimal skills for data analysts to learn? (High Demand AND High Pay)


# Tools I Used

For my deep dive into the data analyst job market, I relied on a suite of powerful tools to extract, analyze, and visualize critical insights:

- **Python:** My primary language for data manipulation and analysis, forming the foundation of my work.
  - **Pandas:** I leveraged this library to efficiently handle and analyze large datasets.
  - **Matplotlib:** This library enabled me to create clear, informative visualizations.
  - **Seaborn:** I used Seaborn for its advanced visualization capabilities, adding a polished look to my data presentations.
- **Jupyter Notebooks:** An ideal environment for combining code, notes, and visual outputs seamlessly during my exploratory analysis.
- **Visual Studio Code:** My preferred code editor for writing and testing Python scripts, offering a robust development experience.
- **Git & GitHub:** Essential tools for version control and collaboration, ensuring that my code was well-organized and easily shared with others.

Each tool played a critical role in my analysis process, helping me transform raw data into actionable insights and communicate findings effectively.

# Data Preprocessing
This section outlines the steps taken to prepare the data for analysis, ensuring accuracy and usability.
## Import & Clean Up Data
I start by importing necessary libraries and loading the dataset, followed by initial data cleaning tasks to ensure data quality.

```python
# Importing Libraries
import ast
import pandas as pd
import seaborn as sns
from datasets import load_dataset
import matplotlib.pyplot as plt  

# Loading Data
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()

# Data Cleanup
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)
```
## Filter US Jobs
To focus my analysis on the U.S. job market, I apply filters to the dataset, narrowing down to roles based in the United States.

```python
df_us = df[df['job_country'] == 'United States']
```

# The Analysis

Each Jupyter notebook for this project aimed at investigating specific aspects of the data job market. Here’s how I approached each question:

## 1. What are the most demanded skills for the top 3 most popular data roles?

To find the most demanded skills for the top 3 most popular data roles. I filtered out those positions by which ones were the most popular, and got the top 6 skills for these top 3 roles. This query highlights the most popular job titles and their top skills, showing which skills I should pay attention to for data related job.

View my notebook with detailed steps here: [2_skills_count.ipynb](3_project/2_skills_count.ipynb)

### Visualize Data

```python

fig, ax = plt.subplots(len(top_3_jobs),1)

sns.set_theme(style='ticks')

for i in range(len(top_3_jobs)):
    df_draw=df_skill_perc[df_skill_perc['job_title_short'] == top_3_jobs[i]]
    sns.barplot(data=df_draw, x='skill_percent', y='job_skills' , ax=ax[i], hue='skill_percent', palette='dark:b_r', legend= False)
    ax[i].set_title(top_3_jobs[i])
    ax[i].set_xlim(0,80)
    ax[i].set_ylabel('')
    ax[i].set_xlabel('')


fig.suptitle('Percentage of top 6 skills in job posting', fontsize =15)
plt.tight_layout()
plt.show()

```

### Results

![working?](3_project\images\skill_demand_All_data_role.png)
*Bar graph visualizing the salary for the top 3 data roles and their top 5 skills associated with each*
### Insights

- SQL is the most requested skill for Data Analysts and Data Engineer, with it in over half the job postings for both roles. For Data Scientist, Python is the most sought-after skill, appearing in 72% of job postings followed by SQL.
- Data Engineers require more specialized technical skills (AWS, Azure, Spark) compared to Data Analysts and Data Scientists who are expected to be proficient in more general data management and analysis tools (Excel, Tableau).
- Python is a versatile skill, highly demanded across all three roles, but most prominently for Data Scientists (72%) and Data Engineers (65%).


## 2. How are in-demand skills trending for Data Analysts?
To find how skills are trending in 2023-24 for Data Analysts, I filtered data analyst positions and grouped the skills by the month of the job postings. This got me the top 5 skills of data analysts by month, showing how popular skills were throughout 2023.

View my notebook with detailed steps here: [3_skills_trends.ipynb](3_project/3_skills_trends.ipynb)
### Relevant Code
```python

df_us_da_pivot.drop('total', inplace= True)
df_us_da_pivot_final = df_us_da_pivot.iloc[:,:6].copy()
df_us_da_pivot_final

```
![](3_project\images\top_6_skills_moth_wise.png)


```python

da_total_posting = df_us_da.groupby('job_posted_month').size().reset_index(name = 'count') # -------------> total job posting in each month
da_total_posting

```

![](3_project\images\job_posting_month_wise.png)


```python

df_merge = pd.merge(left =df_us_da_pivot_final , right= da_total_posting , how= 'left' , on= 'job_posted_month' )
df_merge = df_merge.set_index('job_posted_month')
df_merge

```

![](3_project\images\merged_data_frame.png)

```python

df_perc = round(df_merge.iloc[ : , : -1].div(df_merge['count'], axis= 0) * 100,3) #-----------> dividing each value in a row with corresponding count value 
df_perc 

```

![](3_project\images\perc_dataframe.png)

### Visualize Data

```python

sns.set_theme(style='ticks')
sns.lineplot(data= df_perc, dashes= False, palette= 'tab10')
plt.title('Top 6 Skills, for Data Analyst in the US, as a percent of total posting per month')
plt.ylabel('percentage')
plt.xlabel(2023)
sns.despine()

ax = plt.gca()
ax.yaxis.set_major_formatter(plt.FuncFormatter(lambda x , _ : f'{int(x)}%' ))
plt.show()

```
### Result
![](3_project\images\perc_skill_outof_toalposting.png)

### Insights:
- SQL remains the most consistently demanded skill throughout the year, although it shows a gradual decrease in demand by upto 8 percentage point.

- Excel experienced a significant drop in demand starting around August but, picked up from october onwards surpassing both python and tableau.

- Both Python and Tableau show relatively stable demand throughout the year with some fluctuations but remain essential skills for data analysts. 

- Power BI, while less demanded compared to the others, shows a slight upward trend towards the year's end.

## 3. How well do jobs and skills pay for Data Analysts?

To identify the highest-paying roles and skills, I only got jobs in the United States and looked at their median salary. But first I looked at the salary distributions of common data jobs like Data Scientist, Data Engineer, and Data Analyst, to get an idea of which jobs are paid the most.

View my notebook with detailed steps here: [4_salary_analysis.ipynb](3_project/4_salary_analysis.ipynb)


#### Visualize Data

```python 
salary_list = [df_us_top6[df_us_top6['job_title_short'] == jt]['salary_year_avg'] for jt in job_titles]
sns.boxplot(data = df_us_top6 , x= 'salary_year_avg', y='job_title_short', order= job_order)
plt.title('Salary distribution in U.S.')
plt.xlabel('yearly salary $')
plt.ylabel('')
ax = plt.gca()
ax.xaxis.set_major_formatter(plt.FuncFormatter(lambda x ,__ : f'${int(x/1000)}k'))
plt.xlim(0,650000)
plt.show()
```
#### Result

![](3_project\images\salary_distribution_job_wise.png)*Box plot visualizing the salary distributions for the top 6 data job titles.*

Insights
- There's a significant variation in salary ranges across different job titles. Senior Data Scientist positions tend to have the highest salary potential, with up to $500K, indicating the high value placed on advanced data skills and experience in the industry.

- Senior Data Engineer and Senior Data Scientist roles show a considerable number of outliers on the higher end of the salary, suggesting that exceptional skills or circumstances can lead to high pay in these roles. 

- The median salaries increase with the seniority and specialization of the roles. Senior roles (Senior Data Scientist, Senior Data Engineer) not only have higher median salaries but also larger differences in typical salaries, reflecting greater variance in compensation as responsibilities increase.

##  4. Highest Paid & Most Demanded Skills for Data Analysts

Next, I narrowed my analysis and focused only on data analyst roles. I looked at the highest-paid skills and the most in-demand skills. I used two bar charts to showcase these.

View my notebook with detailed steps here: [4_salary_analysis.ipynb](3_project/4_salary_analysis.ipynb)
``` python
fig , ax = plt.subplots(2,1)

sns.barplot(data=df_us_top_pay_skill, x= 'median_sal', y=df_us_top_pay_skill.index, ax = ax[1], hue = 'median_sal', palette='dark:b_r')


sns.barplot(data=df_us_top_skill, x= 'median_sal', y=df_us_top_skill.index, ax = ax[0], hue = 'median_sal', palette='light:b')

fig.tight_layout()
plt.show()
```
#### Result
![](3_project\images\highest_paid_skill_vs_highestPaid_profile.png)
*Two separate bar graphs visualizing the highest paid skills and most in-demand skills for data analysts in the US.*

Insights:

- The bottom graph shows specialized technical skills like dplyr, Bitbucket, and Gitlab are associated with higher salaries, some reaching up to $200K, suggesting that advanced technical proficiency can increase earning potential.

- The top graph highlights that foundational skills like Excel, PowerPoint, and SQL are the most in-demand, even though they may not offer the highest salaries. This demonstrates the importance of these core skills for employability in data analysis roles.

- There's a clear distinction between the skills that are highest paid and those that are most in-demand. Data analysts aiming to maximize their career potential should consider developing a diverse skill set that includes both high-paying specialized skills and widely demanded foundational skills.


## 5. What is the most optimal skill to learn for Data Analysts?

To identify the most optimal skills to learn ( the ones that are the highest paid and highest in demand) I calculated the percent of skill demand and the median salary of these skills. To easily identify which are the most optimal skills to learn.

View my notebook with detailed steps here: [5_optimal_skill.ipynb](3_project/5_optimal_skill.ipynb)

#### Visualize Data

```python 
from adjustText import adjust_text

df_da_skills.plot(kind='scatter', x='skill_perc', y='Median_sal' )
plt.xlabel('percentage of job posting')
plt.ylabel('Median salary in $')
plt.title('Most optimal skills for DA in Us')
plt.tight_layout()

plt.show()

```

#### Result

![](3_project\images\scatter_plot_optimizedSkill_DA.png)
*A scatter plot visualizing the most optimal skills (high paying & high demand) for data analysts in the US.*

Insights:

- Oracle, though less frequently mentioned in job postings, offers the highest median salary of nearly $97K, highlighting the premium placed on specialized database skills in data analysis.

- More common skills like Excel and SQL appear often in job listings but come with lower median salaries compared to specialized tools like Python and Tableau, which not only offer higher pay but are also seen moderately in listings.

- Skills such as Python, Tableau, and SQL Server sit at the higher end of the salary range while also being fairly common in job postings, suggesting that expertise in these tools can open up strong career opportunities in data analytics.

### Visualizing Different Techonologies


Let's visualize the different technologies as well in the graph. We'll add color labels based on the technology (e.g., {Programming: Python})

#### Visualize Data

```python
from matplotlib.ticker import PercentFormatter

# Create a scatter plot
scatter = sns.scatterplot(
    data=df_DA_skills_tech_high_demand,
    x='skill_percent',
    y='median_salary',
    hue='technology',  # Color by technology
    palette='bright',  # Use a bright palette for distinct colors
    legend='full'  # Ensure the legend is shown
)
plt.show()
```
*A scatter plot visualizing the most optimal skills (high paying & high demand) for data analysts in the US with color labels for technology.*

#### Results

![](3_project\images\scatter_plot_optimizedSkill_withTech_DA.png)

#### Insights:
- The scatter plot reveals that most programming skills (blue) tend to be associated with higher salary levels, suggesting that programming expertise can lead to better pay in data analytics.

- Database skills (green), such as Oracle and SQL Server, are linked to some of the highest salaries among data analyst tools, reflecting strong demand and high value placed on data management and manipulation skills.

- Analyst tools (orange), like Tableau and Power BI, are common in job listings and offer competitive salaries, highlighting the importance of visualization and analysis software in today’s data roles.

# What I Learned
Throughout this project, I embarked on a data adventure that not only boosted my Python prowess but also revealed the inner workings of the data analyst job market. Here are some fun and insightful takeaways from my journey:

- **Mastering Python:**  
  I dove deep into advanced Python techniques, wielding libraries like Pandas to tame unruly datasets and using Seaborn and Matplotlib to craft visuals that speak louder than words. It felt like upgrading my toolkit with superpowers!

- **The Art of Data Cleaning:**  
  I learned that cleaning your data is like preparing ingredients before cooking a gourmet meal—it’s essential! Without meticulous preparation, even the most brilliant analysis can fall flat. A clean dataset is the secret sauce to accurate insights.

- **Mapping the Data-Driven Career Landscape:**  
  This project was also a crash course in strategic career planning. I discovered how aligning my technical skills with market demand—by analyzing the interplay of skill requirements, salaries, and job availability—can help pave the way for a thriving career in tech.

This experience not only sharpened my technical skills but also gave me a playful yet powerful perspective on the world of data analytics.

# Insights
This project provided several general insights into the data job market for analysts:

- **Skill Demand and Salary Correlation:** There is a clear correlation between the demand for specific skills and the salaries these skills command. Advanced and specialized skills like Python and Oracle often lead to higher salaries.
- **Market Trends:** There are changing trends in skill demand, highlighting the dynamic nature of the data job market. Keeping up with these trends is essential for career growth in data analytics.
- **Economic Value of Skills:** Understanding which skills are both in-demand and well-compensated can guide data analysts in prioritizing learning to maximize their economic returns.

# Challenges I Faced

- **Data Inconsistencies:**  
  Managing missing or inconsistent data entries required rigorous data-cleaning techniques. Ensuring data integrity was essential for producing accurate and reliable analysis.

- **Complex Data Visualization:**  
  Designing effective visual representations of multifaceted datasets presented a significant challenge. I focused on developing clear and compelling visuals that accurately conveyed the underlying insights.

These challenges provided valuable learning opportunities, ultimately enhancing my data analytics capabilities and strengthening my problem-solving skills.

# Final Conclusion

This exploration into the data analyst job market has been highly informative, offering a comprehensive understanding of the critical skills and emerging trends that define this dynamic field. The insights gained have not only deepened my understanding but also provided actionable guidance for advancing a career in data analytics. 

*As the market evolves, ongoing analysis and adaptation will be essential to remain competitive.*