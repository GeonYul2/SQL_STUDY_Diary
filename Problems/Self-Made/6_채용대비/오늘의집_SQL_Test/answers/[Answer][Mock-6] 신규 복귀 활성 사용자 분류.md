# [Answer][Mock-6] 신규 복귀 활성 사용자 분류

## 정답 SQL

```sql
WITH monthly_activity AS (
    SELECT DISTINCT
           user_id,
           DATE_FORMAT(activity_time, '%Y-%m') AS activity_month
    FROM user_activity_logs
),
base AS (
    SELECT user_id,
           activity_month,
           MIN(activity_month) OVER (PARTITION BY user_id) AS first_month,
           LAG(activity_month) OVER (PARTITION BY user_id ORDER BY activity_month) AS prev_month
    FROM monthly_activity
)
SELECT activity_month,
       COUNT(CASE WHEN activity_month = first_month THEN 1 END) AS new_users,
       COUNT(CASE
             WHEN activity_month <> first_month
              AND prev_month = DATE_FORMAT(DATE_SUB(STR_TO_DATE(CONCAT(activity_month, '-01'), '%Y-%m-%d'), INTERVAL 1 MONTH), '%Y-%m')
             THEN 1 END) AS current_users,
       COUNT(CASE
             WHEN activity_month <> first_month
              AND (prev_month IS NULL OR prev_month <> DATE_FORMAT(DATE_SUB(STR_TO_DATE(CONCAT(activity_month, '-01'), '%Y-%m-%d'), INTERVAL 1 MONTH), '%Y-%m'))
             THEN 1 END) AS resurrected_users
FROM base
GROUP BY activity_month
ORDER BY activity_month;
```

## 해설
- 먼저 유저별 월 활동을 dedup 합니다.
- `MIN()`과 `LAG()`로 첫 활동 월과 직전 활동 월을 구합니다.
- 바로 이전 달 여부를 비교해 current/resurrected를 나눕니다.
