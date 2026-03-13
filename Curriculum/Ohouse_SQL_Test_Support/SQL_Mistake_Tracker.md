# SQL Mistake Tracker

> 목적: 문제를 풀면서 헷갈렸던 문법, 실수 포인트, 재발 방지 포인트를 누적 기록  
> 사용법: 문제를 풀 때마다 문제명/카테고리를 먼저 남기고, 틀리거나 헷갈린 포인트가 있으면 아래 템플릿으로 추가

---

## 기록 템플릿

### [날짜] 플랫폼 / 문제명
- 카테고리:
- 문제 유형:
- 내가 헷갈린 것:
- 내가 쓴 오답/헷갈린 포인트:
- 정답/핵심 포인트:
- 재발 방지 한 줄:
- 연결 치트시트:

---

## [2026-03-11] 프로그래머스 / 서울에 위치한 식당 목록 출력하기

- 카테고리: SELECT
- 문제 유형: SELECT, JOIN, GROUP BY, LIKE, 정렬
- 내가 헷갈린 것: `LIKE`에서 `%`와 `_` 의미, prefix 매칭과 contains 매칭 차이
- 내가 쓴 오답/헷갈린 포인트:
  - `WHERE ADDRESS LIKE "서울%"`
  - 문제를 다시 풀면서 `%`와 `_` 문법을 순간적으로 헷갈림
  - 회고상 `%서울%` 와 `서울%` 차이를 다시 점검할 필요가 있었음
- 정답/핵심 포인트:
  - `%` : 0개 이상의 임의 문자열
  - `_` : 정확히 1개의 임의 문자
  - `LIKE '서울%'` : `서울`로 시작하는 값
  - `LIKE '%서울%'` : 문자열 어디에든 `서울`이 포함된 값
  - 이 문제는 주소가 `서울`로 시작하는 데이터 조건을 확인하는 문제라면 `서울%`와 `%서울%`의 의미 차이를 명확히 알아야 함
- 재발 방지 한 줄:
  - `prefix 검색 = '문자%'`, contains 검색 = `'%문자%'`, 한 글자 와일드카드 = `'_'`
- 연결 치트시트:
  - `Curriculum/Ohouse_SQL_Test_Support/Ohouse_SQL_Syntax_Differences.md`
  - `Curriculum/Ohouse_SQL_Test_CheatSheet.md`
- 필요 시 `LIKE vs REGEXP_LIKE`는 `Curriculum/Ohouse_SQL_Test_Support/Ohouse_SQL_Regex_CheatSheet.md`

---

## [2026-03-11] 프로그래머스 / 오프라인-온라인 판매 데이터 통합하기

- 카테고리: SELECT
- 문제 유형: SELECT, UNION ALL, DATE_FORMAT, NULL 처리, 결과 포맷
- 내가 헷갈린 것:
  - `NULL AS user_id`
  - `DATE_FORMAT` 포맷 문자열 (`%Y-%m`, `%Y-%m-%d`)
  - 결과 출력 형식 맞추기
  - `UNION` 과 `UNION ALL` 차이
- 내가 쓴 오답/헷갈린 포인트:
  - 오프라인 데이터에 `NULL AS USER_ID`를 붙여야 하는 포인트를 다시 떠올려야 했음
  - 날짜 포맷을 `yyyy-mm`처럼 감으로 쓰려다가 `%Y-%m` 형식을 다시 확인해야 했음
  - 출력 형식을 문제 요구대로 `'%Y-%m-%d'`로 맞춰야 하는 점을 놓치기 쉬움
  - `UNION`을 썼는데, 이 문제는 보통 **`UNION ALL`** 이 더 안전함
- 정답/핵심 포인트:
  - 오프라인 판매 테이블엔 `USER_ID`가 없으므로 `NULL AS USER_ID`
  - 월 필터링: `DATE_FORMAT(sales_date, '%Y-%m') = '2022-03'`
  - 출력 포맷: `DATE_FORMAT(sales_date, '%Y-%m-%d')`
  - 중복 제거 요구가 없으므로 `UNION`보다 `UNION ALL` 우선 고려
- 재발 방지 한 줄:
  - 컬럼 개수 맞출 때 없는 컬럼은 `NULL AS col_name`, 날짜 포맷은 `%Y/%m/%d`가 아니라 `%Y-%m-%d`, 합치기는 중복 제거 없으면 `UNION ALL`
- 연결 치트시트:
  - `Curriculum/Ohouse_SQL_Test_Support/Ohouse_SQL_Syntax_Differences.md`
  - `Curriculum/Ohouse_SQL_Test_CheatSheet.md`

---

## [2026-03-12] 프로그래머스 / 업그레이드 할 수 없는 아이템 구하기

- 카테고리: IS NULL
- 문제 유형: IS NULL, LEFT JOIN, 계층 관계 해석
- 내가 헷갈린 것:
  - 문제를 급하게 읽으면 `업그레이드 할 수 없는 아이템`이 무엇을 뜻하는지 헷갈림
  - 부모/자식 방향을 먼저 정리하지 않으면 조건 방향이 뒤집히기 쉬움
  - self join/계층 관점으로 보면 쉬운데 처음엔 바로 안 떠오를 수 있음
- 내가 쓴 오답/헷갈린 포인트:
  - 문제를 똑바로 읽지 않으면 `ROOT`를 찾는지 `leaf`를 찾는지 혼동할 수 있음
  - `ITEM_ID = PARENT_ITEM_ID` 관계를 머릿속에 먼저 그리지 않으면 어떤 아이템을 제외해야 하는지 헷갈림
- 정답/핵심 포인트:
  - 찾는 대상은 **더 이상 업그레이드할 수 없는 말단(leaf) 아이템**
  - 어떤 아이템의 `ITEM_ID`가 다른 행의 `PARENT_ITEM_ID`로 등장하면 그 아이템은 아직 업그레이드 가능
  - 따라서 `ITEM_INFO.ITEM_ID = ITEM_TREE.PARENT_ITEM_ID`로 `LEFT JOIN` 후 `IT.PARENT_ITEM_ID IS NULL`인 행만 남기면 됨
  - 또는 `NOT EXISTS`로도 같은 의미를 표현할 수 있음
- 재발 방지 한 줄:
  - 계층 문제는 먼저 **부모/자식 방향**과 **내가 찾는 게 root인지 leaf인지**를 한 줄로 적고 시작한다
- 연결 치트시트:
  - `Curriculum/Ohouse_SQL_Test_CheatSheet.md`

---

## [2026-03-12] 프로그래머스 / 자동차 대여 기록 별 대여 금액 구하기

- 카테고리: String, Date
- 문제 유형: String, Date, CASE, DATEDIFF, LEFT JOIN, 할인 계산
- 내가 헷갈린 것:
  - 문제의 목표가 `할인액` 계산이 아니라 `최종 대여 금액` 계산이라는 점
  - 기간 구간 분기를 쓸 때 조건 순서와 범위 겹침 처리
  - 할인 플랜이 없는 대여 기록도 결과에 포함해야 한다는 점
- 내가 쓴 오답/헷갈린 포인트:
  - 원금에서 할인액을 따로 빼는 흐름으로 생각하다가 계산식이 꼬일 수 있었음
  - `> 7`, `> 30`처럼 작은 구간부터 쓰면 큰 기간도 첫 조건에 먼저 걸림
  - `BETWEEN 7 AND 30`, `BETWEEN 30 AND 90`는 `30`, `90`에서 범위가 겹칠 수 있음
  - 할인 플랜 테이블을 기본 `JOIN`하면 7일 미만처럼 플랜이 없는 대여가 누락될 수 있음
- 정답/핵심 포인트:
  - 최종 금액은 `일일 요금 × 대여 일수 × (1 - 할인율 / 100)`
  - `CASE`는 위에서 아래로 평가하며 **처음 만족한 조건에서 종료**
  - 구간 조건은 큰 범위부터 쓰거나, 범위가 겹치지 않게 명시해야 함
  - 할인 플랜이 없는 경우까지 포함하려면 `LEFT JOIN + COALESCE(할인율, 0)` 처리
- 재발 방지 한 줄:
  - 구간 문제는 **범위를 먼저 적고 CASE는 큰 구간부터**, 할인 문제는 **최종금액 식을 먼저 세운 뒤 구현**한다
- 연결 치트시트:
  - `Curriculum/Ohouse_SQL_Test_CheatSheet.md`
  - `Curriculum/Ohouse_SQL_Test_Support/Ohouse_SQL_Syntax_Differences.md`

---

## [2026-03-12] 프로그래머스 / 물고기 종류 별 대어 찾기

- 카테고리: SUM,MAX,MIN
- 문제 유형: GROUP BY, MAX, JOIN
- 내가 헷갈린 것:
  - 특별히 큰 헷갈림은 없었음
  - 다만 `그룹별 MAX`만 구하면 상세 행을 바로 못 가져온다는 패턴은 다시 확인할 가치가 있음
- 내가 쓴 오답/헷갈린 포인트:
  - 이번 재풀이에서는 큰 오답은 없었음
  - 핵심은 그룹별 최대값 집계 후 원본에 다시 JOIN해서 상세 컬럼을 복원하는 흐름
- 정답/핵심 포인트:
  - `GROUP BY + MAX`로 물고기 종류별 최대 길이를 구함
  - 그 결과를 원본과 다시 JOIN해서 `ID`, `FISH_NAME`, `LENGTH`를 가져옴
  - JOIN 시에는 **그룹 키 + 최대값**을 함께 매칭해야 안전함
- 재발 방지 한 줄:
  - 그룹 대표값만 구한 뒤 상세 컬럼이 필요하면 **원본 재조인** 패턴을 떠올린다
- 연결 치트시트:
  - `Curriculum/Ohouse_SQL_Test_CheatSheet.md`

---

## [2026-03-12] 프로그래머스 / 취소되지 않은 진료 예약 조회하기

- 카테고리: JOIN
- 문제 유형: 다중 JOIN, 날짜 필터, 상태 조건 필터
- 내가 헷갈린 것:
  - 특별한 헷갈림은 없었음
- 내가 쓴 오답/헷갈린 포인트:
  - 이번 재풀이에서는 큰 오답은 없었음
  - `APPOINTMENT`, `PATIENT`, `DOCTOR` 3개 테이블을 정확히 연결하고 날짜/취소 여부/진료과 조건만 걸어주면 되는 문제
- 정답/핵심 포인트:
  - 기본 3테이블 JOIN 구조를 먼저 세우고
  - `MCDP_CD = 'CS'`, `APNT_CNCL_YN = 'N'`, 날짜 조건을 함께 필터링
  - `APNT_YMD` 기준 오름차순 정렬
- 재발 방지 한 줄:
  - 다중 JOIN 문제는 **조인 관계를 먼저 고정하고 조건을 뒤에 붙인다**
- 연결 치트시트:
  - `Curriculum/Ohouse_SQL_Test_CheatSheet.md`

---

## [2026-03-12] 프로그래머스 / 상품을 구매한 회원 비율 구하기

- 카테고리: JOIN
- 문제 유형: JOIN, 비율 계산, DISTINCT 집계, CROSS JOIN
- 내가 헷갈린 것:
  - 이번 재풀이에서는 크게 막히지 않았고 `CROSS JOIN` 포인트를 다시 확인함
- 내가 쓴 오답/헷갈린 포인트:
  - 큰 오답은 없었음
  - 분모가 되는 전체 회원 수 1행 집계를 메인 집계 결과에 붙이는 방법을 다시 복습함
- 정답/핵심 포인트:
  - 2021년 가입자 집합을 먼저 제한
  - 월별 구매 회원 수는 `COUNT(DISTINCT USER_ID)`
  - 전체 회원 수 같은 **1행 분모 집계**는 `CROSS JOIN`으로 붙이면 계산이 깔끔함
- 재발 방지 한 줄:
  - 비율 문제에서는 **분자 집계 / 분모 1행 집계 / 결합 방식(CROSS JOIN)** 을 먼저 분리해서 본다
- 연결 치트시트:
  - `Curriculum/Ohouse_SQL_Test_CheatSheet.md`

---

## [2026-03-12] 프로그래머스 / 그룹별 조건에 맞는 식당 목록 출력하기

- 카테고리: JOIN
- 문제 유형: JOIN, GROUP BY, 윈도우 함수, RANK
- 내가 헷갈린 것:
  - `RANK()` 계열 윈도우 함수를 잠깐 까먹었음
  - 회원별 리뷰 수 집계 후 순위를 매기고, 1위 회원의 리뷰만 다시 가져오는 흐름을 다시 복습할 필요가 있었음
- 내가 쓴 오답/헷갈린 포인트:
  - 이번 재풀이에서는 해결했지만 핵심 함수가 바로 안 떠오름
  - 문제를 풀 때 `COUNT(*)` 기반 집계 → `RANK()` → `rnk = 1` 대상과 원본 JOIN 흐름으로 정리하면 쉬워짐
- 정답/핵심 포인트:
  - 먼저 회원별 리뷰 수를 집계하고 `RANK() OVER (ORDER BY COUNT(*) DESC)`로 순위를 매김
  - `RANK = 1`인 회원만 추려서 기본 리뷰 데이터 및 프로필과 JOIN
  - 동점 1위가 여러 명이면 `RANK()`가 모두 포함해준다는 점이 중요
- 재발 방지 한 줄:
  - **집계 후 상위 N명/1위 추출 문제는 `GROUP BY → RANK()` 패턴**을 먼저 떠올린다
- 연결 치트시트:
  - `Curriculum/Ohouse_SQL_Test_CheatSheet.md`

---

## [2026-03-12] 프로그래머스 / 입양 시각 구하기(2)

- 카테고리: GROUP BY
- 문제 유형: 재귀 CTE, GROUP BY, LEFT JOIN, COALESCE
- 내가 헷갈린 것:
  - `WITH RECURSIVE`로 0~23 숫자 테이블을 만드는 흐름이 가장 헷갈렸음
  - `COALESCE` 자체는 괜찮았고, 핵심 헷갈림은 재귀 CTE였음
- 내가 쓴 오답/헷갈린 포인트:
  - 데이터가 없는 시간대도 0으로 채워야 하므로 먼저 전체 시간축(0~23)을 만들어야 하는데, 이 발상이 바로 안 떠오를 수 있음
  - 집계만 하면 데이터가 있는 시간만 나오므로 문제 요구를 놓치기 쉬움
- 정답/핵심 포인트:
  - `WITH RECURSIVE`로 0부터 23까지의 시간 테이블 생성
  - 실제 데이터는 `HOUR(DATETIME)` 기준으로 집계
  - 생성한 시간 테이블과 집계 결과를 `LEFT JOIN`
  - 없는 값은 `COALESCE(count_col, 0)`으로 0 처리
- 재발 방지 한 줄:
  - **없는 구간까지 모두 보여줘야 하는 문제는 먼저 전체 축(숫자/날짜/시간)을 만들고 LEFT JOIN한다**
- 연결 치트시트:
  - `Curriculum/Ohouse_SQL_Test_CheatSheet.md`

---

## 자주 적을 만한 실수 유형

- `LEFT JOIN`인데 오른쪽 조건을 `WHERE`에 둬서 INNER처럼 바뀜
- `COUNT(*)`와 `COUNT(DISTINCT user_id)`를 혼동
- `UNION` / `UNION ALL` 혼동
- `NOT IN` / `NOT EXISTS` 혼동
- `RANK()` / `DENSE_RANK()` / `ROW_NUMBER()` 혼동
- `DATEDIFF()` / `TIMESTAMPDIFF()` 혼동
- `LIKE '%x%'` / `LIKE 'x%'` / `LIKE '_x%'` 혼동

---

## 운영 원칙

- 틀린 문제만 쓰지 말고, **맞았지만 헷갈린 문제도 기록**
- 헷갈림이 거의 없던 문제도 **문제명 + 카테고리 + 한 줄 메모**는 남겨서 풀이 로그를 유지
- “왜 헷갈렸는지”를 한 줄로 남길 것
- 반드시 관련 치트시트 파일을 링크할 것
- 시험 직전에는 이 파일만 훑어도 되게 만들 것

---

## [2026-03-13] 오늘의집 Self-Made Mock 1~8 + Regex Drill 종합 정리

- 카테고리: Data Quality / Funnel / Window Function / Regex / Retention
- 문제 유형:
  - anomaly detection
  - funnel conversion
  - latest state
  - duplicate detection
  - missing rate
  - monthly user classification
  - integrity check
  - session first/last event
  - regex matching / replace / substr
- 내가 헷갈리기 쉬운 것:
  - 이벤트 수와 유저 수를 섞어 세는 것
  - 최신/첫/마지막 대표 행 문제에서 tie-breaker를 빼먹는 것
  - 월 단위 분류 문제에서 dedup 없이 바로 `LAG()`를 거는 것
  - 안티 조인 문제에서 `NOT EXISTS` 대신 위험한 `NOT IN`을 감으로 쓰는 것
  - regex에서 부분 매칭과 전체 매칭을 혼동하는 것
- 내가 쓴 오답/헷갈린 포인트:
  - 퍼널 문제에서 `COUNT(*)`로 세면 이벤트 중복 때문에 과대집계될 수 있음
  - 최신 상태 문제에서 `MAX(updated_at)`만 구하면 상태 컬럼이 틀어질 수 있음
  - 세션 첫/마지막 이벤트에서 같은 시각 tie-breaker(`event_id`)를 놓치기 쉬움
  - 복귀 사용자 문제는 월 dedup을 먼저 하지 않으면 직전 활동 월 계산이 흔들림
  - 무결성 누락 문제는 기준 집합(`order_completed`)을 먼저 고정해야 함
  - regex는 `REGEXP_LIKE(col, 'abc')`가 기본적으로 부분 매칭이라는 점을 잊기 쉬움
- 정답/핵심 포인트:
  - 이상 탐지 = `GROUP BY DATE` → `LAG()` → 변화율 계산
  - 퍼널 = 날짜-유저-이벤트 dedup 후 `COUNT(DISTINCT CASE WHEN ...)`
  - 최신 상태/첫·마지막 이벤트 = `ROW_NUMBER()` + 명시적 tie-breaker
  - 중복 검출 = 중복 정의 컬럼으로 `GROUP BY` 후 `HAVING COUNT(*) >= 2`
  - 누락률 = 조건부 합계 / 전체 건수
  - 신규/현재/복귀 = 월 dedup + `MIN()` + `LAG()`
  - 무결성 누락 = `NOT EXISTS`
  - regex 전체 일치 = `^...$`
- 재발 방지 한 줄:
  - 오늘의집 스타일 문제는 **grains 고정 → dedup → 집계/윈도우 → NULL/분모0/tie-breaker 점검** 순서로 푼다
- 연결 치트시트:
  - `Curriculum/Ohouse_SQL_Test_CheatSheet.md`
  - `Curriculum/Ohouse_SQL_Test_Support/Ohouse_SQL_Syntax_Differences.md`
  - `Curriculum/Ohouse_SQL_Test_Support/Ohouse_SQL_Regex_CheatSheet.md`
