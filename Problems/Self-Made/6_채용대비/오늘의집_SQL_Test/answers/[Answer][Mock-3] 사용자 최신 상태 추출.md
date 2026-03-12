# [Answer][Mock-3] 사용자 최신 상태 추출

## 정답 SQL

```sql
WITH ranked AS (
    SELECT user_id,
           status AS latest_status,
           updated_at,
           ROW_NUMBER() OVER (
               PARTITION BY user_id
               ORDER BY updated_at DESC, log_id DESC
           ) AS rn
    FROM user_status_logs
)
SELECT user_id,
       latest_status,
       updated_at
FROM ranked
WHERE rn = 1
ORDER BY user_id;
```

## 해설
- 사용자별로 최신 로그 1건만 남기면 됩니다.
- `ROW_NUMBER()`를 쓰고 정렬 기준을 `updated_at DESC, log_id DESC`로 명확히 둡니다.
