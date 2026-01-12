# LLM Loadtest

> LLM 서빙 서버의 성능을 측정하고 최적화하기 위한 부하 테스트 도구

vLLM, SGLang, Ollama 등 OpenAI-compatible API 서버의 부하 테스트를 수행하고, 결과를 Web 대시보드에서 시각화합니다.

## 핵심 가치

| 가치 | 설명 |
|------|------|
| **정확한 측정** | tiktoken 기반 토큰 카운팅, LLM 특화 메트릭 (TTFT, TPOT, ITL) |
| **품질 기반 평가** | Goodput - SLO를 만족하는 요청 비율 측정 |
| **실시간 모니터링** | WebSocket 진행률, GPU 메트릭 (메모리, 활용률, 온도, 전력) |
| **시각화** | 인터랙티브 차트, 모델 비교, 결과 내보내기 |
| **확장성** | 어댑터 패턴으로 vLLM, SGLang, Ollama, Triton 등 지원 |

---

## 빠른 시작

### Docker Compose (권장)

```bash
# 저장소 클론
git clone https://github.com/your-org/llm-loadtest.git
cd llm-loadtest

# 전체 서비스 시작
docker compose up -d

# 접속
# - Web UI: http://localhost:5050
# - API Docs: http://localhost:8085/docs
```

### CLI 설치

```bash
# 프로젝트 루트에서
pip install -e .

# 기본 부하 테스트
llm-loadtest run \
  --server http://localhost:8000 \
  --model qwen3-14b \
  --concurrency 1,10,50 \
  --num-prompts 100

# Goodput 측정 (SLO 기반)
llm-loadtest run \
  --server http://localhost:8000 \
  --model qwen3-14b \
  --concurrency 50 \
  --goodput ttft:500,tpot:50
```

### 인프라 추천

```bash
# GPU 인프라 추천
llm-loadtest recommend \
  --server http://localhost:8000 \
  --model qwen3-14b \
  --peak-concurrency 500 \
  --goodput-target 95

# 출력 예시: "NVIDIA H100 × 5장 필요합니다"
```

---

## 주요 기능

| 카테고리 | 기능 | 상태 |
|---------|------|------|
| **부하 테스트** | 다중 동시성 레벨 테스트, 요청 수/기간 기반 모드 | ✅ |
| **메트릭** | TTFT, TPOT, ITL, E2E Latency, Throughput, Goodput | ✅ |
| **GPU** | 메모리, 활용률, 온도, 전력 모니터링 | ✅ |
| **Web UI** | 실시간 대시보드, 결과 비교, 다크모드 | ✅ |
| **AI 분석** | 벤치마크 결과 AI 분석 보고서, Thinking 모델 지원 | ✅ |
| **검증** | 클라이언트-서버 메트릭 교차 검증, Docker 로그 기반 | ✅ |
| **인프라 추천** | GPU 인프라 자동 추천, SLO 기반 용량 산정 | ✅ |
| **내보내기** | CSV/Excel 다운로드 | ✅ |

---

## 스크린샷

### 대시보드
벤치마크 현황을 한눈에 파악하고 빠르게 액션 수행

### 실시간 모니터링
테스트 진행 중 실시간 Throughput/TTFT 추이 확인

### 결과 분석
Concurrency별 성능 비교 차트 및 상세 테이블

---

## 문서

| 문서 | 설명 |
|------|------|
| [메트릭 상세](docs/guides/metrics.md) | TTFT, TPOT, Goodput 등 메트릭 설명 |
| [Web UI 가이드](docs/guides/web-ui.md) | 대시보드, 벤치마크, 결과 페이지 사용법 |
| [CLI 레퍼런스](docs/guides/cli.md) | run, recommend, info, gpu 명령어 |
| [API 레퍼런스](docs/guides/api.md) | REST API 엔드포인트 |
| [설정 옵션](docs/guides/configuration.md) | 환경변수, 인증, 어댑터 설정 |
| [아키텍처](docs/guides/architecture.md) | 시스템 구조, 프로젝트 폴더 |
| [개발 가이드](docs/guides/development.md) | 로컬 개발, 테스트, 로드맵 |
| [FAQ](docs/guides/faq.md) | 자주 묻는 질문 |

---

## 지원 서버

| 서버 | 어댑터 | 상태 |
|------|--------|------|
| vLLM | openai | ✅ 지원 |
| SGLang | openai | ✅ 지원 |
| Ollama | openai | ✅ 지원 |
| LMDeploy | openai | ✅ 지원 |
| Triton | triton | 🚧 개발 중 |

---

## 프로젝트 구조

```
llm-loadtest/
├── services/
│   ├── api/          # FastAPI 백엔드
│   ├── cli/          # CLI 도구
│   └── web/          # Next.js 대시보드
├── shared/
│   ├── core/         # 핵심 로직 (부하 생성, 메트릭)
│   ├── adapters/     # 서버 어댑터
│   └── database/     # SQLite 저장소
├── docs/
│   └── guides/       # 사용 가이드
└── docker-compose.yml
```

---

## Security Configuration

### API Authentication (Optional)

프로덕션 환경에서는 API 인증을 활성화하는 것을 권장합니다:

```bash
# .env 파일 생성
cp services/api/.env.example services/api/.env

# API_KEY 설정 (주석 해제 후 값 입력)
# API_KEY=your-secure-random-key
```

인증이 활성화되면 쓰기 작업(POST, DELETE)에 `X-API-Key` 헤더가 필요합니다.

### CORS Configuration

프로덕션 배포 시 CORS를 특정 도메인으로 제한하세요:

```bash
# .env 파일에서
CORS_ORIGINS=https://your-domain.com
```

### Docker Network (Local Development)

로컬에서 Docker 컨테이너가 호스트의 vLLM 서버에 접근하려면:

```bash
# docker-compose.override.yml 생성
cp docker-compose.override.yml.example docker-compose.override.yml

# 환경에 맞게 수정 (host.docker.internal 또는 실제 IP)
```

`docker-compose.override.yml`은 `.gitignore`에 포함되어 git에 추적되지 않습니다.

---

## 라이선스

MIT License

---

## 기여

버그 리포트, 기능 제안, PR을 환영합니다!

1. Fork the repository
2. Create your feature branch
3. Commit your changes
4. Push to the branch
5. Open a Pull Request
