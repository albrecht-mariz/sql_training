## SQL Training 

### Data Lemur
PostgreSQL 14

[User's Third Transaction](https://datalemur.com/questions/sql-third-transaction)

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

[App Click-through Rate (CTR)](https://datalemur.com/questions/click-through-rate)

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