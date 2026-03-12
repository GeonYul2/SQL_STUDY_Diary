# [Mock-4] 중복 결제 로그 검출

## 문제 제목
- 한국어: 중복 결제 로그 검출
- English: Detect Duplicate Payment Logs

## 난이도
- LV_3 / Medium

## 문제 유형
- Data Quality, GROUP BY, HAVING, Duplicate Detection

## 문제 의도
- 이 문제가 검증하려는 SQL 역량: 중복 정의, 그룹 집계, 이상 데이터 탐지
- 데이터 품질 관점 포인트: 동일 거래의 중복 기록 검출

---

## 문제 설명 (한국어)

`payment_logs` 테이블에는 결제 로그가 저장되어 있습니다.

아래 조건을 모두 만족하는 경우를 **중복 결제 로그**로 정의합니다.

- `order_id`가 같고
- `payment_status = 'SUCCESS'` 이고
- 같은 `paid_amount`를 가진 로그가 2건 이상 존재

중복 결제 로그가 발생한 주문을 찾아 아래를 출력하세요.

- `order_id`
- `paid_amount`
- `duplicate_count`

결과는 `order_id` 오름차순, `paid_amount` 오름차순으로 정렬하세요.

## Problem Statement (English)

The `payment_logs` table stores payment events.

Define **duplicate payment logs** as cases where:

- the same `order_id`
- with `payment_status = 'SUCCESS'`
- and the same `paid_amount`
- appear at least twice

Return:

- `order_id`
- `paid_amount`
- `duplicate_count`

Order by `order_id` ascending, then `paid_amount` ascending.

---

## 테이블 스키마

```sql
payment_logs(
  payment_log_id BIGINT,
  order_id BIGINT,
  payment_status VARCHAR(20),
  paid_amount DECIMAL(10,2),
  paid_at DATETIME
)
```

## 정답 파일
- `answers/[Answer][Mock-4] 중복 결제 로그 검출.md`

## 검증 포인트
- 실패 결제 제외 여부
- 중복 정의를 정확히 반영했는지
- `HAVING` 사용 시점 이해 여부

## 재풀이 필요 여부
- O
