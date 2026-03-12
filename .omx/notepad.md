

## PRIORITY
Ohouse SQL prep: open Curriculum/Ohouse_SQL_Test_Context.md first. Track mistakes in Concepts/Study_Notes/SQL_Mistake_Tracker.md. Current priority = Programmers review queue first, then Ohouse mocks.


## WORKING MEMORY
[2026-03-11T11:49:04.903Z] 2026-03-11: Added SQL_Mistake_Tracker.md and Ohouse_SQL_Test_Context.md. Recent confusion: LIKE '서울%' vs '%서울%' and % vs _ wildcard meaning on Programmers '서울에 위치한 식당 목록 출력하기'.

[2026-03-11T12:06:04.044Z] 2026-03-11: Added mistake note for Programmers '오프라인-온라인 판매 데이터 통합하기' — recurring confusion on NULL AS missing column, DATE_FORMAT placeholders (%Y-%m, %Y-%m-%d), output formatting, and UNION vs UNION ALL (prefer UNION ALL when no dedup required).
[2026-03-12T06:13:06.454Z] 2026-03-12 user flow note: follow solved-problem sequence under Problems/Programmers. User re-solved both SELECT problems already; next study section is SUM, MAX, MIN. Continue with same workflow: user sends SQL for previously solved problems, assistant reviews answer quality and archives confusion points into Concepts/Study_Notes/SQL_Mistake_Tracker.md.
[2026-03-12T06:30:39.946Z] 2026-03-12: Programmers SUM,MAX,MIN '물고기 종류 별 대어 찾기' re-solved cleanly. Core memo: compute MAX per group, then join back to base table to recover detail rows (ID, name, length). Important pitfall worth remembering: join on both group key and max value to avoid accidental cross-group duplicates.
[2026-03-12T07:08:41.265Z] 2026-03-12: Added tracker note for Programmers '업그레이드 할 수 없는 아이템 구하기'. User solved it but found it hard. Core memo: read the problem carefully, determine whether target is root or leaf, then use self-join/LEFT JOIN thinking on parent-child direction to keep only leaf items.
[2026-03-12T07:54:02.499Z] 2026-03-12: Added formatted Programmers LV4 String, Date note for '자동차 대여 기록 별 대여 금액 구하기' and archived mistakes. Key reminders: CASE is first-match top-down, avoid overlapping BETWEEN boundaries, use LEFT JOIN for missing discount plans, and calculate final fee as principal * (1 - discount_rate/100).
[2026-03-12T09:22:22.603Z] 2026-03-12: Cleanly solved JOIN LV5 '상품을 구매한 회원 비율 구하기'. Short memo: use CROSS JOIN (or equivalent one-row join) to attach total user count denominator to monthly purchased-user aggregates when computing the ratio.
[2026-03-12T09:23:35.975Z] 2026-03-12: Synced recent recurring memos into Curriculum/Ohouse_SQL_Test_CheatSheet.md under '최근 재풀이 메모' (MAX/MIN join-back, root vs leaf, CASE first-match ordering, BETWEEN boundary overlap, final fee formula, LEFT JOIN for missing plans, CROSS JOIN for denominator rows).
[2026-03-12T09:46:46.169Z] 2026-03-12: Added JOIN LV4 '그룹별 조건에 맞는 식당 목록 출력하기' to Mistake Tracker. User's main memo: forgot rank-family window functions; reliable pattern is GROUP BY count -> RANK() -> filter rank=1 -> join back to profile/base rows. Also synced rank memo to CheatSheet.
[2026-03-12T10:47:35.451Z] 2026-03-12: Added GROUP BY LV4 '입양 시각 구하기(2)' to Mistake Tracker. Main confusion: recursive CTE pattern for generating 0~23 hours; COALESCE itself was fine. CheatSheet updated with 'build full axis first, then LEFT JOIN + COALESCE' and 'WITH RECURSIVE = anchor + recursive step + stop condition'.