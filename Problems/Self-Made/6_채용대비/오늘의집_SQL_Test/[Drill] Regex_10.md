# [Drill] Regex 10

> 목적: MySQL 8.0 기준 정규표현식 감각을 빠르게 끌어올리기 위한 드릴  
> 원칙: **먼저 직접 패턴을 쓰고**, 정답은 별도 파일에서 확인

---

## 사용 방법

1. 문제를 보고 먼저 직접 정규표현식 또는 SQL을 작성한다.
2. 가능하면 아래 형식으로 나에게 보낸다.

```md
문제 번호: 3
내 답:
```sql
SELECT ...
```
헷갈린 점:
```

3. 정답은 `answers/[Answer][Drill] Regex_10.md`에서 확인한다.
4. 나는 정답 여부 + 더 안전한 표현 + 실수 포인트를 검토해준다.

---

## Drill 1. 숫자만 포함된 값 찾기

### 한국어
`member_code` 컬럼이 **숫자만으로 이루어진 행**만 조회하라.

### English
Return only the rows where `member_code` contains **digits only**.

---

## Drill 2. 010으로 시작하는 전화번호 찾기

### 한국어
`phone` 컬럼이 `010`으로 시작하는 행만 조회하라.

### English
Return only the rows where `phone` starts with `010`.

---

## Drill 3. 한글 이름만 찾기

### 한국어
`customer_name` 컬럼이 **한글만으로 구성된 행**만 조회하라.

### English
Return only the rows where `customer_name` consists of Korean characters only.

---

## Drill 4. 영문 대문자 3자리 + 숫자 2자리 코드

### 한국어
`product_code`가 `ABC12` 같은 형식(영문 대문자 3자리 + 숫자 2자리)인 행만 조회하라.

### English
Return only the rows where `product_code` matches the format: three uppercase letters followed by two digits.

---

## Drill 5. 이메일 형식 비슷한 값 찾기

### 한국어
`email` 컬럼이 최소한 `아이디@도메인.확장자` 형태를 만족하는 행만 조회하라.  
단, 완전한 RFC 검증은 아님.

### English
Return only the rows where `email` roughly matches `id@domain.extension`.  
This is not a full RFC validation.

---

## Drill 6. 문자열 어디엔가 숫자가 하나라도 포함된 값 찾기

### 한국어
`memo_text` 컬럼에 숫자가 하나라도 포함된 행만 조회하라.

### English
Return only the rows where `memo_text` contains at least one digit anywhere in the string.

---

## Drill 7. 전화번호에서 숫자만 남기기

### 한국어
`phone` 값이 `010-1234-5678`, `010 1234 5678`처럼 들어와 있다.  
정규표현식을 써서 숫자만 남긴 결과를 `normalized_phone`으로 출력하라.

### English
The `phone` column may contain values like `010-1234-5678` or `010 1234 5678`.  
Use a regular expression to keep digits only and output it as `normalized_phone`.

---

## Drill 8. 문자열에서 첫 번째 숫자 덩어리 추출하기

### 한국어
`order_note`가 `Order#A123`, `item-999-ready` 같은 값을 가진다.  
문자열에서 **첫 번째 숫자 덩어리**를 추출하라.

### English
The `order_note` column may contain values like `Order#A123` or `item-999-ready`.  
Extract the **first numeric chunk** from the string.

---

## Drill 9. 정확히 abc와 일치하는 값 찾기

### 한국어
`tag` 컬럼에서 정확히 `abc`인 행만 찾고 싶다.  
부분 매칭이 아니라 **완전 일치**여야 한다.

### English
Return only the rows where `tag` is exactly `abc`, not a partial match.

---

## Drill 10. 대소문자 무시하고 abc 찾기

### 한국어
`keyword` 컬럼에서 `abc`, `AbC`, `ABC` 등을 모두 찾고 싶다.  
대소문자를 무시하는 정규식 매칭을 작성하라.

### English
Return rows where `keyword` matches `abc` regardless of case, such as `abc`, `AbC`, or `ABC`.
