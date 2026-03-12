# [Mock-8] 세션별 첫 이벤트와 마지막 이벤트 추출

## 문제 제목
- 한국어: 세션별 첫 이벤트와 마지막 이벤트 추출
- English: Get the First and Last Event per Session

## 난이도
- LV_4 / Hard

## 문제 유형
- Window Function, Session Analysis, Latest/First State

## 문제 의도
- 이 문제가 검증하려는 SQL 역량: 세션 단위 정렬, 첫/마지막 이벤트 추출
- 데이터 품질 관점 포인트: 세션 흐름 해석 능력

---

## 문제 설명 (한국어)

`session_events` 테이블에서 각 `session_id`별로 첫 이벤트와 마지막 이벤트를 구하세요.

출력 컬럼:
- `session_id`
- `first_event_type`
- `first_event_time`
- `last_event_type`
- `last_event_time`

단,
- 첫 이벤트는 가장 이른 `event_time`
- 마지막 이벤트는 가장 늦은 `event_time`
- 같은 시각이 여러 건이면 `event_id`가 작은 것을 첫 이벤트, 큰 것을 마지막 이벤트로 본다.
- 결과는 `session_id` 오름차순 정렬

## Problem Statement (English)

In the `session_events` table, return the first and last event for each `session_id`.

Return:
- `session_id`
- `first_event_type`
- `first_event_time`
- `last_event_type`
- `last_event_time`

Notes:
- The first event is the earliest `event_time`
- The last event is the latest `event_time`
- If multiple rows share the same timestamp, use the smaller `event_id` for the first event and the larger `event_id` for the last event
- Order by `session_id` ascending

---

## 테이블 스키마

```sql
session_events(
  event_id BIGINT,
  session_id BIGINT,
  event_type VARCHAR(50),
  event_time DATETIME
)
```

## 정답 파일
- `answers/[Answer][Mock-8] 세션별 첫 이벤트와 마지막 이벤트 추출.md`

## 검증 포인트
- 첫/마지막 tie-breaker 반영 여부
- 세션별 1건씩 정확히 출력되는지
- 첫/마지막 추출 로직 명확성

## 재풀이 필요 여부
- O
