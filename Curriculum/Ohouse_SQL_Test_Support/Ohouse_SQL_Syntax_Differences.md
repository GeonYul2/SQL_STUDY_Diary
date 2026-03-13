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

핵심:
- 원본 row 조건 → `WHERE`
- 집계 결과 조건 → `HAVING`

---

## 2. ON vs WHERE in LEFT JOIN

```sql
SELECT *
FROM users u
LEFT JOIN orders o
  ON u.user_id = o.user_id
 AND o.status = 'paid';
```

```sql
SELECT *
FROM users u
LEFT JOIN orders o
  ON u.user_id = o.user_id
WHERE o.status = 'paid';
```

핵심:
- 오른쪽 테이블 조건을 `WHERE`에 두면 LEFT JOIN 의미가 깨질 수 있음

---

## 3. UNION vs UNION ALL

- 중복 제거 요구가 없으면 `UNION ALL` 우선

---

## 4. IN vs EXISTS

- 단순 목록 비교 → `IN`
- 존재 여부 / 상관 서브쿼리 → `EXISTS`

---

## 5. NOT IN vs NOT EXISTS vs LEFT JOIN ... IS NULL

- 안티 조인은 보통 `NOT EXISTS` 우선
- `NOT IN`은 서브쿼리에 `NULL`이 섞이면 위험

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

## 7. CASE + COUNT vs CASE + SUM vs CASE + COUNT(DISTINCT)

```sql
SUM(CASE WHEN condition THEN 1 ELSE 0 END)
```
- 조건을 만족한 **행 수**
- 가장 직관적인 조건부 개수 집계

```sql
COUNT(CASE WHEN condition THEN 1 END)
```
- 이것도 조건을 만족한 **행 수**
- `COUNT`는 NULL이 아닌 값만 세므로 `ELSE`를 보통 쓰지 않음

```sql
COUNT(DISTINCT CASE WHEN condition THEN user_id END)
```
- 조건을 만족한 **고유 user 수**
- 퍼널/구매자 수/활성 유저 수 문제에서 자주 사용

주의:
- `COUNT(CASE WHEN condition THEN 1 ELSE 0 END)`는 0도 NULL이 아니므로 거의 모든 행이 세어질 수 있음
- `THEN user_id`라고 해서 무조건 `COUNT`로 끝내는 게 아니라, **중복 제거가 필요하면 `DISTINCT`까지 포함**해서 생각해야 함

빠른 암기:
- `THEN 1` + `SUM` → 조건 만족 **행 수**
- `THEN user_id` + `COUNT(DISTINCT ...)` → 조건 만족 **고유 유저 수**

---

## 8. ROW_NUMBER vs RANK vs DENSE_RANK

예: 점수 `100, 90, 90, 80`

- `ROW_NUMBER` → 1,2,3,4
- `RANK` → 1,2,2,4
- `DENSE_RANK` → 1,2,2,3

실전:
- 정확히 1건 → `ROW_NUMBER()`
- 공동 순위 허용 → `RANK()` / `DENSE_RANK()`

---

## 9. DATEDIFF vs TIMESTAMPDIFF

| 함수 | 의미 |
|---|---|
| `DATEDIFF(a, b)` | `a - b`의 일수 차이 |
| `TIMESTAMPDIFF(unit, a, b)` | `b - a`를 단위 기준으로 계산 |

핵심:
- `DATEDIFF`는 일수만
- 월 차이/나이/코호트는 보통 `TIMESTAMPDIFF`

---

## 10. DATE_FORMAT vs YEAR/MONTH

- `'2025-03'` 같은 문자열이 필요하면 `DATE_FORMAT(col, '%Y-%m')`
- 연/월을 따로 숫자로 다루면 `YEAR(col), MONTH(col)`

---

## 11. 오늘의집/QA 관점으로 같이 볼 것

- JOIN 후 row explosion이 없는가?
- 비율 계산 시 `COUNT(DISTINCT user_id)`가 필요한가?
- 분모 0을 `NULLIF`로 방지해야 하는가?
- 이 결과값에 sanity check를 붙이면 무엇인가?

```sql
numerator / NULLIF(denominator, 0)
```
