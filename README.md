# Media Performance Analysis
## Project Objective
A little bit of backstory to this project, a private entertainment company is planning to launch a new show analyzing the trends and performance of blockbuster movies from 2015 to 2023, they want to avoid subjective opinions and focus on data-driven insights.

So, this project aims to explore themes like Box Office Performance, Audience demographics, Critical reception, Streaming platform popularity in order to generate meaningful insights that could influence future content strategies
## Key Metrics to note
- Box Office (Total & Average)
- Production Budget (Total & Average)
- Average Audience Age & Rating
- Average Critical Review Score
## Tools and Steps involved in this project
- Extracted raw Excel data into **SQL** and conducted thorough cleaning
- Used SQL for tasks such as age group categorization and null handling
- Exported the cleaned data into **PowerBI** for visualization and trend analysis
## Challenges & Solutions
- Null Values
    - I observed that the dataset contained numerous Null values, particularly in fields like box office gross, production budget, and age. To address this, I applied average imputation in SQL to fill these gaps, preserving valuable data for more accurate analysis. However, for columns such as date, director, and movie title, I opted to use the DELETE statement to remove rows with missing entries.
