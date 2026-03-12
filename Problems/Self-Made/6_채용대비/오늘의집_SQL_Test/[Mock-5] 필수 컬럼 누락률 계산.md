# [Mock-5] 필수 컬럼 누락률 계산

## 문제 제목
- 한국어: 필수 컬럼 누락률 계산
- English: Calculate Missing Rate for Required Columns

## 난이도
- LV_2 / Medium

## 문제 유형
- Data Quality, CASE WHEN, Ratio, GROUP BY

## 문제 의도
- 이 문제가 검증하려는 SQL 역량: 결측치 집계, 날짜별 비율 계산
- 데이터 품질 관점 포인트: 로그 수집 품질 모니터링

---

## 문제 설명 (한국어)

`tracking_logs` 테이블에서 날짜별로 `device_id` 누락률을 구하세요.

출력 컬럼:
- `log_date`
- `total_logs`
- `missing_device_logs`
- `missing_rate`

단,
- `device_id`가 `NULL`이면 누락으로 본다.
- `missing_rate = missing_device_logs / total_logs`
- 소수점 넷째 자리까지 반올림한다.
- 결과는 `log_date` 오름차순으로 정렬한다.

## Problem Statement (English)

In the `tracking_logs` table, calculate the daily missing rate of `device_id`.

Return:
- `log_date`
- `total_logs`
- `missing_device_logs`
- `missing_rate`

Notes:
- A row is considered missing if `device_id` is `NULL`.
- `missing_rate = missing_device_logs / total_logs`
- Round to 4 decimal places.
- Order by `log_date` ascending.

---

## 테이블 스키마

```sql
tracking_logs(
  log_id BIGINT,
  user_id BIGINT,
  device_id VARCHAR(100),
  event_time DATETIME
)
```

## 정답 파일
- `answers/[Answer][Mock-5] 필수 컬럼 누락률 계산.md`

## 검증 포인트
- NULL만 누락으로 보는지
- 분모 0 처리 감각
- 날짜별 집계 정확성

## 재풀이 필요 여부
- O
