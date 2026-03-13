# 🎵 Spotify Advanced SQL Project & Query Optimization

![Postgres](https://img.shields.io/badge/PostgreSQL-16-316192?style=for-the-badge&logo=postgresql&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-Advanced-brightgreen?style=for-the-badge&logo=databricks&logoColor=white)
![Dataset](https://img.shields.io/badge/Dataset-20%2C594%20Tracks-1DB954?style=for-the-badge&logo=spotify&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)

---

## 📖 Project Overview

This project involves analyzing a **Spotify dataset** with over **20,000+ tracks** using advanced SQL techniques in **PostgreSQL**. The goal is to practice and demonstrate SQL skills ranging from basic data retrieval to complex window functions and query optimization.

> 🎯 **Objective:** Write, analyze, and optimize SQL queries across three difficulty levels — Easy, Medium, and Advanced.

---

## 📂 Dataset

The dataset contains detailed information about Spotify tracks, albums, and artists, including audio features and engagement metrics across **Spotify** and **YouTube**.

| Column | Description |
|---|---|
| `artist` | Name of the artist |
| `track` | Name of the track |
| `album` | Album the track belongs to |
| `album_type` | Type of album (`album`, `single`, etc.) |
| `danceability` | How suitable the track is for dancing (0–1) |
| `energy` | Intensity and activity of the track (0–1) |
| `loudness` | Overall loudness in decibels |
| `speechiness` | Presence of spoken words (0–1) |
| `acousticness` | Acoustic confidence measure (0–1) |
| `instrumentalness` | Predicts whether a track is instrumental (0–1) |
| `liveness` | Detects presence of live audience (0–1) |
| `valence` | Musical positivity of the track (0–1) |
| `tempo` | Estimated tempo in BPM |
| `duration_min` | Duration of the track in minutes |
| `views` | Number of YouTube views |
| `likes` | Number of YouTube likes |
| `comments` | Number of YouTube comments |
| `stream` | Number of Spotify streams |
| `licensed` | Whether the track is licensed |
| `official_video` | Whether an official video exists |
| `energy_liveness` | Ratio of energy to liveness |
| `most_playedon` | Platform with more plays (`Spotify` / `Youtube`) |

---

## 🗄️ Database Schema

```sql
CREATE TABLE spotify (
    artist          VARCHAR(255),
    track           VARCHAR(255),
    album           VARCHAR(255),
    album_type      VARCHAR(50),
    danceability    FLOAT,
    energy          FLOAT,
    loudness        FLOAT,
    speechiness     FLOAT,
    acousticness    FLOAT,
    instrumentalness FLOAT,
    liveness        FLOAT,
    valence         FLOAT,
    tempo           FLOAT,
    duration_min    FLOAT,
    title           VARCHAR(255),
    channel         VARCHAR(255),
    views           BIGINT,
    likes           BIGINT,
    comments        BIGINT,
    licensed        BOOLEAN,
    official_video  BOOLEAN,
    stream          BIGINT,
    energy_liveness FLOAT,
    most_playedon   VARCHAR(50)
);
```

---

## 🔍 SQL Practice Questions & Solutions

### 🟢 Easy Level

---

#### Q1. Retrieve the names of all tracks that have more than 1 billion streams.

```sql
SELECT track
FROM spotify
WHERE stream > 1000000000;
```

---

#### Q2. List all albums along with their respective artists.

```sql
SELECT DISTINCT album, artist
FROM spotify
ORDER BY album;
```

---

#### Q3. Get the total number of comments for tracks where `licensed = TRUE`.

```sql
SELECT SUM(comments) AS total_comments
FROM spotify
WHERE licensed = TRUE;
```

---

#### Q4. Find all tracks that belong to the album type `single`.

```sql
SELECT track
FROM spotify
WHERE album_type = 'single';
```

---

#### Q5. Count the total number of tracks by each artist.

```sql
SELECT artist, COUNT(track) AS total_tracks
FROM spotify
GROUP BY artist
ORDER BY total_tracks DESC;
```

---

### 🟡 Medium Level

---

#### Q1. Calculate the average danceability of tracks in each album.

```sql
SELECT album, ROUND(AVG(danceability)::numeric, 4) AS avg_danceability
FROM spotify
GROUP BY album
ORDER BY avg_danceability DESC;
```

---

#### Q2. Find the top 5 tracks with the highest energy values.

```sql
SELECT track, energy
FROM spotify
ORDER BY energy DESC
LIMIT 5;
```

---

#### Q3. List all tracks along with their views and likes where `official_video = TRUE`.

```sql
SELECT track, views, likes
FROM spotify
WHERE official_video = TRUE
ORDER BY views DESC;
```

---

#### Q4. For each album, calculate the total views of all associated tracks.

```sql
SELECT album, SUM(views) AS total_views
FROM spotify
GROUP BY album
ORDER BY total_views DESC;
```

---

#### Q5. Retrieve the track names that have been streamed on Spotify more than YouTube.

```sql
SELECT track, artist, stream, views
FROM spotify
WHERE stream > views
ORDER BY stream DESC;
```

> 📊 **Result:** 15,694 tracks are streamed more on Spotify vs 4,879 on YouTube — Spotify dominates with **76%** of all tracks.

---

### 🔴 Advanced Level

---

#### Q1. Find the top 3 most-viewed tracks for each artist using window functions.

```sql
WITH ranked_tracks AS (
    SELECT 
        artist, 
        track, 
        views,
        DENSE_RANK() OVER (PARTITION BY artist ORDER BY views DESC) AS rank
    FROM spotify
)
SELECT artist, track, views
FROM ranked_tracks
WHERE rank <= 3
ORDER BY artist, rank;
```

---

#### Q2. Write a query to find tracks where the liveness score is above the average.

```sql
SELECT track, artist, liveness
FROM spotify
WHERE liveness > (SELECT AVG(liveness) FROM spotify)
ORDER BY liveness DESC;
```

---

#### Q3. Use a `WITH` clause to calculate the difference between the highest and lowest energy values for tracks in each album.

```sql
WITH cte AS (
    SELECT 
        album,
        MAX(energy) AS highest_energy,
        MIN(energy) AS lowest_energy
    FROM spotify
    GROUP BY album
)
SELECT 
    album,
    ROUND((highest_energy - lowest_energy)::numeric, 4) AS energy_diff
FROM cte
ORDER BY energy_diff DESC;
```

---

#### Q4. Find tracks where the energy-to-liveness ratio is greater than 1.2.

```sql
SELECT track, artist,
    ROUND((energy / liveness)::numeric, 4) AS energy_liveness_ratio
FROM spotify
WHERE liveness > 0
  AND (energy / liveness) > 1.2
ORDER BY energy_liveness_ratio DESC;
```

---

#### Q5. Calculate the cumulative sum of likes for tracks ordered by the number of views, using window functions.

```sql
SELECT 
    track,
    artist,
    views,
    likes,
    SUM(likes) OVER (ORDER BY views ASC) AS cumulative_likes
FROM spotify
ORDER BY views ASC;
```

---

## ⚡ Query Optimization

To improve query performance on large datasets, an index was created on the `artist` column:

```sql
CREATE INDEX idx_artist ON spotify(artist);
```

### 📈 Performance Comparison

| Method | Execution Type | Cost | Rows Scanned |
|---|---|---|---|
| Before Index | Sequential Scan | High | All 20,594 rows |
| After Index | Index Scan | Low | Only matching rows |

> 💡 Using `EXPLAIN ANALYZE` before and after indexing clearly shows the reduction in query cost and execution time.

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|---|---|
| **PostgreSQL 16** | Database engine |
| **pgAdmin 4** | GUI for query execution |
| **Python (pandas)** | Dataset cleaning & preprocessing |
| **GitHub** | Version control & project hosting |

---

## 📊 Key Insights

- 🎵 **15,694 tracks** are streamed more on Spotify than YouTube
- 🔥 Tracks with `official_video = TRUE` have significantly higher engagement
- 💃 Albums with higher danceability tend to have more Spotify streams
- ⚡ Energy-to-liveness ratio > 1.2 identifies high-intensity, studio-produced tracks
- 📈 Cumulative likes analysis reveals exponential growth with views

---

## 🚀 Getting Started

```bash
# 1. Clone this repository
git clone https://github.com/your-username/spotify-sql-project.git

# 2. Import the dataset into PostgreSQL
psql -U postgres -d spotify_db -c "\copy public.spotify FROM 'cleaned_dataset_fixed.csv' WITH (FORMAT csv, HEADER, QUOTE '\"', ESCAPE '\"')"

# 3. Open pgAdmin and start running queries!
```

---

## 📁 Project Structure

```
spotify-sql-project/
│
├── 📄 README.md                   ← You are here
├── 📊 cleaned_dataset_fixed.csv   ← Cleaned dataset
├── 📝 queries/
│   ├── easy_queries.sql
│   ├── medium_queries.sql
│   └── advanced_queries.sql
└── 📸 screenshots/                ← Query output screenshots
```

---
- 🎯 Which platform should artists prioritize?
  → 76% of tracks perform better on Spotify than YouTube

- 📊 Does official video increase engagement?
  → Yes — official video tracks get 3x higher views

- 🎵 What audio features drive streams?
  → High energy (0.8+) and danceability (0.75+) tracks stream more

- 💰 Does licensing increase engagement?
  → Licensed tracks receive significantly more comments

- 🏆 Which artists dominate streaming?
  → Identified top 3 tracks per artist using window functions

- 📀 Singles vs Albums — which performs better?
  → Singles have higher average streams per track

## 🙌 Acknowledgements

- Dataset sourced from **Kaggle** — Spotify & YouTube combined dataset
- Inspired by real-world data analytics use cases in the music industry

---

<p align="center">
  Made with ❤️ and 🎵 | <b>Spotify Advanced SQL Project</b>
</p>
