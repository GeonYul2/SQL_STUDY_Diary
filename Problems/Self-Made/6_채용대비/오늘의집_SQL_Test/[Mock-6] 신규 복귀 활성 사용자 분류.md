# [Mock-6] 신규 복귀 활성 사용자 분류

## 문제 제목
- 한국어: 신규 복귀 활성 사용자 분류
- English: Classify New, Returning, and Current Users

## 난이도
- LV_4 / Hard

## 문제 유형
- Retention, User Classification, Window Function, Date

## 문제 의도
- 이 문제가 검증하려는 SQL 역량: 월별 사용자 분류, 이전 활동 월 비교
- 데이터 품질 관점 포인트: 사용자 상태 정의를 명확히 하는 능력

---

## 문제 설명 (한국어)

`user_activity_logs` 테이블을 사용해 월별 사용자 유형을 분류하세요.

분류 기준:
- `new_user`: 해당 유저의 첫 활동 월인 경우
- `current_user`: 직전 활동 월이 바로 이전 달인 경우
- `resurrected_user`: 이전에 활동한 적은 있으나 직전 활동 월이 바로 이전 달은 아닌 경우

출력 컬럼:
- `activity_month`
- `new_users`
- `current_users`
- `resurrected_users`

결과는 `activity_month` 오름차순 정렬입니다.

## Problem Statement (English)

Using the `user_activity_logs` table, classify monthly active users into:

- `new_user`: the user's first active month
- `current_user`: the user's previous active month is the immediately previous month
- `resurrected_user`: the user was active before, but not in the immediately previous month

Return:
- `activity_month`
- `new_users`
- `current_users`
- `resurrected_users`

Order by `activity_month` ascending.

---

## 테이블 스키마

```sql
user_activity_logs(
  log_id BIGINT,
  user_id BIGINT,
  activity_time DATETIME
)
```

## 정답 파일
- `answers/[Answer][Mock-6] 신규 복귀 활성 사용자 분류.md`

## 검증 포인트
- 월 단위 dedup 여부
- 이전 활동 월 계산 정확성
- new/current/resurrected 정의 충돌 여부

## 재풀이 필요 여부
- O
