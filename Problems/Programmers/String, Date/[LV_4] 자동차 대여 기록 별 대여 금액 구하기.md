# [LV_4] 자동차 대여 기록 별 대여 금액 구하기

> **정보**
> - **날짜**: 2026년 3월 12일
> - **분류**: 프로그래머스 (LV_4)
> - **주제**: String, Date
> - **재풀이 여부**: O

### ✨ 핵심 포인트
- 대여 기간은 `DATEDIFF(END_DATE, START_DATE) + 1`
- 할인 구간 분기는 **큰 구간부터** 체크하거나, 겹치지 않게 범위를 나눠야 함
- `CASE`는 위에서 아래로 내려오며 **처음 만족한 조건에서 종료**
- 할인 금액을 따로 빼는 식보다 **최종 결제 금액 = 원금 × (1 - 할인율)** 로 바로 계산하는 것이 안전
- 할인 플랜이 없는 7일 미만 대여도 결과에 포함해야 하므로 `LEFT JOIN` 필요

### 🎯 문제 설명

`CAR_RENTAL_COMPANY_CAR`, `CAR_RENTAL_COMPANY_RENTAL_HISTORY`, `CAR_RENTAL_COMPANY_DISCOUNT_PLAN`
테이블을 사용해 **트럭 대여 기록별 최종 대여 금액**을 구하는 문제다.

- 차량 정보 테이블에서 일일 대여 요금과 차종을 확인
- 대여 이력 테이블에서 대여 기간을 계산
- 할인 정책 테이블에서 기간별 할인율을 매칭
- 결과로 `HISTORY_ID`, `FEE`를 출력
- 정렬은 `FEE DESC`, `HISTORY_ID DESC`

---

### 문제 해석 포인트

- 이 문제는 **“할인액”** 을 구하는 문제가 아니라 **“최종 대여 금액”** 을 구하는 문제다
- 따라서 계산식은
  - `원금 = 일일 요금 × 대여 일수`
  - `최종 금액 = 원금 × (1 - 할인율 / 100)`
- 할인 구간은 다음처럼 생각하면 편하다
  - 7일 이상 30일 미만
  - 30일 이상 90일 미만
  - 90일 이상

---

### 💡 풀이 과정
- 먼저 트럭 대여 기록만 가져오고, `DATEDIFF + 1`로 실제 대여 일수를 계산한다.
- `CASE`로 할인 구간 문자열(`DURATION_TYPE`)을 만든다.
- 할인 플랜이 없는 7일 미만 대여도 포함해야 하므로 `LEFT JOIN`으로 할인율을 붙인다.
- `COALESCE(CP.DISCOUNT_RATE, 0)`로 할인율이 없는 경우 0% 처리한다.
- 최종 금액을 계산한 뒤 문제 조건대로 정렬한다.

```sql
WITH BASE AS (
    SELECT
        H.HISTORY_ID,
        H.CAR_ID,
        C.CAR_TYPE,
        C.DAILY_FEE,
        DATEDIFF(H.END_DATE, H.START_DATE) + 1 AS PERIOD,
        CASE
            WHEN DATEDIFF(H.END_DATE, H.START_DATE) + 1 >= 90 THEN '90일 이상'
            WHEN DATEDIFF(H.END_DATE, H.START_DATE) + 1 >= 30 THEN '30일 이상'
            WHEN DATEDIFF(H.END_DATE, H.START_DATE) + 1 >= 7 THEN '7일 이상'
            ELSE NULL
        END AS DURATION_TYPE
    FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY AS H
    JOIN CAR_RENTAL_COMPANY_CAR AS C
        ON H.CAR_ID = C.CAR_ID
    WHERE C.CAR_TYPE = '트럭'
)
SELECT
    B.HISTORY_ID,
    FLOOR(B.DAILY_FEE * B.PERIOD * (1 - COALESCE(CP.DISCOUNT_RATE, 0) / 100)) AS FEE
FROM BASE AS B
LEFT JOIN CAR_RENTAL_COMPANY_DISCOUNT_PLAN AS CP
    ON B.CAR_TYPE = CP.CAR_TYPE
   AND B.DURATION_TYPE = CP.DURATION_TYPE
ORDER BY
    FEE DESC,
    B.HISTORY_ID DESC;
```

> [!WARNING]
> **주의사항**
> - `CASE WHEN period > 7 THEN ... WHEN period > 30 THEN ...` 처럼 쓰면 `32일`도 첫 조건(`> 7`)에 먼저 걸려서 `7일 이상`으로 분류된다.
> - `BETWEEN`은 양끝값을 모두 포함하므로 `BETWEEN 7 AND 30`, `BETWEEN 30 AND 90`처럼 쓰면 `30`, `90`에서 구간이 겹친다.
> - 할인 정책 테이블을 `INNER JOIN`하면 할인 플랜이 없는 구간(예: 7일 미만)이 결과에서 사라질 수 있다.
