# 오늘의집 SQL Test - 정규표현식 치트시트

> 기준: **MySQL 8.0**  
> 목적: 시험장에서 가장 약한 정규표현식을 빠르게 다시 보기
> 범위: 이 파일은 **regex 전용**이다. GROUP BY / 집계 / 윈도우 / 날짜 함수는 `Curriculum/SQL_CodingTest_CheatSheet.md`를 본다.

공식 참고:
- MySQL 8.0 Regular Expressions: https://dev.mysql.com/doc/mysql/8.0/en/regexp.html
- MySQL 8.0 Pattern Matching: https://dev.mysql.com/doc/refman/8.0/en/pattern-matching.html

---

## 1. LIKE vs REGEXP_LIKE

| 구분 | LIKE | REGEXP_LIKE |
|---|---|---|
| 패턴 | `%`, `_` | 정규표현식 |
| 감각 | 단순 패턴 매칭 | 복잡한 규칙 매칭 |
| 주의 | 패턴 전체 의미 | 기본적으로 부분 매칭 |

```sql
col LIKE 'abc%'                 -- abc로 시작
REGEXP_LIKE(col, '^abc')        -- abc로 시작
REGEXP_LIKE(col, '^abc$')       -- 정확히 abc
REGEXP_LIKE(col, 'abc')         -- 문자열 어디든 abc가 있으면 참
```

핵심:
- 전체 일치가 필요하면 `^...$`

---

## 2. 기본 문법

| 패턴 | 의미 |
|---|---|
| `^` | 문자열 시작 |
| `$` | 문자열 끝 |
| `.` | 아무 문자 한 개 |
| `*` | 앞 패턴 0회 이상 |
| `+` | 앞 패턴 1회 이상 |
| `?` | 앞 패턴 0회 또는 1회 |
| `[0-9]` | 숫자 한 글자 |
| `[A-Za-z]` | 영문 한 글자 |
| `[가-힣]` | 한글 한 글자 |
| `[^0-9]` | 숫자가 아닌 한 글자 |
| `{n}` | 정확히 n번 |
| `{m,n}` | m~n번 |

---

## 3. MySQL 8.0 REGEXP 함수

### `REGEXP_LIKE(expr, pat[, match_type])`
- 매칭 여부 반환

```sql
REGEXP_LIKE(email, '^[^@]+@[^@]+\\.[^@]+$')
```

예시:
- `abc@test.com` → `1`
- `abc.com` → `0`

### `REGEXP_SUBSTR(expr, pat[, pos[, occurrence[, match_type]]])`
- 매칭된 문자열 반환

```sql
REGEXP_SUBSTR('Order#A123', '[0-9]+')   -- 123
```

### `REGEXP_REPLACE(expr, pat, repl[, pos[, occurrence[, match_type]]])`
- 정규식 치환

```sql
REGEXP_REPLACE(phone, '[^0-9]', '')
```

예시:
- `010-1234-5678` → `01012345678`
- `(010) 1234 5678` → `01012345678`

### `REGEXP_INSTR(expr, pat[, pos[, occurrence[, return_option[, match_type]]]])`
- 매칭 시작 위치 반환

```sql
REGEXP_INSTR('abc123xyz', '[0-9]+')
```

예시:
- `abc123xyz`에서 숫자 시작 위치 → `4`

---

## 4. match_type

| 옵션 | 의미 |
|---|---|
| `c` | 대소문자 구분 |
| `i` | 대소문자 무시 |
| `m` | 멀티라인 |
| `n` | `.`이 줄바꿈도 포함 |
| `u` | Unix line ending 기준 |

```sql
REGEXP_LIKE(col, '^abc$', 'i')
```

---

## 5. 백슬래시(escape) 주의

MySQL 문자열 + 정규식 엔진이 함께 걸려서  
**백슬래시를 두 번 써야 하는 경우가 많다.**

```sql
REGEXP_LIKE('1+2', '1\\+2')
```

시험장에서 안 맞으면:
1. `^...$` 빠졌는지
2. 백슬래시 escape 문제인지
3. 부분 매칭/전체 매칭 착각인지

---

## 6. 자주 쓰는 예시

### 숫자만

```sql
REGEXP_LIKE(col, '^[0-9]+$')
```

예시:
- `12345` → 매칭 O
- `123a45` → 매칭 X

### 영문만

```sql
REGEXP_LIKE(col, '^[A-Za-z]+$')
```

예시:
- `OpenAI` → 매칭 O
- `OpenAI1` → 매칭 X

### 한글만

```sql
REGEXP_LIKE(col, '^[가-힣]+$')
```

예시:
- `오늘의집` → 매칭 O
- `오늘의집1` → 매칭 X

### 영문+숫자 조합 코드

```sql
REGEXP_LIKE(code, '^[A-Z]{3}[0-9]{2}$')
```

예시:
- `ABC12` → 매칭 O
- `AB12` → 매칭 X
- `ABC123` → 매칭 X

### 이메일 형식 비슷한지 검사

```sql
REGEXP_LIKE(email, '^[^@]+@[^@]+\\.[^@]+$')
```

예시:
- `user@company.com` → 매칭 O
- `usercompany.com` → 매칭 X
- `user@company` → 매칭 X

### 전화번호에서 숫자만 남기기

```sql
REGEXP_REPLACE(phone, '[^0-9]', '')
```

예시:
- `010-1234-5678` → `01012345678`
- `010 1234 5678` → `01012345678`

### 문자열에서 첫 숫자만 추출

```sql
REGEXP_SUBSTR(col, '[0-9]+')
```

예시:
- `Order#A123` → `123`
- `item-999-ready` → `999`
- `ab12cd34` → `12`

---

## 7. 코테/실무 보강용 패턴 5개

### 1. OR 조건: `(a|b)`
```sql
REGEXP_LIKE(event_type, '^(view_item|add_to_cart|purchase)$')
```
예시:
- `view_item` → 매칭 O
- `purchase` → 매칭 O
- `refund` → 매칭 X
의미:
- 셋 중 하나와 **완전 일치**
맥락:
- 이벤트 타입이 허용된 값 목록 안에 있는지 검증할 때
- 데이터 품질 체크에서 **도메인 검증** 느낌으로 자주 연결 가능

### 2. 선택 문자: `?`
```sql
REGEXP_LIKE(phone, '^010[- ]?[0-9]{4}[- ]?[0-9]{4}$')
```
예시:
- `010-1234-5678` → 매칭 O
- `010 1234 5678` → 매칭 O
- `01012345678` → 매칭 O
의미:
- `-` 또는 공백이 **있어도 되고 없어도 됨**
맥락:
- 전화번호처럼 입력 포맷이 조금씩 다른 값을 허용하면서도 기본 형식을 검증할 때

### 3. 길이 제한: `{m,n}`
```sql
REGEXP_LIKE(login_id, '^[A-Za-z0-9_]{6,20}$')
```
예시:
- `user_01` → 매칭 O
- `ab` → 매칭 X
- `veryveryveryverylonguserid` → 매칭 X
의미:
- 영문/숫자/언더스코어로만 구성되고 길이는 6~20자
맥락:
- 아이디, 코드, 토큰처럼 **허용 문자 + 길이 제한**이 동시에 있는 문제

### 4. 공백 정리: `\s+`
```sql
REGEXP_REPLACE(message, '\\s+', ' ')
```
예시:
- `hello   world` → `hello world`
- `a		b` → `a b`
의미:
- 연속 공백(스페이스/탭 등)을 한 칸으로 정리
맥락:
- 로그/메모 텍스트 정제, 비교 전 normalize 용도
- 코테에서도 문자열 정리 문제에 응용 가능

### 5. 문자와 숫자를 둘 다 포함하는지 검사
```sql
REGEXP_LIKE(password_col, '[A-Za-z]')
AND REGEXP_LIKE(password_col, '[0-9]')
```
예시:
- `abc123` → 매칭 O
- `abcdef` → 매칭 X
- `123456` → 매칭 X
의미:
- 영문도 있고 숫자도 있음
맥락:
- lookahead 같은 복잡한 regex 없이, SQL에서는 **조건을 두 개로 나눠 쓰는 게 더 읽기 쉬울 때가 많음**
- 실무에서도 유지보수성이 더 좋을 수 있음

### 보강 문법의 감각
- `(a|b)` = 둘 중 하나
- `?` = 있어도 되고 없어도 됨
- `{m,n}` = 반복 횟수 제한
- `\s` = 공백류 (SQL 문자열에서는 보통 `\\s`처럼 두 번 escape를 의심)

---

## 8. 자주 틀리는 포인트

1. `REGEXP_LIKE(col, 'abc')`는 **부분 매칭**
2. 전체 일치가 필요하면 `^abc$`
3. `+`, `.`, `?` 같은 메타문자는 escape가 필요할 수 있음
4. SQL 문자열이라 `\\.` 처럼 두 번 써야 할 수 있음
5. regex는 강력하지만, 단순 prefix/suffix면 `LIKE`가 더 읽기 쉬움

---

## 9. 시험 직전 30초 암기

- 시작: `^`
- 끝: `$`
- 숫자만: `^[0-9]+$`
- 한글만: `^[가-힣]+$`
- 이메일 비슷한 형식: `^[^@]+@[^@]+\\.[^@]+$`
- 숫자만 남기기: `REGEXP_REPLACE(col, '[^0-9]', '')`
- 부분 매칭 기본, 전체 일치는 `^...$`
