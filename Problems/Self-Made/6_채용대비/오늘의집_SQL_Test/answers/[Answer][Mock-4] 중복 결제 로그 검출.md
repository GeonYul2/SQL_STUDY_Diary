# [Answer][Mock-4] 중복 결제 로그 검출

## 정답 SQL

```sql
SELECT order_id,
       paid_amount,
       COUNT(*) AS duplicate_count
FROM payment_logs
WHERE payment_status = 'SUCCESS'
GROUP BY order_id, paid_amount
HAVING COUNT(*) >= 2
ORDER BY order_id, paid_amount;
```

## 해설
- 중복의 정의가 `order_id + paid_amount + SUCCESS` 조합이므로 해당 기준으로 그룹핑합니다.
- 그룹 건수가 2 이상인 것만 남기면 됩니다.
