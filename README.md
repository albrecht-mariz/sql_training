## SQL Training 

### Data Lemur

[Histogram of Tweets](https://datalemur.com/questions/sql-histogram-tweets)

```SQL

WITH
  count_tweets AS (
  SELECT
    user_id,
    COUNT(*) AS tweet_count_per_user
  FROM
    tweets
  WHERE
    tweet_date >= '01/01/2022 00:00:00'
  GROUP BY
    1 )
SELECT
  tweet_count_per_user AS tweet_bucket,
  COUNT(*) AS users_num
FROM
  count_tweets
GROUP BY
  1
ORDER BY
  2 desc

```
[Laptop vs. Mobile Viewership](https://datalemur.com/questions/laptop-mobile-viewership) 

```SQL

SELECT
  SUM(CASE
      WHEN device_type = 'laptop' THEN 1
    ELSE
    0
  END
    ) AS laptop_views,
  SUM(CASE
      WHEN device_type IN ('tablet', 'phone') THEN 1
    ELSE
    0
  END
    ) AS mobile_views
FROM
  viewership
  
```