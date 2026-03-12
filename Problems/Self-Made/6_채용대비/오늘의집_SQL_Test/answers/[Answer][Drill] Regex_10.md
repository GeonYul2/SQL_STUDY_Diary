# [Answer][Drill] Regex 10

## 정답 / Answer Key

### Drill 1
```sql
WHERE REGEXP_LIKE(member_code, '^[0-9]+$')
```

### Drill 2
```sql
WHERE REGEXP_LIKE(phone, '^010')
```

### Drill 3
```sql
WHERE REGEXP_LIKE(customer_name, '^[가-힣]+$')
```

### Drill 4
```sql
WHERE REGEXP_LIKE(product_code, '^[A-Z]{3}[0-9]{2}$')
```

### Drill 5
```sql
WHERE REGEXP_LIKE(email, '^[^@]+@[^@]+\.[^@]+$')
```

### Drill 6
```sql
WHERE REGEXP_LIKE(memo_text, '[0-9]+')
```

### Drill 7
```sql
SELECT REGEXP_REPLACE(phone, '[^0-9]', '') AS normalized_phone
```

### Drill 8
```sql
SELECT REGEXP_SUBSTR(order_note, '[0-9]+') AS first_number
```

### Drill 9
```sql
WHERE REGEXP_LIKE(tag, '^abc$')
```

### Drill 10
```sql
WHERE REGEXP_LIKE(keyword, '^abc$', 'i')
```

## 자주 하는 실수

1. 전체 일치인데 `^...$`를 안 붙임
2. `LIKE`와 `REGEXP_LIKE`의 부분/전체 매칭 감각을 섞어 씀
3. `.` 을 마침표 그대로 생각함
4. `+` 같은 메타문자 escape를 잊음
5. SQL 문자열에서 백슬래시를 하나만 써서 실패함
