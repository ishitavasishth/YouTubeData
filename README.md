# # YouTube Video Analytics SQL Project

## About Dataset
This dataset contains two files for analyzing the relationship between the popularity of a certain video and the most relevant/liked comments of said video.

### File Descriptions
**videos-stats.csv:**
This file contains some basic information about each video, such as the title, likes, views, keyword, and comment count.

**comments.csv:**
For each video in videos-stats.csv, comments.csv contains the top ten most relevant comments as well as said comments' sentiments and likes.

### Column Descriptions
**videos-stats.csv:**

- **Title:** Video Title.
- **Video ID:** The Video Identifier.
- **Published At:** The date the video was published in YYYY-MM-DD.
- **Keyword:** The keyword associated with the video.
- **Likes:** The number of likes the video received. If this value is -1, the likes are not publicly visible.
- **Comments:** The number of comments the video has. If this value is -1, the video creator has disabled comments.
- **Views:** The number of views the video got.

**comments.csv:**

- **Video ID:** The Video Identifier.
- **Comment:** The comment text.
- **Likes:** The number of likes the comment received.
- **Sentiment:** The sentiment of the comment. A value of 0 represents a negative sentiment, while values of 1 or 2 represent neutral and positive sentiments respectively.

### Applicability
- Sentiment Analysis with comments
- Text Generation with comments
- Predicting video likes from comment information
- Popularity Analysis by Keyword
- Popularity Analysis
- Prediction video views from comment information/video statistics
- In-depth EDA of the Data

## Project Overview
This project aims to analyze YouTube video performance using SQL queries. We explore video engagement, sentiment analysis, and predictive modeling to gain insights into factors contributing to video success.

## Data Sources
The project uses two datasets:
1. **youtube_video**
    - Contains video metadata such as title, video ID, publish date, keyword, likes, comments, and views.
2. **youtube_comments**
    - Includes comments on videos with corresponding sentiments and likes.

## Database Schema
```sql
CREATE TABLE youtube_video (
    Title VARCHAR(1000),
    Video_ID VARCHAR(100),
    Published_At VARCHAR(100),
    Keyword VARCHAR(100),
    Likes INT,
    Comments BIGINT,
    Views BIGINT
);

CREATE TABLE youtube_comments (
    Video_ID VARCHAR(100),
    Comment VARCHAR(100000),
    Likes INT,
    Sentiment INT
);
```

## Key Questions and Insights

### Exploratory Data Analysis (EDA)

#### 1. What is the average number of likes, views, and comments across all videos?
```sql
SELECT video_id, ROUND(AVG(likes),2) AS avg_likes, ROUND(AVG(views),2) AS avg_views, ROUND(AVG(comments),2) AS avg_comments
FROM youtube_video
GROUP BY video_id;
```
**Insight:** Provides an overview of engagement across videos.

#### 2. Which keyword is associated with the highest average views?
```sql
SELECT keyword, AVG(views) AS avg_views
FROM youtube_video
GROUP BY keyword
ORDER BY avg_views DESC
LIMIT 1;
```
**Insight:** Identifies the keyword driving the most views.

#### 3. How does the sentiment of comments correlate with the number of likes on videos?
```sql
SELECT CORR(likes, comments) AS correlation
FROM youtube_video
WHERE comments IS NOT NULL AND likes IS NOT NULL;
```
**Insight:** A strong positive relationship suggests higher likes lead to more comments.

#### 4. What percentage of videos have comments disabled or likes hidden?
```sql
SELECT COUNT(CASE WHEN comments = -1 OR likes = -1 THEN 1 END) * 100.0 / COUNT(video_id) AS video_percentage
FROM youtube_video;
```
**Insight:** 47.8% of videos have comments disabled or likes hidden.

### Data Analysis & Modeling

#### 5. Can we predict the number of likes based on keyword and sentiment?
```sql
SELECT yv.video_id, yv.keyword, yc.sentiment, SUM(yv.likes) AS total_likes
FROM youtube_video AS yv
INNER JOIN youtube_comments AS yc USING(video_id)
WHERE yv.likes IS NOT NULL AND yc.sentiment IS NOT NULL AND yv.keyword IS NOT NULL
GROUP BY yv.video_id, yv.keyword, yc.sentiment
ORDER BY total_likes DESC;
```
**Insight:** Keywords like "sports" tend to have positive sentiment and high likes.

#### 6. Can sentiment-based models predict high or low views?
```sql
SELECT video_id, ROUND(AVG(sentiment),2) AS avg_sentiment, SUM(views) AS total_views,
CASE WHEN SUM(views) > 50000 THEN 'high views' ELSE 'low views' END AS view_threshold
FROM youtube_video
INNER JOIN youtube_comments USING(video_id)
WHERE views IS NOT NULL
GROUP BY video_id;
```
**Insight:** Sentiment can be a useful predictor of views.

### Advanced Analytics & Machine Learning

#### 7. Using clustering, can we segment videos based on engagement and sentiment?
```sql
SELECT video_id, keyword,
ROUND(AVG(sentiment),2) AS avg_sentiment,
CASE WHEN SUM(yv.likes + yv.comments) > 50000 THEN 'high engagement' ELSE 'low engagement' END AS engagement_diff,
ROUND(SUM(yv.likes + yv.comments) / NULLIF(SUM(views), 0), 2) AS engagement_ratio
FROM youtube_video AS yv
INNER JOIN youtube_comments AS yc USING(video_id)
WHERE yv.likes IS NOT NULL AND comments IS NOT NULL
GROUP BY video_id, keyword
ORDER BY engagement_ratio DESC;
```
**Insight:** Enables segmentation of videos for targeted strategies.

## Conclusion
This project demonstrates the power of SQL in analyzing YouTube data for valuable business insights, including engagement drivers, sentiment trends, and predictive modeling.


---





