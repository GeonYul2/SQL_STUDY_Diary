# 오늘의집 SQL Test 치트시트

> **시험장 메인 파일 1개만 연다면 이 파일**
>
> 원칙:
> - 먼저 이 파일
> - 보조 자료는 `Curriculum/Ohouse_SQL_Test_Support/` 폴더에 모아둠
> - 문법이 정말 헷갈릴 때만 syntax 파일
> - regex가 헷갈릴 때만 regex 파일

---

## 0. 시험장 오픈 순서

1. `Curriculum/Ohouse_SQL_Test_CheatSheet.md` ← 메인
2. `Curriculum/Ohouse_SQL_Test_Support/SQL_Mistake_Tracker.md` ← 최근 실수 확인용
3. `Curriculum/Ohouse_SQL_Test_Support/Ohouse_SQL_Syntax_Differences.md` ← 문법 차이 확인용
4. `Curriculum/Ohouse_SQL_Test_Support/Ohouse_SQL_Regex_CheatSheet.md` ← regex 확인용

즉, **실전에서는 사실상 이 파일 + 실수노트**만 먼저 보면 된다.

---

## 1. 문제 받자마자 10초 체크

1. **집계 단위(grain)**: user / event / order / session / day / month 중 무엇인가?
2. **무엇을 세는가**: 이벤트 수인가, 유저 수인가, 주문 수인가?
3. **중복 제거 필요 여부**: `COUNT(*)` vs `COUNT(DISTINCT user_id)`
4. **JOIN 후 row explosion** 위험은 없는가?
5. **비율 문제**면 `NULLIF(denominator, 0)` 필요한가?
6. **최신/처음/마지막** 문제면 tie-breaker가 있는가?
7. **없는 구간도 출력**해야 하면 전체 축 + `LEFT JOIN + COALESCE`
8. **출력 컬럼 / 정렬 조건**을 마지막에 다시 봤는가?

---

## 2. 오늘의집 스타일 핵심 패턴 8개

### 1) 이상 탐지
```sql
LAG(metric) OVER (ORDER BY dt)
```
- 전일/전월 비교
- 변화율: `(cur - prev) / NULLIF(prev, 0)`

### 2) 퍼널 전환율
```sql
COUNT(DISTINCT CASE WHEN event_type = 'x' THEN user_id END)
```
- 먼저 날짜-유저-이벤트 dedup
- 이벤트 수 말고 유저 수를 세는 문제인지 확인

### 3) 최신 상태 추출
```sql
ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY updated_at DESC, log_id DESC)
```
- `MAX(updated_at)`만 쓰면 상태 컬럼이 깨질 수 있음

### 4) 중복 로그 검출
```sql
GROUP BY key1, key2
HAVING COUNT(*) >= 2
```
- 중복의 정의를 먼저 문장으로 적기

### 5) 누락률 계산
```sql
SUM(CASE WHEN col IS NULL THEN 1 ELSE 0 END) / NULLIF(COUNT(*), 0)
```

### 6) 신규 / 현재 / 복귀 유저
```sql
MIN(activity_month) OVER (PARTITION BY user_id)
LAG(activity_month) OVER (PARTITION BY user_id ORDER BY activity_month)
```
- 먼저 월 단위 dedup

### 7) 후속 로그 누락 찾기
```sql
NOT EXISTS (... same user, same date, required event ...)
```
- 안티 조인은 `NOT EXISTS` 우선

### 8) 세션 첫/마지막 이벤트
```sql
ROW_NUMBER() OVER (PARTITION BY session_id ORDER BY event_time ASC, event_id ASC)
ROW_NUMBER() OVER (PARTITION BY session_id ORDER BY event_time DESC, event_id DESC)
```

---

## 3. 실전 템플릿

### A. 이상 탐지 / 급감·급증
```sql
WITH daily AS (
  SELECT DATE(event_time) AS dt, COUNT(*) AS cnt
  FROM t
  GROUP BY DATE(event_time)
), lagged AS (
  SELECT dt,
         cnt,
         LAG(cnt) OVER (ORDER BY dt) AS prev_cnt
  FROM daily
)
SELECT dt,
       cnt,
       prev_cnt,
       ROUND((cnt - prev_cnt) / NULLIF(prev_cnt, 0), 4) AS change_ratio
FROM lagged
WHERE prev_cnt IS NOT NULL;
```

### B. 퍼널 / 전환율
```sql
WITH base AS (
  SELECT DISTINCT DATE(event_time) AS event_date, user_id, event_type
  FROM app_events
)
SELECT event_date,
       COUNT(DISTINCT CASE WHEN event_type = 'view_item' THEN user_id END) AS view_users,
       COUNT(DISTINCT CASE WHEN event_type = 'add_to_cart' THEN user_id END) AS cart_users,
       ROUND(
         COUNT(DISTINCT CASE WHEN event_type = 'add_to_cart' THEN user_id END)
         / NULLIF(COUNT(DISTINCT CASE WHEN event_type = 'view_item' THEN user_id END), 0),
         4
       ) AS view_to_cart_rate
FROM base
GROUP BY event_date;
```

### C. 최신 상태 / 대표 행
```sql
ROW_NUMBER() OVER (
  PARTITION BY user_id
  ORDER BY updated_at DESC, log_id DESC
) AS rn
```

### D. 중복 검출
```sql
SELECT key1, key2, COUNT(*) AS dup_cnt
FROM t
WHERE status = 'SUCCESS'
GROUP BY key1, key2
HAVING COUNT(*) >= 2;
```

### E. 누락률
```sql
SELECT DATE(event_time) AS dt,
       COUNT(*) AS total_logs,
       SUM(CASE WHEN device_id IS NULL THEN 1 ELSE 0 END) AS missing_logs,
       ROUND(SUM(CASE WHEN device_id IS NULL THEN 1 ELSE 0 END) / NULLIF(COUNT(*), 0), 4) AS missing_rate
FROM t
GROUP BY DATE(event_time);
```

### F. 월별 신규 / 현재 / 복귀
```sql
WITH monthly AS (
  SELECT DISTINCT user_id, DATE_FORMAT(activity_time, '%Y-%m') AS activity_month
  FROM user_activity_logs
), base AS (
  SELECT user_id,
         activity_month,
         MIN(activity_month) OVER (PARTITION BY user_id) AS first_month,
         LAG(activity_month) OVER (PARTITION BY user_id ORDER BY activity_month) AS prev_month
  FROM monthly
)
SELECT activity_month,
       COUNT(CASE WHEN activity_month = first_month THEN 1 END) AS new_users
FROM base
GROUP BY activity_month;
```

### G. 무결성 누락 / 안티 조인
```sql
WHERE NOT EXISTS (
  SELECT 1
  FROM t2
  WHERE t2.user_id = t1.user_id
    AND DATE(t2.event_time) = DATE(t1.event_time)
    AND t2.event_type = 'payment_success'
)
```

### H. 세션 첫/마지막
```sql
ROW_NUMBER() OVER (PARTITION BY session_id ORDER BY event_time ASC, event_id ASC)  AS rn_first,
ROW_NUMBER() OVER (PARTITION BY session_id ORDER BY event_time DESC, event_id DESC) AS rn_last
```

---

## 4. 문법 실수 최소 세트

- `WHERE` = 집계 전 / `HAVING` = 집계 후
- `LEFT JOIN` 오른쪽 조건은 먼저 `ON`에 둘지 의심
- 중복 제거 요구 없으면 `UNION ALL`
- 안티 조인은 `NOT EXISTS` 우선
- `COUNT(*)` = 행 수
- `COUNT(col)` = NULL 제외
- `COUNT(DISTINCT col)` = 중복 제거
- `ROW_NUMBER` = 정확히 1건
- `RANK`, `DENSE_RANK` = 공동 순위 포함 가능
- `DATEDIFF` = 일수
- `TIMESTAMPDIFF` = 단위 지정
- 월 문자열 = `DATE_FORMAT(dt, '%Y-%m')`
- 날짜 문자열 = `DATE_FORMAT(dt, '%Y-%m-%d')`
- 0 나누기 방지 = `NULLIF(x, 0)`
- 없는 구간 출력 = 전체 축 + `LEFT JOIN + COALESCE`

---

## 5. 조건부 집계 빠른 구분

```sql
SUM(CASE WHEN condition THEN 1 ELSE 0 END)
```
- **조건을 만족한 행 수**
- 조건부 개수를 셀 때 가장 직관적

```sql
COUNT(CASE WHEN condition THEN 1 END)
```
- 이것도 **조건을 만족한 행 수**
- `COUNT`는 NULL이 아닌 값만 세므로 `ELSE`는 보통 쓰지 않음
- `COUNT(CASE WHEN ... THEN 1 ELSE 0 END)`는 0도 세어버릴 수 있어 위험

```sql
COUNT(DISTINCT CASE WHEN condition THEN user_id END)
```
- **조건을 만족한 고유 user 수**
- 퍼널처럼 이벤트 수가 아니라 유저 수를 세는 문제에서 사용

### 시험장 암기 규칙
- `THEN 1` + `SUM` → 조건 만족 **행 수**
- `THEN user_id` + `COUNT(DISTINCT ...)` → 조건 만족 **고유 유저 수**
- 단, `THEN user_id`라고 해서 항상 `COUNT`만 쓰는 게 아니라, **중복 제거가 필요하면 `DISTINCT`까지 같이 생각**해야 함

---

## 6. 정규표현식 최소 세트

```sql
REGEXP_LIKE(col, '^[0-9]+$')
REGEXP_LIKE(col, '^[가-힣]+$')
REGEXP_LIKE(code, '^[A-Z]{3}[0-9]{2}$')
REGEXP_LIKE(email, '^[^@]+@[^@]+\\.[^@]+$')
REGEXP_REPLACE(phone, '[^0-9]', '')
REGEXP_SUBSTR(order_note, '[0-9]+')
REGEXP_LIKE(keyword, '^abc$', 'i')
```

- 부분 매칭 기본 → 전체 일치면 `^...$`
- 단순 prefix/contains는 `LIKE`가 더 읽기 쉬울 수 있음

---

## 7. 영어 신호어 매핑

- for each = `GROUP BY`
- latest / most recent = `ROW_NUMBER`, `MAX`
- duplicate = `GROUP BY + HAVING`
- missing = `IS NULL`
- conversion rate = 분자/분모 분리
- previous = `LAG()`
- no / without = `NOT EXISTS`, `LEFT JOIN ... IS NULL`
- at least = `>=`
- same date = `DATE(col)` 기준 통일

---

## 8. 최근 실수 기반 재발 방지

- `LIKE '서울%'` vs `LIKE '%서울%'` 의미를 섞지 않기
- `NULL AS user_id` 같은 출력 컬럼 맞추기 놓치지 않기
- 계층 문제는 root/leaf 방향을 먼저 적기
- 할인/구간 문제는 최종 식과 조건 순서를 먼저 세우기
- 그룹 대표값 뒤 상세 컬럼이 필요하면 원본 재조인
- 퍼널은 `COUNT(*)`가 아니라 `COUNT(DISTINCT user_id)`인지 의심하기
- 최신/첫/마지막 문제는 tie-breaker를 꼭 확인하기
- 월 분류 문제는 dedup 없이 바로 `LAG()` 걸지 않기
- 데이터가 없는 구간까지 출력이면 전체 축 생성

---

## 9. 마지막 1분 점검

- output column 맞는가?
- order by 맞는가?
- grain 맞는가?
- dedup 필요한가?
- `NULLIF` 빠지지 않았는가?
- tie-breaker 필요한가?
- LEFT JOIN이 INNER로 변하지 않았는가?
- 이 결과를 QA 관점으로 한 줄 설명할 수 있는가?
