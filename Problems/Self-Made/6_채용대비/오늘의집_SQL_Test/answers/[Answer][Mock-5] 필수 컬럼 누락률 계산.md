# [Answer][Mock-5] 필수 컬럼 누락률 계산

## 정답 SQL

```sql
SELECT DATE(event_time) AS log_date,
       COUNT(*) AS total_logs,
       SUM(CASE WHEN device_id IS NULL THEN 1 ELSE 0 END) AS missing_device_logs,
       ROUND(
           SUM(CASE WHEN device_id IS NULL THEN 1 ELSE 0 END) / NULLIF(COUNT(*), 0),
           4
       ) AS missing_rate
FROM tracking_logs
GROUP BY DATE(event_time)
ORDER BY log_date;
```

## 해설
- 날짜별 전체 건수와 누락 건수를 함께 집계합니다.
- 누락률은 조건부 합계를 전체 건수로 나눠 계산합니다.
