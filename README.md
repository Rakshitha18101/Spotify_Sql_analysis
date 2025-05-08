Spotify Advanced SQL Project and Query Optimization P-6
Project Category: Advanced Click Here to get Dataset

![](https://www.vecteezy.com/png/42148659-spotify-logo-spotify-social-media-icon)


**Overview**
This project involves analyzing a Spotify dataset with various attributes about tracks, albums, and artists using SQL. It covers an end-to-end process of normalizing a denormalized dataset, performing SQL queries of varying complexity (easy, medium, and advanced), and optimizing query performance. The primary goals of the project are to practice advanced SQL skills and generate valuable insights from the dataset.

*-- create table*
```
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

***Project Steps***

**Data Exploration**
Before diving into SQL, it’s important to understand the dataset thoroughly. The dataset contains attributes such as:

1.Artist: The performer of the track.
2.Track: The name of the song.
3.Album: The album to which the track belongs.
-4.Album_type: The type of album (e.g., single or album).
Various metrics such as danceability, energy, loudness, tempo, and more.

 **Querying the Data**
After the data is inserted, various SQL queries can be written to explore and analyze the data. Queries are categorized into easy, medium, and advanced levels to help progressively develop SQL proficiency.

#*Easy Queries*#
Simple data retrieval, filtering, and basic aggregations.

#*Medium Queries*#
More complex queries involving grouping, aggregation functions, and joins.

#*Advanced Queries*#
Nested subqueries, window functions, CTEs, and performance optimization.

***15 Practice Questions***

*Easy Level*
1.Retrieve the names of all tracks that have more than 1 billion streams.
```
SELECT * FROM Spotify 
where stream > 1000000000
```
2.List all albums along with their respective artists.

```
select distinct artist,
album
from spotify
```

3.Get the total number of comments for tracks where licensed = TRUE.

```
Select sum(comments) as total_comments 
from spotify 
where licensed='true'
```

4.Find all tracks that belong to the album type single.

```
SELECT * FROM Spotify
where album_type ilike '%single%'
```

5.Count the total number of tracks by each artist.

```
SELECT distinct artist,
COUNT(*) as total_tracks
FROM Spotify
group by 1
order by 2 desc
```

*Medium Level*
1.Calculate the average danceability of tracks in each album.
```
SELECT 
album,
avg(danceability) as avg_danceability
FROM Spotify
group by 1
order by 2 desc
```
2.Find the top 5 tracks with the highest energy values.
```
SELECT track ,energy FROM SPOTIFY
order by energy desc
limit 5
```
3.List all tracks along with their views and likes where official_video = TRUE.
```
SELECT track,
sum(views) as total_views,
sum(likes) as total_views 
from spotify
where official_video = TRUE
group by 1
order by 2 desc
```
4.For each album, calculate the total views of all associated tracks.
```
SELECT album,track,
sum(views) as total_views
from spotify
group by 1,2
order by 3 desc
```
5.Retrieve the track names that have been streamed on Spotify more than YouTube.
```

SELECT * FROM
(SELECT track,
COALESCE(SUM(CASE WHEN most_played_on='Youtube' THEN stream END),0) as Stream_on_Youtube,
COALESCE(SUM(CASE WHEN most_played_on='Spotify' THEN stream END),0) as Stream_on_Spotify
from spotify
group by 1
) as t1
WHERE Stream_on_Spotify>Stream_on_Youtube
and 
	  Stream_on_Youtube<>0
```

*Advanced Level*
1.Find the top 3 most-viewed tracks for each artist using window functions.
```
WITH artist AS (
    SELECT artist, SUM(views) OVER(PARTITION BY artist) AS total_views
    FROM spotify
)
SELECT artist, views
FROM (
    SELECT artist, views, 
           RANK() OVER(ORDER BY views DESC) AS ranking
    FROM spotify
) RankedArtists
WHERE ranking <= 3;
```
2.Write a query to find tracks where the liveness score is above the average.
```
SELECT track, liveness
FROM spotify
WHERE liveness > (SELECT AVG(liveness) FROM spotify)
ORDER BY liveness DESC;
```
3.Use a WITH clause to calculate the difference between the highest and lowest energy values for tracks in each album.
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
4.Find tracks where the energy-to-liveness ratio is greater than 1.2.
```
WITH AlbumEnergy AS (
    SELECT album, 
           MAX(energy) AS max_energy, 
           MIN(energy) AS min_energy
    FROM spotify
    GROUP BY album
)
SELECT album, (max_energy - min_energy) AS energy_difference
FROM AlbumEnergy;
```
5.Calculate the cumulative sum of likes for tracks ordered by the number of views, using window functions.
```
SELECT track, artist, energy, liveness,
       (energy / liveness) AS energy_liveness_ratio
FROM spotify
WHERE (energy / liveness) > 1.2
ORDER BY energy_liveness_ratio DESC;
```
6.Here’s an updated section for your Spotify Advanced SQL Project and Query Optimization README, focusing on the query optimization task you performed. You can include the specific screenshots and graphs as described.
```
SELECT track, artist, views, likes,
       SUM(likes) OVER (ORDER BY views DESC) AS cumulative_likes
FROM spotify;
```
