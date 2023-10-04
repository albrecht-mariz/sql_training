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