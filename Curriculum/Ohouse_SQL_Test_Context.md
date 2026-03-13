# 오늘의집 SQL Test 준비 컨텍스트

> 목적: 세션을 다시 열었을 때도 지금까지의 준비 맥락을 빠르게 복구하기 위한 기준 문서

---

## 1. 준비 목표

- 포지션: 오늘의집 Data Quality Assistant (인턴)
- 준비 대상: SQL Test
- 플랫폼: 프로그래머스
- 방식:
  1. 프로그래머스 재풀이 필요 문제 우선
  2. 오늘의집 특화 mock 병행
  3. 영어 + 한국어 문제 적응
  4. 오답/헷갈린 포인트 누적 기록

---

## 2. 핵심 파일

### 전체 준비 흐름
- `Curriculum/Ohouse_SQL_Test_Preparation_Plan.md`

### 재풀이 필요 문제 큐
- `Curriculum/Ohouse_SQL_Test_Review_Queue.md`

### 메인 치트시트
- `Curriculum/Ohouse_SQL_Test_CheatSheet.md`

### 보조 파일 폴더
- `Curriculum/Ohouse_SQL_Test_Support/README.md`

### 문법 차이 치트시트
- `Curriculum/Ohouse_SQL_Test_Support/Ohouse_SQL_Syntax_Differences.md`

### 정규표현식 치트시트
- `Curriculum/Ohouse_SQL_Test_Support/Ohouse_SQL_Regex_CheatSheet.md`

### 영어/한국어 표현
- `Concepts/Study_Notes/SQL_Bilingual_Vocabulary.md`

### 실수 누적 기록
- `Curriculum/Ohouse_SQL_Test_Support/SQL_Mistake_Tracker.md`

### 채용/시험 비공개 맥락
- `private/2026-03-11_ohouse_sql_test_context.md`

---

## 3. 채용대비 문제 위치

폴더:
- `Problems/Self-Made/6_채용대비/오늘의집_SQL_Test/`

구성:
- `[Drill] Regex_10.md`
- `[Mock-1]` ~ `[Mock-8]`
- `answers/` 아래 별도 정답 파일

원칙:
- 문제 파일과 정답 파일은 분리
- 문제는 한국어 + 영어 둘 다 작성
- 정답은 `answers/` 폴더에서만 확인

---

## 4. 현재 우선순위

프로그래머스 재풀이 우선 문제를 한 바퀴 돌린 뒤, 지금은 **오늘의집 특화 Mock + 최종 압축 노트 단계**다.

현재 우선 작업:
1. `Problems/Self-Made/6_채용대비/오늘의집_SQL_Test/` Mock 1~8 패턴 복습
2. `Curriculum/Ohouse_SQL_Test_CheatSheet.md` 1파일 중심 복습
3. `Curriculum/Ohouse_SQL_Test_Support/SQL_Mistake_Tracker.md` 최근 실수 재확인
4. 문법/regex가 필요할 때만 보조 치트시트 열기

---

## 5. 현재 학습 시스템

사용자는 문제를 직접 풀고, 나는 아래 방식으로 피드백한다.

### 입력 형식

```md
문제: [문제명]
내 풀이 의도:
```sql
-- SQL
```
헷갈린 점:
```

### 내가 해주는 검토

- 정답 가능성
- 출력 컬럼/정렬 조건 충족 여부
- JOIN/집계/윈도우 함수 논리 오류
- NULL/중복/분모 0 예외 처리
- 더 간단한 풀이 가능 여부
- 오늘의집/데이터 품질 관점 확장 포인트

---

## 6. 최근 기록된 헷갈림

### 프로그래머스 - 서울에 위치한 식당 목록 출력하기

- 헷갈린 포인트: `LIKE`에서 `%`와 `_`, 그리고 `서울%` vs `%서울%`
- 연결 파일:
  - `Curriculum/Ohouse_SQL_Test_Support/SQL_Mistake_Tracker.md`
  - `Curriculum/Ohouse_SQL_Test_Support/Ohouse_SQL_Syntax_Differences.md`

---

## 7. 세션 재시작 시 바로 할 일

1. `Curriculum/Ohouse_SQL_Test_Context.md` 열기
2. `Curriculum/Ohouse_SQL_Test_CheatSheet.md` 열기
3. `Curriculum/Ohouse_SQL_Test_Support/SQL_Mistake_Tracker.md`에서 최근 헷갈린 포인트 확인
4. 필요할 때만 syntax / regex 치트시트를 추가로 열기
