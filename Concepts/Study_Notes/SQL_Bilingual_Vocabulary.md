# SQL 코테 영어/한국어 표현 정리

영어 문제를 빠르게 읽고, 한국어 개념과 바로 연결하기 위한 메모입니다.

## 자주 나오는 표현

| English | 한국어 | 메모 |
|---|---|---|
| retrieve | 조회하다 | SELECT 의미로 자주 나옴 |
| find | 구하다 | 결과 집계/조건 검색 |
| distinct | 중복 제거 | DISTINCT |
| total number of | 총 개수 | COUNT와 연결 |
| average | 평균 | AVG |
| ratio | 비율 | 분모/분자 주의 |
| conversion rate | 전환율 | 보통 0~1 또는 % |
| retain / retention | 유지 / 리텐션 | cohort와 자주 연결 |
| most recent | 가장 최근 | MAX(date), ROW_NUMBER |
| for each | 각 ~별로 | GROUP BY 신호 |
| at least | 최소 ~이상 | >= |
| no later than | ~이전까지 | 날짜 조건 주의 |
| missing | 누락된 | NULL 체크 |
| duplicate | 중복된 | dedup / COUNT 비교 |
| invalid | 유효하지 않은 | domain/range check |

## 데이터 품질 표현

| English | 한국어 |
|---|---|
| data quality | 데이터 품질 |
| sanity check | 상식적 범위 검증 |
| integrity check | 무결성 검증 |
| null check | 결측치 검증 |
| row count check | 건수 검증 |
| anomaly detection | 이상 탐지 |
| event log | 이벤트 로그 |
| user activity | 사용자 활동 |
| behavior data | 행동 데이터 |

## 윈도우 함수 표현

| English | 한국어 |
|---|---|
| rank users by | ~기준으로 유저 순위 매기기 |
| previous row | 이전 행 |
| cumulative sum | 누적합 |
| running total | 누적합 |
| partition by | 그룹별로 나누어 |
| order within each group | 각 그룹 내 정렬 |

## 계속 추가할 것

- 실제 프로그래머스/영문 문제에서 본 표현
- 헷갈렸던 표현
- QA / 로그 분석 관련 표현
