---
name: memory-system
description: >
  3-File Memory System for preventing Goal Drift in long-running tasks.
  Automatically activated for complex multi-step implementations.
  Core principle: "Before any major decision, read the plan file."
  Keywords: memory, plan, checkpoint, notes, task, goal drift, context
---

# Memory System Protocol

> **Purpose**: Context Window 한계를 우회하여 Goal Drift(목표 표류) 방지

**상세 내용**: `CLAUDE.md`의 Memory System 섹션 참조

---

## Quick Reference

### 3가지 핵심 규칙

| 규칙 | 설명 | 시점 |
|------|------|------|
| **READ BEFORE DECIDE** | `task_plan.md` 읽기 | 주요 결정 전 |
| **LOG ALL ERRORS** | Error Log에 기록 | 에러 발생 시 |
| **UPDATE CHECKPOINT** | `checkpoint.md` 업데이트 | 세션 종료 전 |

### 파일 구조

```
.claude/memory/
├── task_plan.md      # Master Plan - 목표, 단계, 에러 로그
├── notes.md          # Research Notes - 발견 사항, 코드 참조
└── checkpoint.md     # Session State - 진행 상황, 다음 단계
```

---

## 활성화 조건

다음 조건 중 하나라도 해당되면 메모리 시스템 사용:
- 3단계 이상의 작업
- 여러 파일 수정이 필요한 작업
- 리팩토링, 기능 추가 등 복잡한 작업
- 에러 해결이 3회 이상 실패한 경우

---

## 빠른 시작

```bash
# 메모리 폴더 생성
mkdir -p .claude/memory

# 파일 생성 (CLAUDE.md 템플릿 참조)
```

---

## 3회 반복 규칙

동일 에러 3회 반복 시:
1. Error Patterns에 기록
2. 접근 방식 변경 필수
3. notes.md에 대안 기록

---

## 세션 재개 체크리스트

```markdown
- [ ] checkpoint.md 읽기
- [ ] task_plan.md Current Status 확인
- [ ] notes.md 관련 정보 스캔
```

---

## 상세 템플릿

`CLAUDE.md`의 File Templates 섹션에 전체 템플릿이 있습니다.
