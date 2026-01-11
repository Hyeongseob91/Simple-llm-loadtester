# AI 전화 상담 시스템 SLO 명세

> **Created**: 2026-01-11
> **Status**: Active

---

## 시스템 개요

AI 상담사가 고객 통화를 실시간 응대하는 시스템

```
[고객] → STT → [Text] → RAG Engine → [LLM] → TTS → [고객]
                         (Retrieval)   (vLLM)
```

---

## 인프라 구성

| Component | Spec |
|-----------|------|
| LLM Server | vLLM |
| GPU | NVIDIA RTX PRO 6000 Blackwell × 2 |
| GPU Memory Utilization | 0.85 |
| Model | qwen3-vl-30b-thinking (또는 동급) |

---

## 파이프라인 시간 예산 (전체 1초)

| 구간 | 할당 시간 | 비고 |
|------|-----------|------|
| STT (음성→텍스트) | ~200ms | 협력사 제공 (스트리밍) |
| RAG Retrieval | ~50ms | Weaviate 벡터 검색 |
| **LLM (TTFT)** | **~550ms** | vLLM 첫 토큰 생성 |
| TTS (텍스트→음성) | ~200ms | 협력사 제공 (스트리밍) |
| **Total** | **~1,000ms** | 고객 질문 → AI 음성 시작 |

---

## SLO 요구사항

### 응답 시간 목표

| Metric | Target | 기준 | 설명 |
|--------|--------|------|------|
| **TTFT** | 500ms | p90 | 90% 요청이 500ms 내 첫 토큰 |
| **TPOT** | 100ms | p90 | 토큰당 생성 시간 (여유) |
| **E2E** | 10,000ms | p90 | 전체 응답 완료 (스트리밍) |

### 서비스 목표

| 항목 | 값 |
|------|-----|
| 동시 통화 목표 | 100~300명 |
| 트래픽 패턴 | 24시간 고른 분포 |
| 응답 길이 | 50~300 토큰 (질문에 따라) |
| 에러율 목표 | < 1% |

---

## Goodput Thresholds 설정값

벤치마크 테스트 시 사용할 값:

```json
{
  "ttft_ms": 500,
  "tpot_ms": 100,
  "e2e_ms": 10000
}
```

---

## 벤치마크 해석 가이드

### TTFT p90 기준 동시 통화 가능 수 추정

| TTFT p90 | 권장 Concurrency | 비고 |
|----------|------------------|------|
| < 500ms | ✅ SLO 달성 | 서비스 가능 |
| 500~800ms | ⚠️ 경고 | 피크 시 SLO 위반 가능 |
| > 800ms | ❌ SLO 미달 | 인프라 증설 필요 |

### 필요 조치 결정 흐름

```
TTFT p90 > 500ms at 목표 Concurrency?
│
├─ YES → 선택:
│         1. 동시 통화 목표 하향 조정
│         2. GPU 증설
│         3. 더 작은 모델로 교체
│
└─ NO  → ✅ 현재 인프라로 서비스 가능
```

---

## 다음 단계

1. [ ] 새 Goodput 설정으로 벤치마크 재실행
2. [ ] RTX PRO 6000 Blackwell 실제 성능 측정
3. [ ] 동시 통화 100명 기준 TTFT p90 확인
4. [ ] 필요 시 인프라 조정 계획 수립

---

## 참고: 벤치마크 명령어 예시

```bash
# Web UI에서 실행
# Presets: Custom
# Concurrency: 10,30,50,100
# Prompts: 200
# Input Len: 128 (상담 질문 수준)
# Output Len: 100 (평균 응답)
# Goodput SLO: TTFT=500, TPOT=100, E2E=10000
```
