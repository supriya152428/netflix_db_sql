
# 🎬 Netflix Movies and TV Shows Data Analysis Using SQL

![Netflix](https://github.com/najirh/netflix_sql_project/blob/main/logo.png)

## 📌 Overview

This project focuses on exploring and analyzing Netflix’s movie and TV show catalog using SQL. The goal is to uncover insights into content distribution, genre trends, audience ratings, and regional production using real-world data.

---

## 🎯 Objectives

* Explore content patterns across years, genres, and regions.
* Find meaningful business insights using SQL queries.
* Develop problem-solving skills using PostgreSQL functions and logic.
* Strengthen SQL fundamentals like CTEs, window functions, string handling, and filtering.

---

## 📊 Dataset Information 

* **Source:** [Kaggle – Netflix Movies and TV Shows Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

---

## 🗃️ Table Schema

```sql
DROP TABLE IF EXISTS netflix;
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

---

## 🧠 Business Questions & SQL Queries

### 1️⃣ Count the Number of Movies vs TV Shows

```sql
SELECT 
    type,
    COUNT(*)
FROM netflix
GROUP BY 1;
```

**Purpose:** Check whether Netflix has more movies or TV shows.

---

### 2️⃣ Most Common Rating for Movies and TV Shows

```sql
WITH RatingCounts AS (
    SELECT 
        type,
        rating,
        COUNT(*) AS rating_count
    FROM netflix
    GROUP BY type, rating
),
RankedRatings AS (
    SELECT 
        type,
        rating,
        rating_count,
        RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank
    FROM RatingCounts
)
SELECT 
    type,
    rating AS most_frequent_rating
FROM RankedRatings
WHERE rank = 1;
```

**Purpose:** Discover the most frequent audience ratings across content types.

---

### 3️⃣ List All Movies Released in 2020

```sql
SELECT * 
FROM netflix
WHERE release_year = 2020;
```

**Purpose:** Get all titles released in a specific year.

---

### 4️⃣ Top 5 Countries with Most Content on Netflix

```sql
SELECT * 
FROM
(
    SELECT 
        UNNEST(STRING_TO_ARRAY(country, ',')) AS country,
        COUNT(*) AS total_content
    FROM netflix
    GROUP BY 1
) AS t1
WHERE country IS NOT NULL
ORDER BY total_content DESC
LIMIT 5;
```

**Purpose:** Identify the countries producing the most Netflix content.

---

### 5️⃣ Identify the Longest Movie on Netflix

```sql
SELECT 
    *
FROM netflix
WHERE type = 'Movie'
ORDER BY SPLIT_PART(duration, ' ', 1)::INT DESC;
```

**Purpose:** Spot the movie with the highest runtime.

---

### 6️⃣ Content Added in the Last 5 Years

```sql
SELECT *
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';
```

**Purpose:** Analyze how much content was added recently.

---

### 7️⃣ Movies/TV Shows by Director 'Rajiv Chilaka'

```sql
SELECT *
FROM (
    SELECT 
        * ,
        UNNEST(STRING_TO_ARRAY(director, ',')) AS director_name
    FROM netflix
) AS t
WHERE director_name = 'Rajiv Chilaka';
```

**Purpose:** List content created by a specific director.

---

### 8️⃣ TV Shows with More Than 5 Seasons

```sql
SELECT *
FROM netflix
WHERE type = 'TV Show'
  AND SPLIT_PART(duration, ' ', 1)::INT > 5;
```

**Purpose:** Identify long-running TV shows.

---

### 9️⃣ Content Count in Each Genre

```sql
SELECT 
    UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
    COUNT(*) AS total_content
FROM netflix
GROUP BY 1;
```

**Purpose:** Count how many titles exist in each genre.

---

### 🔟 Top 5 Years with Highest Avg Content Release from India

```sql
SELECT 
    country,
    release_year,
    COUNT(show_id) AS total_release,
    ROUND(
        COUNT(show_id)::numeric / 
        (SELECT COUNT(show_id) FROM netflix WHERE country = 'India')::numeric * 100, 2
    ) AS avg_release
FROM netflix
WHERE country = 'India'
GROUP BY country, release_year
ORDER BY avg_release DESC
LIMIT 5;
```

**Purpose:** Measure India's content growth over time.

---

### 1️⃣1️⃣ List All Documentaries

```sql
SELECT * 
FROM netflix
WHERE listed_in LIKE '%Documentaries';
```

**Purpose:** Filter Netflix content categorized as documentaries.

---

### 1️⃣2️⃣ Content Without a Director

```sql
SELECT * 
FROM netflix
WHERE director IS NULL;
```

**Purpose:** Identify entries missing the director field.

---

### 1️⃣3️⃣ Salman Khan Movies in Last 10 Years

```sql
SELECT * 
FROM netflix
WHERE casts LIKE '%Salman Khan%'
  AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;
```

**Purpose:** Analyze Salman Khan’s recent presence on Netflix.

---

### 1️⃣4️⃣ Top 10 Actors in Indian Movies

```sql
SELECT 
    UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor,
    COUNT(*)
FROM netflix
WHERE country = 'India'
GROUP BY actor
ORDER BY COUNT(*) DESC
LIMIT 10;
```

**Purpose:** Spot India’s most frequently cast actors on Netflix.

---

### 1️⃣5️⃣ Categorize Content as 'Good' or 'Bad' Based on Keywords

```sql
SELECT 
    category,
    COUNT(*) AS content_count
FROM (
    SELECT 
        CASE 
            WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
            ELSE 'Good'
        END AS category
    FROM netflix
) AS categorized_content
GROUP BY category;
```

**Purpose:** Detect violent content using keyword logic.

---

## 📈 Findings & Conclusion

* Netflix’s library is movie-heavy with a global presence, especially from the US and India.
* The most common ratings include TV-MA and TV-14, showing a focus on adult audiences.
* Genre-based filtering and sentiment detection can support better content recommendations.
* Using SQL, complex questions around duration, keywords, casting, and year-wise trends can be answered effectively.

---

## 👩‍💻 Author – Supriya Yadav

> B.Tech CSE student | Aspiring Data Analyst
> Passionate about real-world datasets, visual storytelling, and SQL problem-solving.

📎 [LinkedIn] (https://www.linkedin.com/in/supriya-yadav-b1564b319)
    [git hub] github.com/supriya152428 








   


