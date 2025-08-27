# Spotify Advanced SQL Project
Project Category: Advanced
[Click Here to get Dataset](https://www.kaggle.com/datasets/sanjanchaudhari/spotify-dataset)

![Spotify Logo](spotify_logo.PNG)

## Overview
This project uses **SQL** to analyze a Spotify dataset that contains different properties about tracks, albums, and artists. In addition to conducting SQL queries of various complexity (simple, medium, and advanced), it covers the entire process of normalizing a denormalized dataset and extracting insightful information from it.

```sql
-- create table
DROP TABLE IF EXISTS spotify;
CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);
```
## Project Steps

### 1. Data Exploration
Before diving into SQL, it’s important to understand the dataset thoroughly. The dataset contains attributes such as:
- `Artist`: The performer of the track.
- `Track`: The name of the song.
- `Album`: The album to which the track belongs.
- `Album_type`: The type of album (e.g., single or album).
- Various metrics such as `danceability`, `energy`, `loudness`, `tempo`, and more.

### 4. Querying the Data
After the data is inserted, various SQL queries can be written to explore and analyze the data.
#### Basic EDA:
```sql
-- 1. Count total number of records in the dataset
SELECT COUNT(*) 
FROM spotify;

-- 2. Count the number of unique artists
SELECT COUNT(DISTINCT artist) 
FROM spotify;


-- 3. Find all unique album types
SELECT DISTINCT album_type 
FROM spotify;

-- 4. Find the longest song (maximum duration in minutes)
SELECT MAX(duration_min) 
FROM spotify;

-- 5. Find the shortest song (minimum duration in minutes)
SELECT MIN(duration_min) 
FROM spotify;

-- 6. Retrieve all songs with zero duration (potential data errors)
SELECT * 
FROM spotify
WHERE duration_min = 0;

-- 7. Delete records with zero duration (data cleaning step)
DELETE 
FROM spotify
WHERE duration_min = 0;

-- 8. Find all unique channels
SELECT DISTINCT channel 
FROM spotify;

-- 9. Find all unique platforms where songs are most played
SELECT DISTINCT most_played_on 
FROM spotify;

```




#### Important Analysis has been done
1. Retrieve the names of all tracks that have more than 1 billion streams.
```sql

SELECT track, artist, streams 
FROM spotify 
WHERE stream > 1000000000;
```
2. List all albums along with their respective artists.
```sql
SELECT DISTINCT album,artist
FROM spotify
ORDERED BY 1;
```
3. Get the total number of comments for tracks where `licensed = TRUE`.
```sql
SELECT SUM(comments) as total_comments
FROM spotify
WHERE licensed = "true";

```


4. Find all tracks that belong to the album type `single`.
```sql
SELECT *
FROM spotify
WHERE album_type = "single";
```

5. Count the total number of tracks by each artist.
```sql
SELECT 	artist, COUNT(*) as total_no_songs
FROM spotify
GROUP BY artist
ORDERED BY 2;
```

6. Calculate the average danceability of tracks in each album.
```sql
SELECT album, avg(danceability) as avg_danceability
FROM spotify
GROUP BY 1
ORDERED BY 2 DESC;
```

7. Find the top 5 tracks with the highest energy values.
```sql
SELECT track, MAX(energy)
FROM spotify
GROUP BY 1
ORDERED BY 2 DESC
LIMIT 5;

```

8. List all tracks along with their views and likes where `official_video = TRUE`.
```sql
SELECT track, views, likes 
FROM spotify 
WHERE official_video = TRUE;
```

9. For each album, calculate the total views of all associated tracks.
```sql
SELECT album, SUM(views) AS total_views 
FROM spotify 
GROUP BY album 
ORDER BY total_views DESC;
```

10. Retrieve the track names that have been streamed on Spotify more than YouTube.
```sql
SELECT track, artist, stream, views 
FROM spotify 
WHERE most_played_on = 'Spotify' AND stream > views;
```

11. Find the top 3 most-viewed tracks for each artist using window functions.
```sql
SELECT artist, track, views
FROM (
    SELECT artist, track, views,
           ROW_NUMBER() OVER (PARTITION BY artist ORDER BY views DESC) AS rn
    FROM spotify
) t
WHERE rn <= 3;
```
12. Write a query to find tracks where the liveness score is above the average.
```sql
SELECT track, artist, liveness
FROM spotify
WHERE liveness > (SELECT AVG(liveness) FROM spotify);
```

13. **Use a `WITH` clause to calculate the difference between the highest and lowest energy values for tracks in each album.**
```sql
WITH cte
AS
(SELECT 
	album,
	MAX(energy) as highest_energy,
	MIN(energy) as lowest_energery
FROM spotify
GROUP BY 1
)
SELECT 
	album,
	highest_energy - lowest_energery as energy_diff
FROM cte
ORDER BY 2 DESC
```
   
14. Find tracks where the energy-to-liveness ratio is greater than 1.2.
```sql
SELECT track, artist, energy, liveness, 
       (energy / NULLIF(liveness, 0)) AS energy_liveness_ratio
FROM spotify
WHERE (energy / NULLIF(liveness, 0)) > 1.2;
```



15. Calculate the cumulative sum of likes for tracks ordered by the number of views, using window functions.
```sql
SELECT track, artist, views, likes,
       SUM(likes) OVER (ORDER BY views DESC ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS cumulative_likes
FROM spotify;
```

## Technology Stack
- **Database**: PostgreSQL
- **SQL Queries**: DDL, DML, Aggregations, Joins, Subqueries, Window Functions
- **Tools**: pgAdmin 4 (or any SQL editor), PostgreSQL (via Homebrew, Docker, or direct installation)

## How to Run the Project
1. Install PostgreSQL and pgAdmin (if not already installed).
2. Set up the database schema and tables using the provided normalization structure.
3. Insert the sample data into the respective tables.
4. Execute SQL queries to solve the listed problems.

---

## Next Steps
- **Visualize the Data**: Use a data visualization tool like **Tableau** or **Power BI** to create dashboards based on the query results.
- **Expand Dataset**: Add more rows to the dataset for broader analysis and scalability testing.


---


## License
This project is licensed under the MIT License.
