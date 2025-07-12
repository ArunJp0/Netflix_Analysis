# Netflix Data Analysis Using SQL

## Project Overview

- **Project Title** : Netflix Data Analysis
- **Level** : Beginner
- **Database** : netflix_db

This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

## Objectives

1. Analyze the distribution of content types (movies vs TV shows).
2. Identify the most common ratings for movies and TV shows.
3. List and analyze content based on release years, countries, and durations.
4. Explore and categorize content based on specific criteria and keywords.

## Project Structure

### 1. Database Setup

- **Database Creation**: The project starts by creating a database named netflix_db.
- **Table Creation**: A table named 'netflix' is created to store the movies and tv shows content data. The table structure includes columns for show_id, title, type, director, casts, country, date_added, release_year, rating, duration, listed_in, and description.

```sql
CREATE DATABASE netflix_db;

CREATE TABLE netflix
(
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
```

### 2. Data Analysis & Business Key Problems and Answers

1. **Count the Number of Movies vs TV Shows**
   ```sql
   select type, count(*) as total_content
   from netflix
   group by type;
   ```

2. **Find the Most Common Rating for Movies and TV Shows**
   ```sql
   select type, rating
      from
      (
      select type, rating,
      count(*) as rating_count,
      rank() over(partition by type order by count(*) desc) as mcr
      from netflix
      group by 1,2
      ) as tl
      where mcr = 1;
   ```

3. **List All Movies Released in a Specific Year (e.g., 2020)**
   ```sql
   select * from netflix
   where release_year = 2020 and type = 'Movie';
   ```

4. **Find the Top 5 Countries with the Most Content on Netflix**
   ```sql
   select SUBSTRING_INDEX(country, ',', 1) as country, count(show_id) as total_content
   from netflix
   WHERE country IS NOT NULL
   group by country
   order by 2 desc
   limit 5;
   ```

5. **Identify the Longest Movie**
   ```sql
   select title,
   cast(REPLACE(duration, ' min', '') as signed) as duration
   from netflix
   where type = 'Movie' and duration is not null
   order by duration desc
	 limit 1;
   ```

6. **Find Content Added in the Last 5 Years**
   ```sql
   select *
   from netflix
	 WHERE date_added >= DATE_SUB(current_date(), INTERVAL 5 year);
   ```

7. **Find All Movies/TV Shows by Director 'Rajiv Chilaka**
   ```sql
   select *
      from netflix
      where director like '%Rajiv Chilaka%';
   ```

8. **List All TV Shows with More Than 5 Seasons**
   ```sql
   select *
      from netflix
      where type = 'TV Show' and substring_index(duration, ' ', 1)  > 5; 
   ```

9. **Count the Number of Content Items in Each Genre**
    ```sql
      select SUBSTRING_INDEX(listed_in, ',', 1) as genre, count(show_id) as total_content
      from netflix
      group by genre
      order by 2 desc;
   ```

10. **Find each year and the average numbers of content release in India on netflix**
    ```sql
    select year(date_added) as year,
       count(*) as content_released,
       round(count(*) / (select count(*) from netflix where country = 'India') * 100,2) as avg_content_released
       from netflix
       where country = 'India'
       group by 1
       order by 1 desc;
    ```

11. **List All Movies that are Documentaries**
    ```sql
    select count(*)
    from netflix
    where listed_in like '%Documentaries%';
    ```

12. **Find All Content Without a Director**
    ```sql
    select * 
    from netflix
    where director is null;
    ```

13. **Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years**
    ```sql
    select * 
    from netflix
    where casts like '%Salman khan%'
    and release_year > extract(year from current_date) - 11;
    ```

14. **Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India**
    ```sql
     select SUBSTRING_INDEX(casts, ',', 1) as actors, count(*) as number_of_movies
     from netflix
     where country = 'India' and casts is not null
     group by 1
     order by 2 desc
     limit 10;
    ```

15. **Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords**
    ```sql
       select category, count(*) as content_count
       from
       (
       select 
       case 
       when description like '%kill%' or description like '%violence%' then 'Bad'
       else 'Good'
       end as category
       from netflix
       ) as category_content
       group by category;
    ```
**Objective**: Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.

# Findings

- **Content Distribution**: The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
- **Common Ratings**: Insights into the most common ratings provide an understanding of the content's target audience.
- **Geographical Insights**: The top countries and the average content releases by India highlight regional content distribution.
- **Content Categorization**: Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.

This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.

# Conclusion

This project serves as a comprehensive introduction to SQL for data analysts, covering database setup, data cleaning, exploratory data analysis, and business-driven SQL queries. The findings from this project can help drive business decisions by understanding movies and tv shows content analysis by their date added, release year, rating, duration etc.



