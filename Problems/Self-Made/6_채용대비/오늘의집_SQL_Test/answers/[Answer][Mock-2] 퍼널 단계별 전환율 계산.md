# [Answer][Mock-2] 퍼널 단계별 전환율 계산

## 정답 SQL

```sql
WITH daily_user_event AS (
    SELECT DISTINCT
           DATE(event_time) AS event_date,
           user_id,
           event_type
    FROM app_events
    WHERE event_type IN ('view_item', 'add_to_cart', 'purchase')
)
SELECT event_date,
       COUNT(DISTINCT CASE WHEN event_type = 'view_item' THEN user_id END) AS view_users,
       COUNT(DISTINCT CASE WHEN event_type = 'add_to_cart' THEN user_id END) AS cart_users,
       COUNT(DISTINCT CASE WHEN event_type = 'purchase' THEN user_id END) AS purchase_users,
       ROUND(
           COUNT(DISTINCT CASE WHEN event_type = 'add_to_cart' THEN user_id END)
           / NULLIF(COUNT(DISTINCT CASE WHEN event_type = 'view_item' THEN user_id END), 0),
           4
       ) AS view_to_cart_rate,
       ROUND(
           COUNT(DISTINCT CASE WHEN event_type = 'purchase' THEN user_id END)
           / NULLIF(COUNT(DISTINCT CASE WHEN event_type = 'add_to_cart' THEN user_id END), 0),
           4
       ) AS cart_to_purchase_rate
FROM daily_user_event
GROUP BY event_date
ORDER BY event_date;
```

## 해설
- 날짜/유저/이벤트 단위로 먼저 dedup 합니다.
- 그 위에서 조건부 `COUNT(DISTINCT user_id)`로 각 단계 유저 수를 계산합니다.
- 분모 0을 피하기 위해 `NULLIF`를 사용합니다.
