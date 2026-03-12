# 오늘의집 SQL Test - 정규표현식 치트시트

> 기준: **MySQL 8.0**  
> 목적: 시험장에서 가장 약한 정규표현식을 빠르게 다시 보기

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

### `REGEXP_INSTR(expr, pat[, pos[, occurrence[, return_option[, match_type]]]])`
- 매칭 시작 위치 반환

```sql
REGEXP_INSTR('abc123xyz', '[0-9]+')
```

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

### 영문만

```sql
REGEXP_LIKE(col, '^[A-Za-z]+$')
```

### 한글만

```sql
REGEXP_LIKE(col, '^[가-힣]+$')
```

### 영문+숫자 조합 코드

```sql
REGEXP_LIKE(code, '^[A-Z]{3}[0-9]{2}$')
```

### 이메일 형식 비슷한지 검사

```sql
REGEXP_LIKE(email, '^[^@]+@[^@]+\\.[^@]+$')
```

### 전화번호에서 숫자만 남기기

```sql
REGEXP_REPLACE(phone, '[^0-9]', '')
```

### 문자열에서 첫 숫자만 추출

```sql
REGEXP_SUBSTR(col, '[0-9]+')
```

---

## 7. 자주 틀리는 포인트

1. `REGEXP_LIKE(col, 'abc')`는 **부분 매칭**
2. 전체 일치가 필요하면 `^abc$`
3. `+`, `.`, `?` 같은 메타문자는 escape가 필요할 수 있음
4. SQL 문자열이라 `\\.` 처럼 두 번 써야 할 수 있음
5. regex는 강력하지만, 단순 prefix/suffix면 `LIKE`가 더 읽기 쉬움

---

## 8. 시험 직전 30초 암기

- 시작: `^`
- 끝: `$`
- 숫자만: `^[0-9]+$`
- 한글만: `^[가-힣]+$`
- 이메일 비슷한 형식: `^[^@]+@[^@]+\\.[^@]+$`
- 숫자만 남기기: `REGEXP_REPLACE(col, '[^0-9]', '')`
- 부분 매칭 기본, 전체 일치는 `^...$`
