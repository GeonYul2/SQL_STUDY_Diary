# [Mock-3] 사용자 최신 상태 추출

## 문제 제목
- 한국어: 사용자 최신 상태 추출
- English: Get the Latest Status per User

## 난이도
- LV_3 / Medium

## 문제 유형
- Window Function, ROW_NUMBER, Latest State

## 문제 의도
- 이 문제가 검증하려는 SQL 역량: 최신 이벤트 추출, 동률 정렬 기준 설정
- 데이터 품질 관점 포인트: 동일 시각 이벤트 처리 기준 명확화

---

## 문제 설명 (한국어)

`user_status_logs` 테이블에는 유저의 상태 변경 이력이 저장되어 있습니다.

각 유저의 **가장 최신 상태**를 구하세요.

출력 컬럼:

- `user_id`
- `latest_status`
- `updated_at`

단,
- 최신 상태는 `updated_at`이 가장 큰 행입니다.
- 만약 같은 유저에게 동일한 `updated_at` 값이 여러 개라면, `log_id`가 더 큰 행을 최신으로 봅니다.
- 결과는 `user_id` 오름차순 정렬입니다.

## Problem Statement (English)

The `user_status_logs` table stores status change history for users.

Return the **latest status** for each user.

Output:

- `user_id`
- `latest_status`
- `updated_at`

Notes:
- The latest row is determined by the largest `updated_at`.
- If multiple rows have the same `updated_at` for the same user, use the larger `log_id` as the latest row.
- Order by `user_id` ascending.

---

## 테이블 스키마

```sql
user_status_logs(
  log_id BIGINT,
  user_id BIGINT,
  status VARCHAR(30),
  updated_at DATETIME
)
```

## 정답 파일
- `answers/[Answer][Mock-3] 사용자 최신 상태 추출.md`

## 검증 포인트
- tie-breaker 반영 여부
- `MAX(updated_at)`만 쓰고 상태 컬럼 매칭이 깨지지 않는지

## 재풀이 필요 여부
- O
