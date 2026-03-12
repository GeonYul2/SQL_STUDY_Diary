# [Mock-2] 퍼널 단계별 전환율 계산

## 문제 제목
- 한국어: 퍼널 단계별 전환율 계산
- English: Funnel Conversion Rate by Step

## 난이도
- LV_3 / Medium

## 문제 유형
- Funnel, CASE WHEN, COUNT DISTINCT, Ratio

## 문제 의도
- 이 문제가 검증하려는 SQL 역량: 퍼널 정의, 사용자 단위 중복 제거, 비율 계산
- 데이터 품질 관점 포인트: 이벤트 중복으로 인한 과대집계 방지

---

## 문제 설명 (한국어)

`app_events` 테이블에는 다음 이벤트가 저장됩니다.

- `view_item`
- `add_to_cart`
- `purchase`

각 날짜별로 아래 값을 구하세요.

- `event_date`
- `view_users`
- `cart_users`
- `purchase_users`
- `view_to_cart_rate`
- `cart_to_purchase_rate`

단,
- 같은 날짜에 같은 유저가 같은 이벤트를 여러 번 발생시켜도 **한 번만** 집계합니다.
- 전환율은 소수점 넷째 자리까지 반올림합니다.
- 결과는 `event_date` 오름차순입니다.

## Problem Statement (English)

The `app_events` table contains the following event types:

- `view_item`
- `add_to_cart`
- `purchase`

For each date, return:

- `event_date`
- `view_users`
- `cart_users`
- `purchase_users`
- `view_to_cart_rate`
- `cart_to_purchase_rate`

Notes:
- If the same user triggers the same event multiple times on the same date, count that user only once.
- Round conversion rates to 4 decimal places.
- Order by `event_date` ascending.

---

## 테이블 스키마

```sql
app_events(
  event_id BIGINT,
  user_id BIGINT,
  event_type VARCHAR(50),
  event_time DATETIME
)
```

## 정답 파일
- `answers/[Answer][Mock-2] 퍼널 단계별 전환율 계산.md`

## 검증 포인트
- 이벤트 중복 제거 여부
- 유저 수와 이벤트 건수 구분 여부
- 분모 0 처리 여부

## 재풀이 필요 여부
- O
