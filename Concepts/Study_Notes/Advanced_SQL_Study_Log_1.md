# 🚀 고급 SQL 스터디 로그 (Advanced SQL)

> **프로젝트**: [백문이불여일타] 데이터 분석을 위한 고급 SQL
> **기간**: 2025.08.08 (마감)
> **상태**: 완료 ✅

---

## 📚 1. Advanced Concepts

### 1.1 부서별 최고 연봉자 찾기 (Correlated Subquery)
`GROUP BY`의 한계를 극복하고, 동점자까지 모두 출력하기 위한 서브쿼리 활용법.

**잘못된 접근 (HAVING)**
```sql
-- 부서별로 한 명만 남기 때문에 동률의 경우 누군지 알 수 없게 됨
SELECT D.name, E.name, E.Salary ...
GROUP BY departmentID
HAVING ...
```

**올바른 접근 (WHERE IN Subquery)**
```sql
SELECT D.name AS Department, E.name AS Employee, E.Salary
FROM Employee E
JOIN Department D ON E.departmentId = D.id
WHERE (E.departmentId, E.Salary) IN (
    SELECT departmentId, MAX(Salary)
    FROM Employee
    GROUP BY departmentId
);
```
> **로직**: "각 부서의 최고 연봉"을 먼저 구하고 (`departmentId`, `MAX(Salary)`), 그 조합과 일치하는 사원을 찾는다.

---

## 🧩 2. HackerRank Challenges

### 2.1 Challenges (복잡한 집계 로직)
**문제**: 해커들이 만든 챌린지 수를 비교하여 정렬. (최댓값은 중복 허용, 그 외 중복은 제거)

```sql
WITH Counter AS (
    SELECT h.hacker_id, h.name, COUNT(*) AS cnt
    FROM Hackers h
    JOIN Challenges c ON h.hacker_id = c.hacker_id
    GROUP BY h.hacker_id, h.name
),
MaxCounter AS (
    SELECT MAX(cnt) as max_cnt FROM Counter
),
CntCounts AS (
    SELECT cnt, COUNT(*) as duplicate_cnt
    FROM Counter
    GROUP BY cnt
)
SELECT c.hacker_id, c.name, c.cnt
FROM Counter c
JOIN MaxCounter m ON 1=1
JOIN CntCounts cc ON c.cnt = cc.cnt
WHERE c.cnt = m.max_cnt      -- 최댓값이거나
   OR cc.duplicate_cnt = 1   -- 중복이 없는 경우만
ORDER BY c.cnt DESC, c.hacker_id;
```

### 2.2 New Companies (계층형 데이터 카운트)
**핵심**: `DISTINCT`를 사용하여 중복 제거 카운트.

```sql
SELECT c.company_code, c.founder,
    COUNT(DISTINCT lm.lead_manager_code),
    COUNT(DISTINCT sm.senior_manager_code),
    COUNT(DISTINCT m.manager_code),
    COUNT(DISTINCT e.employee_code)
FROM Company c
JOIN Lead_Manager lm ON c.company_code = lm.company_code
JOIN Senior_Manager sm ON lm.lead_manager_code = sm.lead_manager_code
JOIN Manager m ON sm.senior_manager_code = m.senior_manager_code
JOIN Employee e ON m.manager_code = e.manager_code
GROUP BY c.company_code, c.founder
ORDER BY c.company_code;
```

### 2.3 Occupations (Pivot Table)
ROW를 COLUMN으로 변환하기 (`CASE WHEN` + `GROUP BY`).

```sql
SELECT
    MIN(CASE WHEN Occupation = 'Doctor' THEN Name END) AS Doctor,
    MIN(CASE WHEN Occupation = 'Professor' THEN Name END) AS Professor,
    MIN(CASE WHEN Occupation = 'Singer' THEN Name END) AS Singer,
    MIN(CASE WHEN Occupation = 'Actor' THEN Name END) AS Actor
FROM (
    SELECT Occupation, Name,
           ROW_NUMBER() OVER (PARTITION BY Occupation ORDER BY Name) as rn
    FROM Occupations
) t
GROUP BY rn
ORDER BY rn;
```
> **Why MIN?**: `GROUP BY rn`을 했을 때, 나머지 `CASE WHEN`의 `NULL` 값들을 제거하고 하나의 값만 남기기 위해 사용 (MAX도 가능).

---

## 💻 3. LeetCode Examples

### 3.1 Consecutive Numbers (연속된 수)
`SELF JOIN`을 통해 `id`, `id+1`, `id+2`를 연결하여 비교.

```sql
SELECT DISTINCT l1.num AS ConsecutiveNums
FROM Logs l1
INNER JOIN Logs l2 ON l1.id + 1 = l2.id
INNER JOIN Logs l3 ON l2.id + 1 = l3.id
WHERE l1.num = l2.num AND l2.num = l3.num;
```

### 3.2 Nth Highest Salary (User Defined Function)
**사용자 정의 함수**와 `LIMIT OFFSET` 활용.

```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  SET N = N - 1; -- LIMIT의 OFFSET은 0부터 시작하므로
  RETURN (
      SELECT DISTINCT Salary
      FROM Employee
      ORDER BY Salary DESC
      LIMIT 1 OFFSET N
  );
END
```

---

## 🪟 4. Window Functions (심화)

> **차이점**:
> - `GROUP BY`: 행을 압축하여 결과 행 수가 줄어듦.
> - `WINDOW`: 원본 행을 유지하면서 계산된 열만 추가함.

### 순위 함수 3대장
1. **`ROW_NUMBER()`**: 1, 2, 3, 4 (동점 무시, 고유 번호)
2. **`RANK()`**: 1, 1, 3, 4 (동점 시 순위 건너뜀)
3. **`DENSE_RANK()`**: 1, 1, 2, 3 (동점 시 순위 유지)

### 누적합 (Cumulative Sum)
```sql
SELECT id, Month, Salary,
       SUM(Salary) OVER (ORDER BY Month) AS Cumulative_Salary
FROM Employee;
```

### 이전 행/다음 행 (LAG, LEAD)
```sql
SELECT id,
       LAG(Salary, 1, 0) OVER (ORDER BY id) AS Prev_Salary,
       LEAD(Salary, 1, 0) OVER (ORDER BY id) AS Next_Salary
FROM Employee;
```

---

## 🔗 유용한 링크
- [RegexOne (정규표현식 튜토리얼)](https://regexone.com/)
- [MySQL Stored Function Guide](https://www.mysqltutorial.org/mysql-stored-function/)