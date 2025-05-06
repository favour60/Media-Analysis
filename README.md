# Media Performance Analysis

Historical Movie perfromance from 2015 to 2023

### Project Objective

A little bit of backstory to this project, a private entertainment company is planning to launch a new show analyzing the trends and performance of blockbuster movies from 2015 to 2023, they want to avoid subjective opinions and focus on data-driven insights.

So, this project aims to explore themes like Box Office Performance, Audience demographics, Critical reception, Streaming platform popularity in order to generate meaningful insights that could influence future content strategies

### Key Metrics to note

- Box Office (Total & Average)
- Production Budget (Total & Average)
- Average Audience Age & Rating
- Average Critical Review Score

### Tools and Steps involved in this project

1. Extracted raw Excel data into **SQL** and conducted thorough cleaning
2. Used SQL for tasks such as age group categorization and null handling
3. Exported the cleaned data into **PowerBI** for visualization and trend analysis

### Challenges & Solutions

- ***Null Values***; I observed that the dataset contained numerous Null values, particularly in fields like box office gross, production budget, and age. To address this, I applied average imputation in SQL to fill these gaps, preserving valuable data for more accurate analysis. However, for columns such as date, director, and movie title, I opted to use the DELETE statement to remove rows with missing entries.

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

- ***Inconsistent labels***; I also identified inconsistencies in the gender labels. To resolve this, I used the UPDATE statement in SQL to standardize gender entries (e.g. ensuring all values consistently reflected "Male" or "Female").

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

- ***Duplicates***; For the duplicates records, I used a nested query along with a temporary table (CTE) created using the WITH clause, and employed the ROW_NUMBER window function in SQL. This approach allowed me to efficiently identify and eliminate redundant data.

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

- ***Multiple Data Tables***; Additionally, the original dataset was spread across multiple Excel sheets. After individually cleaning these tables using SQL, I used JOIN operations to consolidate the data and transfer it to PowerBI for visualization. UNION wasn't applicable, as the tables lacked matching column structures required for merging into a single table.

```SQL
 SELECT * FROM Project3.dbo.movie_box_office_data$
JOIN Project3.dbo.Audience_demographic_data$
ON Project3.dbo.movie_box_office_data$.[Movie ID] = Project3.dbo.Audience_demographic_data$.[Movie ID]
JOIN Project3.dbo.Critical_review_data$
ON Project3.dbo.Audience_demographic_data$.[Movie ID] = Project3.dbo.Critical_review_data$.[Movie ID]
```

### Insights and Recommendations

- **Box Office Perfromance Insights**
  - The financial overview indicates that movies have generally been highly profitable, often earning well beyond their production budgets.
  - Horror films, in particular, outperformed broader genres like action, likely due to their lower production costs combined with strong box office returns.
  - Directors such as Brittany Webster and Catherine Good consistently produce high-revenue films, highlighting their potential value to studios.
  - Additionally, there is clear evidence of market recovery and growth following 2019.
 
  **Recommendations**
  - To maximize profit margins, it's important to maintain disciplined production budgets, especially in lower-cost genres like Horror.
  - Investing in directors with a track record of box office success and focusing on high-performing genres such Horror, Sci-Fi, and Romance can boost returns.
  - Exploring hybrid genres may help attract broader audiences while continuing to produce a variety of films with an emphasis on those that show strong financial performance.
  - Additionally, aligning marketing strategies and release schedules with peak cinema periods based on historical trends can enhance overall success.
 
![Movie box analysis 3 - Power BI Desktop (May 2024) 5_4_2025 3_55_00 PM](https://github.com/user-attachments/assets/b1b676ca-18af-410e-a7ba-3b32a8a21c51)

- **Audience Demographics Insights**
  - Middle aged individuals show a strong prefernce for thriller movies compared to both adolescents and older alduts, while older aldutls tend to favor romance, sci-fi, thriller, drama, and action more than other age groups. Adolescents, on the other hand, lean more toward drama.
  - Across all genres, females make up a larger portion of the audience, indicating they are a more engaged or dominant demographic in movie consumption.
  - Viewing habits also vary by genre, with horror, comedy, and sci-fi being primarily consumed through downloads, romance and action movies through CD purchases, and thriller and drama films being most popular on streaming platforms.

  **Recommendations**
  - Tailoring genre-based marketing by age-group -- such as targeting thrillers to middle-aged viewers, promoting romance, sci-fi, drama, and action to older adults, and focusing drama on adolescents -- can significantly improve engagement, satisfaction, and audience retention.
  - Market strategies and content creation can be more effective when tailored to female preferences, while still exploring ways to engage the male audience
  - To maximize reach and revenue, it's essential to align distribution strategies with genre performance. For example, Horror, Comedy, and Sci-Fi genres thrive through downloads, so investing in promotional campaigns, exclusive downloadable content, and partnerships with platforms like iTunes or Google Play is beneficial. Romance and Action movies, which are more commonly purchased, can gain traction through bundles, collector's editions, and discounts on popular purchase platforms like Amazon or DVD retailers. For Thriller and Drama, which are often streamed, enhancing the streamihg experience through strong licensing deals, optimized discoverability, and high-quality subtitles or dubbing is key.

![Movie box analysis 3 - Power BI Desktop (May 2024) 5_6_2025 4_24_23 AM](https://github.com/user-attachments/assets/9f00f5dd-b7a4-4959-88a2-c3660564295f)

- **Critical Reception Insights**
  - The analysis reveals significant gaps between audience ratings and critical review score. For instance, the movie *Hotel Act South* received a high audience rating of 7, well above average, but a low critical review score of 43. Conversely, *Effort* earned a strong critical score of 79, yet had a very low audience rating of 1. These discrepancies may stem from differing expectations and priorities between critics and viewers, as well as potential cultural or social influences, lack of market transparency, or the effects of hype or backlash.

  **Recommendations**
  - Enhancing transparency in both critic and audience scores is essential, including diversifying the pool of critics to represent various cultures, age groups, and backgrounds for a more inclusive reflection of audience experiences.
  - Implementing measures like requiring proof of ticket purchase or streaming access can help prevent review bombimng, while encouraging detailed reviews instead of basic ratings can offer more meaningful insights.
  - Providing accurate trailers and promotional content can manage audience expectations, and fostering better communication between critics and viewers can help bridge their differing perspectives and build mutual understanding.

 ![Movie box analysis 3 - Power BI Desktop (May 2024) 5_5_2025 7_10_53 AM](https://github.com/user-attachments/assets/abe49700-1cf3-481c-8823-211f7f71a7d5)
 
### Limitation and Conclusion

Due to the significant amount of missing values contained in the dataset that had to either be removed or filled using average imputation, the overall data quality and accuracy were affected.

However, this project provided a valuable opportunity to strengthen my skills in data cleaning using SQL and in creating visualizations with PowerBI. It also reinforced the growing importance of data-driven insights across nearly every industry worldwide.





