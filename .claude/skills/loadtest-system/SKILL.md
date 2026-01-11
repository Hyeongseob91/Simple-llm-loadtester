---
name: loadtest-system
description: >
  Provides comprehensive project context for llm-loadtest (vLLM load testing tool).
  Use when working with CLI, API, Core modules, Web dashboard, or Adapters.
  Triggers on questions about project structure, code navigation, metrics calculation,
  GPU monitoring, or adapter implementation. Keywords: vLLM, load test, benchmark,
  TTFT, throughput, OpenAI compatible, Triton, GPU monitor, async, aiohttp.
---

# llm-loadtest System Context

## Architecture Identity

| 구분 | 현재 상태 | 설명 |
|------|----------|------|
| **프로젝트 유형** | MSA Monorepo | services/, shared/ 분리 |
| **핵심 기능** | LLM 부하테스트 | vLLM 등 LLM 서버 성능 측정 |
| **주요 기술** | Python asyncio | 비동기 HTTP 클라이언트 기반 |
| **어댑터 패턴** | 다중 백엔드 | OpenAI Compatible, Triton 등 |

---

## Quick Reference

| Component | Location | Tech Stack | Entry Point |
|-----------|----------|------------|-------------|
| **CLI** | `services/cli/` | Typer + Rich | `main.py` |
| **API** | `services/api/` | FastAPI + WebSocket | `main.py` |
| **Web** | `services/web/` | Next.js + TailwindCSS | `src/` |
| **Core** | `shared/core/` | asyncio + aiohttp | `load_generator.py` |
| **Adapters** | `shared/adapters/` | Protocol implementations | `base.py` |
| **Database** | `shared/database/` | SQLite | `database.py` |

### Shared Modules

```
shared/
├── core/
│   ├── load_generator.py    # 부하 생성 메인 로직
│   ├── metrics.py           # TTFT, Throughput 등 메트릭 계산
│   ├── models.py            # 데이터 모델 정의
│   ├── gpu_monitor.py       # GPU 사용률 모니터링
│   ├── tokenizer.py         # 토큰 카운팅
│   └── recommend.py         # 인프라 추천 로직
├── adapters/
│   ├── base.py              # 추상 어댑터 베이스
│   ├── openai_compat.py     # OpenAI Compatible API (vLLM)
│   └── triton.py            # Triton Inference Server
└── database/
    └── database.py          # SQLite 벤치마크 결과 저장
```

### Services

```
services/
├── cli/src/llm_loadtest/
│   ├── main.py              # CLI 진입점
│   └── commands/            # run, gpu, info, recommend
├── api/src/llm_loadtest_api/
│   ├── main.py              # FastAPI 서버
│   ├── routers/             # benchmarks, recommend, websocket
│   └── services/            # benchmark_service
└── web/src/
    ├── app/                 # Next.js 페이지
    ├── components/          # UI 컴포넌트
    └── hooks/               # useBenchmarkProgress 등
```

---

## System Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                        User Interface                           │
│         ┌─────────────┐         ┌─────────────┐                │
│         │    CLI      │         │  Web UI     │                │
│         │(services/   │         │(services/   │                │
│         │    cli/)    │         │    web/)    │                │
│         └──────┬──────┘         └──────┬──────┘                │
│                │                       │                        │
│                └───────────┬───────────┘                        │
│                            │                                    │
│                            ▼                                    │
│                ┌─────────────────────┐                          │
│                │    FastAPI Server   │                          │
│                │   (services/api/)   │                          │
│                └──────────┬──────────┘                          │
│                           │                                     │
└───────────────────────────┼─────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│                     Shared Modules                              │
│  ┌───────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │LoadGenerator  │  │   Metrics    │  │ GPU Monitor  │         │
│  │(shared/core/) │──│  Calculator  │──│   (nvml)     │         │
│  └───────┬───────┘  └──────────────┘  └──────────────┘         │
│          │                                                      │
└──────────┼──────────────────────────────────────────────────────┘
           │
           ▼
┌─────────────────────────────────────────────────────────────────┐
│                  Adapters (shared/adapters/)                    │
│  ┌───────────────────┐    ┌───────────────────┐                │
│  │ OpenAI Compatible │    │   Triton Server   │                │
│  │    (vLLM, etc)    │    │                   │                │
│  └─────────┬─────────┘    └─────────┬─────────┘                │
│            │                        │                          │
└────────────┼────────────────────────┼──────────────────────────┘
             │                        │
             ▼                        ▼
      ┌──────────────┐         ┌──────────────┐
      │   vLLM       │         │   Triton     │
      │   Server     │         │   Server     │
      └──────────────┘         └──────────────┘
```

---

## Key Metrics

| Metric | Description | Unit |
|--------|-------------|------|
| **TTFT** | Time to First Token | ms |
| **TPS** | Tokens Per Second | tokens/s |
| **Throughput** | Requests Per Second | req/s |
| **Latency p50/p95/p99** | Response Time Percentiles | ms |
| **Goodput** | 성공 요청 처리량 | tokens/s |
| **GPU Utilization** | GPU 사용률 | % |
| **GPU Memory** | VRAM 사용량 | GB |

---

## Code Navigation

### Entry Points by Task

| Task | Start Here |
|------|------------|
| 부하테스트 로직 수정 | `shared/core/load_generator.py` |
| 메트릭 계산 수정 | `shared/core/metrics.py` |
| 새 어댑터 추가 | `shared/adapters/base.py` → 상속 |
| CLI 명령어 추가 | `services/cli/src/llm_loadtest/commands/` |
| API 엔드포인트 추가 | `services/api/src/llm_loadtest_api/routers/` |
| GPU 모니터링 수정 | `shared/core/gpu_monitor.py` |
| 데이터베이스 수정 | `shared/database/database.py` |
| 웹 페이지 추가 | `services/web/src/app/` |

### Common Patterns

| Pattern | Location | Example |
|---------|----------|---------|
| **Adapter** | `shared/adapters/` | `openai_compat.py`, `triton.py` |
| **CLI Command** | `services/cli/src/llm_loadtest/commands/` | `run.py`, `gpu.py` |
| **API Route** | `services/api/src/llm_loadtest_api/routers/` | `benchmarks.py` |
| **Service** | `services/api/src/llm_loadtest_api/services/` | `benchmark_service.py` |
| **Data Model** | `shared/core/models.py` | `BenchmarkResult` |

---

## CLI Commands

```bash
# 부하테스트 실행
llm-loadtest run --url http://localhost:8000 --concurrency 10

# GPU 상태 확인
llm-loadtest gpu

# 시스템 정보
llm-loadtest info

# 인프라 추천
llm-loadtest recommend --model Qwen/Qwen2.5-VL-7B-Instruct
```

---

## API Endpoints

```
POST /api/v1/benchmarks           → 벤치마크 시작
GET  /api/v1/benchmarks           → 벤치마크 목록
GET  /api/v1/benchmarks/{id}      → 벤치마크 결과
POST /api/v1/benchmarks/{id}/analyze → AI 분석 보고서 생성
WS   /ws/benchmark/{id}           → 실시간 진행 상황
POST /api/v1/recommend            → 인프라 추천
```

---

## Frequently Asked Questions

### "새 어댑터 추가하려면?"

1. `shared/adapters/base.py`의 `BaseAdapter` 상속
2. `send_request()` 메서드 구현
3. `shared/adapters/__init__.py`에 등록

### "메트릭 계산 방식 수정하려면?"

1. `shared/core/metrics.py` 수정
2. 해당 메트릭 계산 함수 수정
3. 테스트 케이스 업데이트 (`tests/unit/test_metrics.py`)

### "CLI에 새 옵션 추가하려면?"

1. `services/cli/src/llm_loadtest/commands/` 해당 명령어 파일 수정
2. Typer decorator로 옵션 추가
3. 옵션 값을 Core 모듈에 전달

---

## Related Skills

- `.claude/skills/memory-system/SKILL.md` - Goal Drift 방지
- `.claude/skills/digging/SKILL.md` - 요구사항 파고들기 (PRD 생성)

## Related Agents

- `.claude/agents/loadtest-debugger.md` - 디버깅 에이전트
- `.claude/agents/loadtest-tester.md` - 테스트 에이전트
- `.claude/agents/loadtest-code-reviewer.md` - 코드 리뷰 에이전트
