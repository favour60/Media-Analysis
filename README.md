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
1. ***Null Values***; I observed that the dataset contained numerous Null values, particularly in fields like box office gross, production budget, and age. To address this, I applied average imputation in SQL to fill these gaps, preserving valuable data for more accurate analysis. However, for columns such as date, director, and movie title, I opted to use the DELETE statement to remove rows with missing entries.
```SQL
 --UPDATING ROWS WITH NULL VALUES USING AVERAGE IMPUTATION------------
---box office gross
UPDATE Project3.dbo.movie_box_office_data$
SET Project3.dbo.movie_box_office_data$.[Box Office Gross ($)] = (SELECT AVG(Project3.dbo.movie_box_office_data$.[Box Office Gross ($)]) AS [avg box ofice gross]
FROM Project3.dbo.movie_box_office_data$)
WHERE Project3.dbo.movie_box_office_data$.[Box Office Gross ($)] IS NULL

---forgot to include round up so updating after
SELECT Project3.dbo.movie_box_office_data$.[Box Office Gross ($)] FROM Project3.dbo.movie_box_office_data$
UPDATE Project3.dbo.movie_box_office_data$ SET Project3.dbo.movie_box_office_data$.[Box Office Gross ($)] = ROUND(Project3.dbo.movie_box_office_data$.[Box Office Gross ($)], 0)

---production budget
UPDATE Project3.dbo.movie_box_office_data$
SET Project3.dbo.movie_box_office_data$.[Production Budget ($)] = (SELECT ROUND (AVG(Project3.dbo.movie_box_office_data$.[Production Budget ($)]), 0) AS [avg production budget]
FROM Project3.dbo.movie_box_office_data$)
WHERE Project3.dbo.movie_box_office_data$.[Production Budget ($)] IS NULL
```

2. ***Inconsistent labels***; I also identified inconsistencies in the gender labels. To resolve this, I used the UPDATE statement in SQL to standardize gender entries (e.g. ensuring all values consistently reflected "Male" or "Female").
``` SQL
 ----STANDARDISING INCONSISTENT ROWS AND UPDATING THE ROWS------------- 
---M to Male
UPDATE Project3.dbo.Audience_demographic_data$
SET Project3.dbo.Audience_demographic_data$.Gender = 'Male'
WHERE Project3.dbo.Audience_demographic_data$.Gender = 'M'

---F to Female
UPDATE Project3.dbo.Audience_demographic_data$
SET Project3.dbo.Audience_demographic_data$.Gender = 'Female'
WHERE Project3.dbo.Audience_demographic_data$.Gender = 'F'
```

3. ***Duplicates***; For the duplicates records, I used a nested query along with a temporary table (CTE) created using the WITH clause, and employed the ROW_NUMBER window function in SQL. This approach allowed me to efficiently identify and eliminate redundant data.
```SQL
 --CHECKING FOR AND DELETING DUPLICATES
WITH deleteDuplicates AS
(
	SELECT Project3.dbo.movie_box_office_data$.[Movie ID],
	ROW_NUMBER() OVER(
	PARTITION BY Project3.dbo.movie_box_office_data$.[Movie ID] ORDER BY Project3.dbo.movie_box_office_data$.[Title]) AS row_num
	FROM Project3.dbo.movie_box_office_data$
)
DELETE
FROM deleteDuplicates
WHERE row_num > 1
```

4. ***Multiple Data Tables***; Additionally, the original dataset was spread across multiple Excel sheets. After individually cleaning these tables using SQL, I used JOIN operations to consolidate the data and transfer it to PowerBI for visualization. UNION wasn't applicable, as the tables lacked matching column structures required for merging into a single table.
```SQL
 SELECT * FROM Project3.dbo.movie_box_office_data$
JOIN Project3.dbo.Audience_demographic_data$
ON Project3.dbo.movie_box_office_data$.[Movie ID] = Project3.dbo.Audience_demographic_data$.[Movie ID]
JOIN Project3.dbo.Critical_review_data$
ON Project3.dbo.Audience_demographic_data$.[Movie ID] = Project3.dbo.Critical_review_data$.[Movie ID]
```
## Insights and Recommendations
- **Box Office Perfromance Insights**
  - The financial overview indicates that movies have generally been highly profitable, often earning well beyond their production budgets.
  - Horror films, in particular, outperformed broader genres like action, likely due to their lower production costs combined with strong box office returns.
  - Directors such as Brittany Webster and Catherine Good consistently produce high-revenue films, highlighting their potential value to studios.
  - Additionally, there is clear evidence of market recovery and growth following 2019.
 
  **Recommendations**



