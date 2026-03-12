# [Mock-1] 일자별 이벤트 로그 이상 탐지

## 문제 제목
- 한국어: 일자별 이벤트 로그 이상 탐지
- English: Daily Event Log Anomaly Check

## 난이도
- LV_2 / Medium

## 문제 유형
- Data Quality, GROUP BY, Date, Sanity Check

## 문제 의도
- 이 문제가 검증하려는 SQL 역량: 일자별 집계, 비율 계산, 이상 탐지용 조건식
- 데이터 품질 관점 포인트: 전일 대비 급감/급증 탐지

---

## 문제 설명 (한국어)

`event_logs` 테이블에는 사용자 행동 로그가 저장되어 있습니다.  
각 날짜별 이벤트 건수를 집계하고, **직전 날짜 대비 50% 이상 감소했거나 100% 이상 증가한 날짜**를 찾아주세요.

출력 컬럼은 다음과 같습니다.

- `log_date`
- `event_count`
- `prev_event_count`
- `change_ratio`

단,
- `change_ratio = (event_count - prev_event_count) / prev_event_count`
- 직전 날짜 데이터가 없는 첫 날짜는 제외합니다.
- 결과는 `log_date` 오름차순으로 정렬하세요.

## Problem Statement (English)

The `event_logs` table stores user event logs.  
For each date, count the number of events and find the dates where the event volume **decreased by at least 50% or increased by at least 100% compared to the previous date**.

Return:

- `log_date`
- `event_count`
- `prev_event_count`
- `change_ratio`

Notes:
- `change_ratio = (event_count - prev_event_count) / prev_event_count`
- Exclude the first date because it has no previous date.
- Order by `log_date` ascending.

---

## 테이블 스키마

```sql
event_logs(
  event_id BIGINT,
  user_id BIGINT,
  event_type VARCHAR(50),
  event_time DATETIME
)
```

## 정답 파일
- `answers/[Answer][Mock-1] 일자별 이벤트 로그 이상 탐지.md`

## 검증 포인트
- 첫 날짜 제외 여부
- 분모 0 방지 여부
- 날짜 단위 집계 정확성

## 재풀이 필요 여부
- O
