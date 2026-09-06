# Prompteer Backend

> 생성형 AI 프롬프팅 인터랙티브 챌린지 플랫폼 **'Prompteer'**의 백엔드 API 서버 저장소입니다.

---

### 📌 프로젝트 및 기여 영역 안내
> 본 저장소는 팀 프로젝트 **'Prompteer'**의 백엔드 코드베이스 중, 포트폴리오 및 소스코드 열람을 위해 공개용으로 아카이빙한 저장소입니다.  
> 협업 프로젝트 특성상 팀원의 기본 CRUD 및 라우터 코드가 포함되어 있으며, 제가 담당하여 직접 설계·구현한 영역은 다음과 같습니다.

* **AI 연동 파이프라인 구축 (`app/utils/genai/`)**
  - Google Gemini API 연동 및 `google.genai` SDK 클라이언트 마이그레이션
  - 코드(PS)·이미지·비디오 챌린지 유형별 프롬프트 엔지니어링 및 AI 응답 처리
* **인증 / 보안 및 사용자 라이프사이클 (`app/routers/user.py`, `app/crud/user.py`)**
  - FastAPI `OAuth2PasswordBearer` 기반 토큰 인증 및 비밀번호 검증 API 구현
  - 회원 탈퇴 시 데이터 무결성 보존을 위한 `Soft Delete` 로직 설계
* **도메인 단위 아키텍처 리팩토링 (`app/models/`, `app/routers/challenge.py`)**
  - 단일 모델/스키마 결합도 해소 및 도메인 단위(`challenge`, `post`, `share`, `user`) 패키지 분리
  - `completed-challenges` API의 챌린지 유형별 엔드포인트 분리 및 응답 페이로드 최적화
* **테스트 자동화 및 인프라 트러블슈팅 (`tests/`, Nginx, Docker)**
  - `pytest` 기반 챌린지 시나리오 및 엣지 케이스 테스트 코드 작성 (`test_challenge_scenario.py`)
  - Nginx 리버스 프록시 연동 시 정적 미디어 중복 경로 버그 디버깅 및 경로 정규화

---

## 🚀 실행 가이드 (Getting Started)

### 1. 의존성 관리 도구 (`uv`) 설치
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### 2. 로컬 실행 환경 구축
1. **기존 DB 파일 초기화** (재실행 시)
   ```bash
   rm -f run.db
   ```

2. **환경변수 설정 (`.env`)**
   ```env
   DATABASE_URL=sqlite:///./run.db
   GEMINI_API_KEY="google cloud에서 발급받은 키"
   SECRET_KEY="openssl rand -base64 32 명령어로 생성한 키"
   ```

3. **코드 실행 샌드박스 Docker 이미지 빌드** *(사전 요구사항: Docker 환경)*
   ```bash
   cd utils/sandbox
   docker build -t python-with-time .
   cd ../..
   ```

4. **API 서버 실행**
   ```bash
   uv run uvicorn app.main:app --port 8000 --reload
   ```

5. **초기 시드 데이터 생성**
   ```bash
   uv run python3 initializer/init.py
   ```

6. **API 문서 확인**
   - Swagger UI: `http://127.0.0.1:8000/docs`

---

## 🧪 테스트 실행 (Testing)

```bash
# 1. Mocking 기반 단위/통합 테스트 (API 비용 미발생)
uv run pytest

# 2. Live Gemini API 연동 테스트 (API 호출 비용 발생 주의)
uv run pytest --run-gemini-api
```

---

## 🛠 코드 품질 및 관리 도구

```bash
# Import 정렬 및 Lint 검사 (Ruff)
uv run ruff check --fix --extend-select I .

# 코드베이스 메트릭 측정 (라인 수 / 바이트 수)
git ls-files | xargs wc -l
git ls-files | xargs wc -c
```

---

## 🗺 아키텍처 확장 계획 (Future Roadmap)
1. **인증 고도화**: SSO 소셜 로그인 연동 및 Clerk Auth 솔루션 도입 검토
2. **식별자 체계 개선**: Auto-increment ID에서 분산 환경 대응을 위한 UUIDv7 전환
3. **데이터베이스 확장**: 로컬 SQLite에서 고가용성 클라우드 RDBMS(AWS RDS PostgreSQL/MySQL) 전환
4. **스키마 버전 관리**: Alembic을 활용한 DB 마이그레이션 자동화 파이프라인 구축
5. **캐싱 계층 구축**: Redis 인메모리 캐시 도입을 통한 반복 API 조회 레이턴시 최적화
