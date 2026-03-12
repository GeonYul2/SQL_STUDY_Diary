# 오늘의집 SQL Test - 문법 차이 정리

> 기준: **MySQL 8.0**  
> 목적: 비슷해 보이지만 결과가 달라지는 문법 차이를 빠르게 확인

---

## 1. WHERE vs HAVING

| 구분 | WHERE | HAVING |
|---|---|---|
| 적용 시점 | GROUP BY 이전 | GROUP BY 이후 |
| 주 용도 | 행 필터링 | 집계 결과 필터링 |
| 집계 함수 사용 | 보통 불가 | 가능 |

```sql
SELECT category, COUNT(*)
FROM products
WHERE created_at >= '2025-01-01'
GROUP BY category;

SELECT category, COUNT(*) AS cnt
FROM products
GROUP BY category
HAVING COUNT(*) >= 10;
```

핵심:
- 원본 row 조건 → `WHERE`
- 집계 결과 조건 → `HAVING`

---

## 2. ON vs WHERE in LEFT JOIN

```sql
-- LEFT JOIN 의미 유지
SELECT *
FROM users u
LEFT JOIN orders o
  ON u.user_id = o.user_id
 AND o.status = 'paid';
```

```sql
-- NULL 행이 제거되어 INNER처럼 변할 수 있음
SELECT *
FROM users u
LEFT JOIN orders o
  ON u.user_id = o.user_id
WHERE o.status = 'paid';
```

핵심:
- 매칭 기준 → `ON`
- 최종 필터 → `WHERE`
- 오른쪽 테이블 조건을 `WHERE`에 두면 LEFT JOIN 의미가 깨질 수 있음

---

## 3. UNION vs UNION ALL

| 구분 | UNION | UNION ALL |
|---|---|---|
| 중복 제거 | O | X |
| 기본 선택 | 중복 제거가 꼭 필요할 때 | 보통 먼저 고려 |

```sql
SELECT user_id FROM offline_sales
UNION ALL
SELECT user_id FROM online_sales;
```

핵심:
- 중복 제거 요구 없으면 `UNION ALL`

---

## 4. IN vs EXISTS

| 구분 | IN | EXISTS |
|---|---|---|
| 감각 | 값 목록 비교 | 행 존재 여부 |
| 자주 맞는 경우 | 단순 목록 포함 여부 | 상관 서브쿼리, 존재 확인 |

```sql
WHERE user_id IN (
  SELECT user_id
  FROM orders
)

WHERE EXISTS (
  SELECT 1
  FROM orders o
  WHERE o.user_id = u.user_id
)
```

---

## 5. NOT IN vs NOT EXISTS vs LEFT JOIN ... IS NULL

### `NOT IN`

```sql
WHERE user_id NOT IN (SELECT user_id FROM banned_users)
```

주의:
- 서브쿼리에 `NULL`이 섞이면 위험

### `NOT EXISTS`

```sql
WHERE NOT EXISTS (
  SELECT 1
  FROM banned_users b
  WHERE b.user_id = u.user_id
)
```

장점:
- NULL 이슈에 더 안전
- 안티 조인 의도가 명확

### `LEFT JOIN ... IS NULL`

```sql
FROM users u
LEFT JOIN banned_users b
  ON u.user_id = b.user_id
WHERE b.user_id IS NULL
```

장점:
- 시각적으로 이해 쉬움

실전 권장:
- 보통 `NOT EXISTS` 우선 고려

---

## 6. COUNT(*) vs COUNT(col) vs COUNT(DISTINCT col)

| 표현 | 의미 |
|---|---|
| `COUNT(*)` | 전체 행 수 |
| `COUNT(col)` | NULL 아닌 값 수 |
| `COUNT(DISTINCT col)` | 중복 제거 후 값 수 |

실수 포인트:
- 주문 건수 vs 주문자 수 vs 사용자 수 구분

---

## 7. CASE + COUNT vs CASE + SUM

```sql
COUNT(CASE WHEN status = 'paid' THEN 1 END)
SUM(CASE WHEN status = 'paid' THEN 1 ELSE 0 END)
```

핵심:
- 둘 다 조건부 개수 계산 가능
- `SUM(CASE WHEN ... THEN 1 ELSE 0 END)`가 더 직관적일 때가 많음

주의:
- `COUNT(CASE WHEN ... THEN 1 ELSE 0 END)`는 ELSE 0 때문에 전부 세질 수 있음

---

## 8. ROW_NUMBER vs RANK vs DENSE_RANK

예: 점수 `100, 90, 90, 80`

- `ROW_NUMBER` → 1,2,3,4
- `RANK` → 1,2,2,4
- `DENSE_RANK` → 1,2,2,3

실전:
- 정확히 N개 → `ROW_NUMBER()`
- 공동 순위 허용 → `RANK()` / `DENSE_RANK()`
- TOP N with ties → `DENSE_RANK()` 자주 사용

---

## 9. DATEDIFF vs TIMESTAMPDIFF

| 함수 | 의미 |
|---|---|
| `DATEDIFF(a, b)` | `a - b`의 일수 차이 |
| `TIMESTAMPDIFF(unit, a, b)` | `b - a`를 단위 기준으로 계산 |

```sql
DATEDIFF(end_date, start_date)
TIMESTAMPDIFF(DAY, start_date, end_date)
TIMESTAMPDIFF(MONTH, signup_date, order_date)
```

핵심:
- `DATEDIFF`는 일수만
- 월 차이/나이/코호트는 보통 `TIMESTAMPDIFF`

---

## 10. DATE_FORMAT vs YEAR/MONTH

| 상황 | 추천 |
|---|---|
| `'2025-03'` 같은 문자열 필요 | `DATE_FORMAT(col, '%Y-%m')` |
| 연/월 숫자 분리 집계 | `YEAR(col), MONTH(col)` |

---

## 11. 오늘의집/QA 관점으로 같이 볼 것

- JOIN 후 row explosion이 없는가?
- 비율 계산 시 `COUNT(DISTINCT user_id)`가 필요한가?
- 분모 0을 `NULLIF`로 방지해야 하는가?
- 이 결과값에 sanity check를 붙이면 무엇인가?

```sql
numerator / NULLIF(denominator, 0)
```

---

## 빠른 암기

- WHERE = 집계 전 / HAVING = 집계 후
- LEFT JOIN 오른쪽 조건은 먼저 ON에 둘지 의심
- 중복 제거 없으면 UNION ALL
- 안티 조인은 NOT EXISTS 우선
- 구매자 수는 COUNT(DISTINCT user_id)인지 먼저 체크
