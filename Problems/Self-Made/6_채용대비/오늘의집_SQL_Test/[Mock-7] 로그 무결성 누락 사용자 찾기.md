# [Mock-7] 로그 무결성 누락 사용자 찾기

## 문제 제목
- 한국어: 로그 무결성 누락 사용자 찾기
- English: Find Users Missing Required Follow-up Logs

## 난이도
- LV_3 / Medium

## 문제 유형
- Data Quality, NOT EXISTS, Integrity Check

## 문제 의도
- 이 문제가 검증하려는 SQL 역량: 안티 조인, 존재/부재 검증
- 데이터 품질 관점 포인트: 필수 후속 이벤트 누락 탐지

---

## 문제 설명 (한국어)

`service_events` 테이블에서 `order_completed` 이벤트가 발생한 유저 중,
같은 날짜에 `payment_success` 이벤트가 **한 번도 없는 유저**를 찾으세요.

출력 컬럼:
- `event_date`
- `user_id`

결과는 `event_date`, `user_id` 오름차순 정렬입니다.

## Problem Statement (English)

In the `service_events` table, find users who had an `order_completed` event but **no `payment_success` event on the same date**.

Return:
- `event_date`
- `user_id`

Order by `event_date`, `user_id` ascending.

---

## 테이블 스키마

```sql
service_events(
  event_id BIGINT,
  user_id BIGINT,
  event_type VARCHAR(50),
  event_time DATETIME
)
```

## 정답 파일
- `answers/[Answer][Mock-7] 로그 무결성 누락 사용자 찾기.md`

## 검증 포인트
- 같은 날짜 기준 비교 여부
- `NOT EXISTS` 사용 이유 이해 여부
- 중복 사용자 제거 여부

## 재풀이 필요 여부
- O
