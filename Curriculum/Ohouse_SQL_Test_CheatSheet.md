# 오늘의집 SQL Test 치트시트

> 시험 직전에는 이 파일만 열고, 상세 확인이 필요하면 아래 세부 문서로 이동한다.

---

## 빠른 사용 순서

### 1. 문법이 헷갈릴 때
- `Curriculum/Ohouse_SQL_Syntax_Differences.md`

### 2. 정규표현식이 헷갈릴 때
- `Curriculum/Ohouse_SQL_Regex_CheatSheet.md`

### 3. 재풀이 우선순위를 보고 싶을 때
- `Curriculum/Ohouse_SQL_Test_Review_Queue.md`

### 4. 전체 준비 흐름을 보고 싶을 때
- `Curriculum/Ohouse_SQL_Test_Preparation_Plan.md`

---

## 시험 직전 10개만 확인

1. `LEFT JOIN`인데 오른쪽 조건을 `WHERE`에 두면 INNER처럼 변할 수 있다.
2. 중복 제거 요구가 없으면 `UNION ALL`을 먼저 생각한다.
3. 안티 조인은 `NOT EXISTS`를 우선 고려한다.
4. 주문 수 / 구매자 수 / 유저 수를 헷갈리지 않는다.
5. `COUNT(*)`, `COUNT(col)`, `COUNT(DISTINCT col)` 차이를 구분한다.
6. `ROW_NUMBER`, `RANK`, `DENSE_RANK` 동점 처리 차이를 외운다.
7. `DATEDIFF`는 일수, `TIMESTAMPDIFF`는 단위 지정이다.
8. 비율 계산은 `NULLIF(denominator, 0)`를 고려한다.
9. `REGEXP_LIKE(col, 'abc')`는 문자열 어디든 `abc`가 있으면 참이다.
10. 문자열 전체 일치가 필요하면 `^...$`를 붙인다.

---

## 오늘의집 포지션용 체크포인트

- JOIN 후 중복 집계가 생기지 않았는가?
- 비율 계산 분모가 0일 수 있지 않은가?
- 이 결과를 데이터 품질 검증용으로 바꾸면 어떤 sanity check를 붙일 수 있는가?
- 사용자/이벤트/주문 중 어떤 단위로 집계하는지 분명한가?

---

## 최근 재풀이 메모

- 그룹별 `MAX/MIN`만 구하면 상세 컬럼은 사라진다 → **원본 테이블에 다시 JOIN해서 대표 행을 복원**
- 계층/자기참조 문제는 먼저 **부모-자식 방향**과 **root/leaf 중 무엇을 찾는지** 적고 시작
- `CASE`는 **위에서 아래 first match** → 구간 조건은 **큰 범위부터**
- `BETWEEN`은 양끝 포함 → `7~30`, `30~90`처럼 쓰면 경계값이 겹칠 수 있음
- 할인 문제는 `할인액`이 아니라 **최종 결제 금액 식**을 먼저 세운다  
  `원금 × (1 - 할인율 / 100)`
- 할인 플랜이 없는 행도 포함해야 하면 `JOIN`이 아니라 **`LEFT JOIN`**
- 비율 문제에서 분모용 1행 집계는 **`CROSS JOIN`** 으로 붙이면 깔끔
- 집계 후 1위/상위권 추출은 **`GROUP BY → RANK()`** 를 먼저 떠올린다
- 동점 1위를 모두 포함해야 하면 `ROW_NUMBER()`보다 **`RANK()` / `DENSE_RANK()`** 가 더 맞을 수 있음
- 데이터가 없는 구간도 모두 출력해야 하면 **먼저 전체 축(숫자/날짜/시간)** 을 만들고 `LEFT JOIN + COALESCE`
- `WITH RECURSIVE`는 **시작값(anchor) + 다음값(recursive) + 종료조건** 3개로 읽는다
