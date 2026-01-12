# PRD: 오픈소스 공개 전 보안 점검

> **Version**: 1.0
> **Created**: 2026-01-12
> **Status**: Completed
> **라이선스**: MIT License

---

## 1. 개요

### 1.1 배경
llm-loadtest 프로젝트를 오픈소스로 공개하고 LinkedIn에 공유할 예정입니다. 현재 코드에 사내 인프라 정보(IP 주소, 서버 URL)가 하드코딩되어 있어 공개 전 보안 점검이 필요합니다.

### 1.2 목적
- 사내 인프라 정보(IP, 호스트명) 제거
- 오픈소스 공개에 적합한 설정으로 변경
- 보안 가이드 문서화

### 1.3 범위
| 포함 | 제외 |
|------|------|
| docker-compose.yml IP 제거 | Git 히스토리 rewrite |
| Web UI/API 기본값 변경 | CLI 예제 코드 변경 |
| README 보안 가이드 추가 | 테스트 코드 수정 |
| LICENSE 파일 생성 | CONTRIBUTING.md 생성 |

---

## 2. 요구사항

### 2.1 기능 요구사항

| ID | 요구사항 | 우선순위 | 상태 |
|----|---------|---------|------|
| FR-1 | docker-compose.yml에서 사내 IP(192.168.123.35) 제거 | P0 | ✅ 완료 |
| FR-2 | Web UI 기본 server_url을 빈 문자열로 변경 | P0 | ✅ 완료 |
| FR-3 | API 엔드포인트 기본값을 빈 문자열로 변경 | P0 | ✅ 완료 |
| FR-4 | docker-compose.override.yml.example 생성 | P1 | ✅ 완료 |
| FR-5 | README에 보안 설정 가이드 섹션 추가 | P1 | ✅ 완료 |
| FR-6 | LICENSE 파일 생성 | P1 | ✅ 완료 |

### 2.2 비기능 요구사항

| ID | 요구사항 | 기준 |
|----|---------|------|
| NFR-1 | 기존 기능 정상 동작 | Docker 배포 테스트 통과 |
| NFR-2 | 사용자 경험 유지 | placeholder로 예제 URL 표시 |

---

## 3. 기술 설계

### 3.1 수정 대상 파일

```
수정 파일:
├── docker-compose.yml                    # IP 제거, override 패턴 적용
├── services/web/src/app/benchmark/new/page.tsx    # 기본값 빈 문자열
├── services/web/src/app/benchmark/[id]/analysis/page.tsx  # 기본값 빈 문자열
├── services/web/src/app/recommend/page.tsx        # 기본값 빈 문자열
├── services/api/src/llm_loadtest_api/routers/benchmarks.py  # 기본값 빈 문자열
└── README.md                             # 보안 가이드 섹션 추가

신규 파일:
├── docker-compose.override.yml.example   # 로컬 개발용 설정 예제
├── LICENSE                               # 오픈소스 라이선스 (MIT)
└── .gitignore                            # docker-compose.override.yml 추가
```

### 3.2 변경 상세

#### 3.2.1 docker-compose.yml
- 사내 IP `192.168.123.35` 제거
- `docker-compose.override.yml` 사용 안내 주석 추가

#### 3.2.2 docker-compose.override.yml.example
- 로컬 개발용 extra_hosts 설정 예제 제공
- `host-gateway` 또는 실제 IP 사용 안내

#### 3.2.3 Web UI 기본값 변경
- `benchmark/new/page.tsx`: server_url 기본값 빈 문자열
- `benchmark/[id]/analysis/page.tsx`: 폴백 URL 빈 문자열
- `recommend/page.tsx`: server_url 기본값 빈 문자열
- placeholder는 `http://your-vllm-server:8000`으로 표시

#### 3.2.4 API 기본값 변경
- `benchmarks.py`: server_url Query 기본값 빈 문자열

#### 3.2.5 README.md 보안 가이드
- API Authentication (Optional) 섹션
- CORS Configuration 섹션
- Docker Network (Local Development) 섹션

#### 3.2.6 LICENSE
- MIT License 적용

---

## 4. 구현 결과

| Phase | 작업 | 파일 | 상태 |
|-------|-----|------|------|
| 1 | docker-compose.yml에서 IP 제거 | docker-compose.yml | ✅ |
| 2 | docker-compose.override.yml.example 생성 | 신규 파일 | ✅ |
| 3 | .gitignore에 override 파일 추가 | .gitignore | ✅ |
| 4 | Web UI 기본값 빈 문자열로 변경 | 3개 TSX 파일 | ✅ |
| 5 | API 기본값 빈 문자열로 변경 | benchmarks.py | ✅ |
| 6 | README 보안 가이드 섹션 추가 | README.md | ✅ |
| 7 | LICENSE 파일 생성 | LICENSE | ✅ |

---

## 5. 성공 기준

- [x] docker-compose.yml에 사내 IP(192.168.123.35) 없음
- [x] Web UI에서 server_url 기본값이 빈 문자열
- [x] API에서 server_url 기본값이 빈 문자열
- [x] docker-compose.override.yml.example 존재
- [x] .gitignore에 docker-compose.override.yml 포함
- [x] README에 Security Configuration 섹션 존재
- [x] LICENSE 파일 존재
- [ ] `docker-compose up` 정상 동작 (검증 필요)

---

## 6. 검증 계획

### 6.1 보안 검증
```bash
# 민감 정보 검색
grep -r "192.168" . --include="*.yml" --include="*.yaml"
grep -r "host.docker.internal:8000" . --include="*.tsx" --include="*.py"
```

### 6.2 기능 검증
```bash
# Docker 빌드 및 실행
docker-compose build
docker-compose up -d

# 웹 UI 접속 확인
curl http://localhost:5050

# API 상태 확인
curl http://localhost:8085/health
```

### 6.3 Git 커밋 전 체크리스트
- [ ] `git diff` 로 변경사항 확인
- [ ] `.env` 파일이 추적되지 않음 확인
- [ ] `docker-compose.override.yml`이 추적되지 않음 확인
