# Digging 질문 템플릿

## 라운드별 질문 가이드

### Round 1: 핵심 방향

**구현 위치**
```json
{
  "question": "이 기능을 어디에 구현할까요?",
  "header": "구현 위치",
  "options": [
    {"label": "CLI 명령어로", "description": "llm-loadtest [command] 형태"},
    {"label": "API 엔드포인트로", "description": "POST /api/... 형태"},
    {"label": "기존 로직에 통합", "description": "자동으로 동작하도록"},
    {"label": "복합 (CLI + 자동)", "description": "둘 다 지원"}
  ]
}
```

**핵심 방식**
```json
{
  "question": "처리 방식은 어떻게 할까요?",
  "header": "처리 방식",
  "options": [
    {"label": "동기 (즉시 결과)", "description": "요청 후 바로 응답"},
    {"label": "비동기 (백그라운드)", "description": "작업 ID 반환 후 폴링"},
    {"label": "실시간 (WebSocket)", "description": "진행 상황 스트리밍"},
    {"label": "배치 (일괄 처리)", "description": "여러 작업 묶어서 처리"}
  ]
}
```

---

### Round 2: 세부 결정

**대상 범위**
```json
{
  "question": "어떤 항목들을 대상으로 할까요?",
  "header": "대상 범위",
  "multiSelect": true,
  "options": [
    {"label": "핵심 항목만 (Recommended)", "description": "가장 중요한 2-3개"},
    {"label": "확장 (5개 이상)", "description": "상세한 분석 필요 시"},
    {"label": "전체", "description": "모든 항목 포함"},
    {"label": "사용자 선택", "description": "옵션으로 지정 가능하게"}
  ]
}
```

**허용 오차/임계값**
```json
{
  "question": "허용 범위는 어느 정도가 적절할까요?",
  "header": "허용 범위",
  "options": [
    {"label": "엄격 (±2%)", "description": "정밀한 검증 필요 시"},
    {"label": "보통 (±5%) (Recommended)", "description": "합리적인 기본값"},
    {"label": "느슨 (±10%)", "description": "대략적 확인만 필요 시"},
    {"label": "사용자 설정", "description": "--tolerance 옵션으로 지정"}
  ]
}
```

---

### Round 3: 예외 및 출력

**실패 처리**
```json
{
  "question": "실패 시 어떻게 처리할까요?",
  "header": "실패 처리",
  "options": [
    {"label": "에러로 중단", "description": "exit code 1, 프로세스 종료"},
    {"label": "경고만 표시 (Recommended)", "description": "WARNING 출력, 계속 진행"},
    {"label": "조용히 스킵", "description": "로그만 남기고 진행"},
    {"label": "사용자 선택", "description": "--strict / --warn 옵션"}
  ]
}
```

**결과 저장**
```json
{
  "question": "결과를 어디에 저장할까요?",
  "header": "결과 저장",
  "options": [
    {"label": "기존 결과에 포함 (Recommended)", "description": "BenchmarkResult 등에 필드 추가"},
    {"label": "별도 파일로", "description": "[name]_result.json 형태"},
    {"label": "콘솔 출력만", "description": "저장 없이 터미널에만 표시"},
    {"label": "선택 가능", "description": "--output 옵션으로 지정"}
  ]
}
```

**저장 위치 (PRD용)**
```json
{
  "question": "PRD를 어디에 저장할까요?",
  "header": "저장 위치",
  "options": [
    {"label": "docs/prd/ (Recommended)", "description": "프로젝트 문서 폴더에 저장"},
    {"label": "저장 안 함", "description": "콘솔 출력만, 파일 생성 없음"}
  ]
}
```

---

## 도메인 특화: llm-loadtest

### 메트릭 관련

**메트릭 선택**
```json
{
  "question": "어떤 메트릭을 포함할까요?",
  "header": "메트릭",
  "multiSelect": true,
  "options": [
    {"label": "TTFT (Time To First Token)", "description": "첫 토큰 응답 시간"},
    {"label": "Throughput (tokens/s)", "description": "초당 토큰 처리량"},
    {"label": "E2E Latency", "description": "전체 요청 응답 시간"},
    {"label": "Goodput", "description": "성공한 요청의 처리량"}
  ]
}
```

**수집 방식**
```json
{
  "question": "메트릭 수집 방식은?",
  "header": "수집 방식",
  "options": [
    {"label": "Prometheus /metrics", "description": "vLLM 내장 엔드포인트 활용"},
    {"label": "로그 파싱", "description": "서버 로그에서 추출"},
    {"label": "클라이언트 측정", "description": "요청/응답 시간 직접 측정"},
    {"label": "복합", "description": "서버 + 클라이언트 양쪽"}
  ]
}
```

### API/CLI 관련

**출력 형식**
```json
{
  "question": "출력 형식은?",
  "header": "출력 형식",
  "options": [
    {"label": "테이블 (Recommended)", "description": "터미널에서 보기 좋게"},
    {"label": "JSON", "description": "프로그래밍 연동용"},
    {"label": "CSV", "description": "스프레드시트 분석용"},
    {"label": "선택 가능", "description": "--format 옵션으로 지정"}
  ]
}
```

---

## AskUserQuestion 호출 패턴

### 2개 질문 동시 (권장)

```json
{
  "questions": [
    {
      "question": "첫 번째 질문?",
      "header": "항목1",
      "multiSelect": false,
      "options": [...]
    },
    {
      "question": "두 번째 질문?",
      "header": "항목2",
      "multiSelect": false,
      "options": [...]
    }
  ]
}
```

### multiSelect 사용 시

```json
{
  "questions": [
    {
      "question": "어떤 기능들을 포함할까요? (복수 선택 가능)",
      "header": "기능 선택",
      "multiSelect": true,
      "options": [
        {"label": "기능 A", "description": "설명"},
        {"label": "기능 B", "description": "설명"},
        {"label": "기능 C", "description": "설명"}
      ]
    }
  ]
}
```

---

## 질문 작성 체크리스트

- [ ] header는 12자 이내로 간결하게
- [ ] 선택지는 2-4개 (너무 많으면 분리)
- [ ] 합리적 기본값에 (Recommended) 표시
- [ ] description으로 각 선택의 의미 설명
- [ ] "잘 모르겠어요" 대신 "사용자 선택/설정" 옵션 제공
