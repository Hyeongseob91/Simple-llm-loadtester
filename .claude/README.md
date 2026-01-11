# Claude Code Configuration

llm-loadtest 프로젝트를 위한 Claude Code 설정입니다.

## 폴더 구조

```
.claude/
├── agents/                          # 전문 에이전트 정의
│   ├── loadtest-debugger.md         # 디버깅 전문가
│   ├── loadtest-tester.md           # 테스트 전문가
│   └── loadtest-code-reviewer.md    # 코드 리뷰 전문가
│
├── skills/                          # 스킬 정의
│   ├── loadtest-system/             # 프로젝트 컨텍스트
│   │   └── SKILL.md
│   ├── memory-system/               # Goal Drift 방지 메모리 시스템
│   │   └── SKILL.md
│   └── digging/                     # 요구사항 파고들기 (PRD 생성)
│       ├── SKILL.md
│       └── reference/
│           └── question-templates.md
│
└── memory/                          # 작업 메모리 (작업 시 생성)
    ├── task_plan.md                 # 마스터 플랜
    ├── notes.md                     # 리서치 노트
    └── checkpoint.md                # 세션 체크포인트
```

## 에이전트 사용법

### loadtest-debugger
부하테스트 문제 진단 전문가
- 트리거: "부하테스트 실패해", "vLLM 응답이 안 와", "메트릭이 이상해"

### loadtest-tester
테스트 실행 전문가
- 트리거: "테스트 실행해줘", "pytest 돌려줘"

### loadtest-code-reviewer
코드 품질 리뷰 전문가
- 트리거: "코드 리뷰해줘", "비동기 코드 체크"

## 스킬 사용법

### loadtest-system
프로젝트 전체 컨텍스트 제공
- MSA 아키텍처 (`services/`, `shared/`)
- 코드 구조, 핵심 파일 경로

### memory-system
긴 작업에서 Goal Drift 방지
- 주요 결정 전 task_plan.md 읽기
- 에러 발생 시 Error Log 기록
- 세션 종료 시 checkpoint.md 업데이트

### digging
모호한 요구사항을 파고들어 PRD로 변환
- 트리거: `/digging`, "파고들어", "PRD 작성해줘"
- 3라운드 질문 (방향 → 세부 → 예외/저장)
- Before/After 비교 + PRD 출력
- 저장 위치 항상 질문

## 메모리 시스템 사용

복잡한 작업 시작 시:

```bash
# memory 폴더에 파일 생성
mkdir -p .claude/memory
# task_plan.md, notes.md, checkpoint.md 생성 (CLAUDE.md 템플릿 참조)
```

작업 중:
1. 주요 결정 전 → task_plan.md 읽기
2. 에러 발생 → Error Log 기록
3. 세션 종료 전 → checkpoint.md 업데이트

## 핵심 파일 경로

| Task | Start Here |
|------|------------|
| 부하테스트 로직 | `shared/core/load_generator.py` |
| 메트릭 계산 | `shared/core/metrics.py` |
| GPU 모니터링 | `shared/core/gpu_monitor.py` |
| 인프라 추천 | `shared/core/recommend.py` |
| 새 어댑터 추가 | `shared/adapters/base.py` |
| 데이터베이스 | `shared/database/database.py` |
| CLI 명령어 | `services/cli/src/llm_loadtest/commands/` |
| API 엔드포인트 | `services/api/src/llm_loadtest_api/routers/` |
| AI 분석 보고서 | `services/api/.../routers/benchmarks.py:analyze_result` |
| 벤치마크 상세 페이지 | `services/web/src/app/benchmark/[id]/page.tsx` |
| 실시간 진행률 훅 | `services/web/src/hooks/useBenchmarkProgress.ts` |

## 프로젝트 구조

```
llm-loadtest/
├── services/                    # 서비스 레이어
│   ├── cli/                     # Typer 기반 CLI
│   ├── api/                     # FastAPI 백엔드
│   └── web/                     # Next.js 대시보드
│
├── shared/                      # 공유 모듈
│   ├── core/                    # 핵심 로직 (load_generator, metrics)
│   ├── adapters/                # 백엔드 어댑터 (vLLM, Triton)
│   └── database/                # SQLite 데이터베이스
│
├── tests/                       # 테스트
└── CLAUDE.md                    # Claude Code 메인 설정
```
