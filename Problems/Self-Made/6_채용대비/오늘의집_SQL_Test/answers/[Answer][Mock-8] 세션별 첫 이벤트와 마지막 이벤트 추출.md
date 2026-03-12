# [Answer][Mock-8] 세션별 첫 이벤트와 마지막 이벤트 추출

## 정답 SQL

```sql
WITH ranked AS (
    SELECT session_id,
           event_type,
           event_time,
           ROW_NUMBER() OVER (
               PARTITION BY session_id
               ORDER BY event_time ASC, event_id ASC
           ) AS rn_first,
           ROW_NUMBER() OVER (
               PARTITION BY session_id
               ORDER BY event_time DESC, event_id DESC
           ) AS rn_last
    FROM session_events
),
first_event AS (
    SELECT session_id,
           event_type AS first_event_type,
           event_time AS first_event_time
    FROM ranked
    WHERE rn_first = 1
),
last_event AS (
    SELECT session_id,
           event_type AS last_event_type,
           event_time AS last_event_time
    FROM ranked
    WHERE rn_last = 1
)
SELECT f.session_id,
       f.first_event_type,
       f.first_event_time,
       l.last_event_type,
       l.last_event_time
FROM first_event f
JOIN last_event l
  ON f.session_id = l.session_id
ORDER BY f.session_id;
```

## 해설
- 세션별 첫 이벤트와 마지막 이벤트를 각각 `ROW_NUMBER()`로 구합니다.
- tie-breaker로 `event_id`를 함께 사용합니다.
