## SQL Training 

### Data Lemur
PostgreSQL 14

[Spotify: Top 5 Artists](https://datalemur.com/questions/top-fans-rank)

`DENSE_RANK()`

```SQL

WITH
  top_artists AS (
  SELECT
    a.artist_name,
    DENSE_RANK() OVER (ORDER BY COUNT(s.song_id) DESC) AS artist_rank
  FROM
    artists a
  JOIN
    songs s
  ON
    a.artist_id = s.artist_id
  JOIN
    global_song_rank g
  ON
    s.song_id = g.song_id
  WHERE
    rank <= 10
  GROUP BY
    1 )
SELECT
  *
FROM
  top_artists
WHERE
  artist_rank <= 5

```

[Twitter: Tweets' Rolling Averages](https://datalemur.com/questions/rolling-average-tweets)

`ROWS BETWEEN n PRECEDING AND CURRENT ROW`

```SQL

SELECT
  user_id,
  tweet_date,
  ROUND(AVG(tweet_count) 
  OVER(PARTITION BY user_id 
  ORDER BY tweet_date 
  ROWS BETWEEN 2 PRECEDING AND CURRENT ROW),2) 
  AS rolling_avg
FROM
  tweets
  
```

[Twitter: Histogram of Tweets](https://datalemur.com/questions/sql-histogram-tweets)

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

[Snapchat: Sending vs. Opening Snaps](https://datalemur.com/questions/time-spent-snaps)

`CTE`
`WITH alias AS ()`
`FILTER()`

```SQL

WITH
  open_send AS (
  SELECT
    ag.age_bucket,
    SUM(a.time_spent) FILTER (
    WHERE
      a.activity_type = 'send') AS send_sum,
    SUM(a.time_spent) FILTER (
    WHERE
      a.activity_type = 'open') AS open_sum
  FROM
    activities a
  JOIN
    age_breakdown ag
  ON
    a.user_id = ag.user_id
  GROUP BY
    1 )
SELECT
  age_bucket,
  ROUND(100.0*send_sum / (send_sum + open_sum),2) AS send_perc,
  ROUND(100.0*open_sum / (send_sum + open_sum),2) AS open_perc
FROM
  open_send

```

[UBER: User's Third Transaction](https://datalemur.com/questions/sql-third-transaction)

`window function`
`ROW_NUMBER() OVER(PARTITION BY id ORDER BY date)`

```SQL

WITH
  transactions_ranked AS (
  SELECT
    *,
    ROW_NUMBER() OVER(PARTITION BY user_id ORDER BY transaction_date) AS rnk
  FROM
    transactions )
SELECT
  user_id,
  spend,
  transaction_date
FROM
  transactions_ranked
WHERE
  rnk = 3

```

[New York Times: Laptop vs. Mobile Viewership](https://datalemur.com/questions/laptop-mobile-viewership) 

`SUM(CASE)`

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

[Facebook: App Click-through Rate (CTR)](https://datalemur.com/questions/click-through-rate)

```SQL

SELECT
  app_id,
  ROUND(100.0 * SUM(
      CASE
        WHEN event_type = 'click' THEN 1
      ELSE
      0
    END
      )/ SUM(
      CASE
        WHEN event_type = 'impression' THEN 1
      ELSE
      0
    END
      ),2) AS ctr
FROM
  events
WHERE
  timestamp BETWEEN '2022-01-01'
  AND '2022-12-31'
GROUP BY 1

```
[TikTok: Second Day Confirmation](https://datalemur.com/questions/second-day-confirmation)

```SQL

SELECT
  user_id
FROM
  emails e
JOIN
  texts t
ON
  e.email_id = t.email_id
WHERE
  t.signup_action = 'Confirmed'
  AND (DATE(action_date) - DATE(signup_date)) = 1 
  
```