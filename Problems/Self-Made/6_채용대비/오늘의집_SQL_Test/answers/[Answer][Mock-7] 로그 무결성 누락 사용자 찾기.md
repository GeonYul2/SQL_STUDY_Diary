# [Answer][Mock-7] 로그 무결성 누락 사용자 찾기

## 정답 SQL

```sql
SELECT DISTINCT
       DATE(s1.event_time) AS event_date,
       s1.user_id
FROM service_events s1
WHERE s1.event_type = 'order_completed'
  AND NOT EXISTS (
      SELECT 1
      FROM service_events s2
      WHERE s2.user_id = s1.user_id
        AND DATE(s2.event_time) = DATE(s1.event_time)
        AND s2.event_type = 'payment_success'
  )
ORDER BY event_date, user_id;
```

## 해설
- `order_completed`를 기준 집합으로 잡습니다.
- 같은 날짜에 `payment_success`가 존재하지 않는 경우만 남깁니다.
