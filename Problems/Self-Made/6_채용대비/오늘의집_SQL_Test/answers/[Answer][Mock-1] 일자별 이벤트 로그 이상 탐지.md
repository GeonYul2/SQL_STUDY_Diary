# [Answer][Mock-1] 일자별 이벤트 로그 이상 탐지

## 정답 SQL

```sql
WITH daily AS (
    SELECT DATE(event_time) AS log_date,
           COUNT(*) AS event_count
    FROM event_logs
    GROUP BY DATE(event_time)
),
lagged AS (
    SELECT log_date,
           event_count,
           LAG(event_count) OVER (ORDER BY log_date) AS prev_event_count
    FROM daily
)
SELECT log_date,
       event_count,
       prev_event_count,
       ROUND((event_count - prev_event_count) / NULLIF(prev_event_count, 0), 4) AS change_ratio
FROM lagged
WHERE prev_event_count IS NOT NULL
  AND (
      (event_count - prev_event_count) / NULLIF(prev_event_count, 0) <= -0.5
      OR
      (event_count - prev_event_count) / NULLIF(prev_event_count, 0) >= 1.0
  )
ORDER BY log_date;
```

## 해설
- 먼저 날짜별 이벤트 건수를 집계합니다.
- `LAG()`로 전일 건수를 가져옵니다.
- 변화율을 계산한 뒤 급감/급증 조건으로 필터링합니다.
