# 오늘의집 SQL Test 대비

오늘의집 Data Quality Assistant 포지션 맥락에 맞춘 **회사 특화형 SQL mock 문제**를 정리하는 폴더입니다.

## 목적

- 로그 데이터 품질 검증 관점 연습
- 사용자 행동 로그 분석 관점 연습
- 프로그래머스 스타일 제한 시간 대비
- 영어/한국어 문제 서술에 모두 익숙해지기

## 운영 원칙

1. **문제는 영어 + 한국어 둘 다 제공**
2. **MySQL 8.0 기준**
3. **문제 파일과 정답 파일을 분리**
4. **가능하면 프로그래머스 스타일 출력 형식으로 작성**

## 구조

- 문제 파일: 이 폴더 바로 아래
- 정답 파일: `answers/`

예:
- 문제: `[Mock-1] ... .md`
- 정답: `answers/[Answer][Mock-1] ... .md`

## 추천 구성

- `TEMPLATE.md` 기준으로 문제 작성
- 난이도는 Easy / Medium / Hard 또는 LV_1 ~ LV_4로 표시
- 아래 유형을 우선 작성
  - 로그 품질 검증
  - funnel / retention
  - 중복 / 결측 / integrity check
  - 최신 상태 추출
  - window function 기반 행동 분석

## 병행 전략

- `Problems/Programmers/` : 플랫폼 적응용
- `Problems/Self-Made/6_채용대비/오늘의집_SQL_Test/` : 포지션 맞춤형

둘을 병행해서,

- **프로그래머스 감각**
- **실무형 데이터 QA 감각**

을 같이 가져가는 것을 목표로 한다.
