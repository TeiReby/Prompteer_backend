> 본 저장소는 팀 프로젝트 **'Prompteer'**의 백엔드 코드베이스 중, 저의 포트폴리오 및 소스코드 열람을 위해 공개용으로 아카이빙한 저장소입니다.  
> 팀 협업 프로젝트 특성상 팀원의 기본 CRUD 및 라우터 코드가 함께 포함되어 있으며, 제가 구현한 영역은 다음과 같습니다.

* **AI 연동 파이프라인 구축 (`app/utils/genai/`)**
  - Google Gemini API 연동 및 `google.genai` SDK 클라이언트 마이그레이션
  - 코드(PS)·이미지·비디오 챌린지 유형별 프롬프트 엔지니어링 및 AI 응답 처리
* **인증 / 보안 및 사용자 라이프사이클 (`app/routers/user.py`, `app/crud/user.py`)**
  - FastAPI `OAuth2PasswordBearer` 기반 토큰 인증 및 비밀번호 검증 API 구현
  - 회원 탈퇴 시 데이터 보존 및 무결성을 위한 `Soft Delete` 로직 설계
* **도메인 단위 아키텍처 리팩토링 (`app/models/`, `app/routers/challenge.py`)**
  - 단일 모델/스키마 결합도 해소 및 도메인 단위(`challenge`, `post`, `share`, `user`) 패키지 분리
  - `completed-challenges` API의 챌린지 유형별 엔드포인트 분리 및 응답 페이로드 최적화
* **테스트 자동화 및 인프라 트러블슈팅 (`tests/`, Nginx, Docker)**
  - `pytest` 기반 챌린지 시나리오 및 엣지 케이스 테스트 코드 작성 (`test_challenge_scenario.py`)
  - Nginx 리버스 프록시 연동 시 정적 미디어 중복 경로 버그 디버깅 및 경로 정규화

uv 설치
curl -LsSf https://astral.sh/uv/install.sh | sh 

데모 run하는 방법
1. 기존 *.db 파일 삭제

2. .env 파일 만들어서 입력
DATABASE_URL=sqlite:///./run.db
GEMINI_API_KEY="google cloud에서 발급받은 키"
SECRET_KEY="openssl rand -base64 32 명령어로 생성한 키"

3. utils/sandbox/로 이동하여 dockerfile 기반 이미지 생성(당연히 docker cli가 있어야겠죠)
docker build -t python-with-time .

4. terminal에 다음 커맨드 입력
uv run uvicorn app.main:app --port 8000 --reload

5. uv run python3 initializer/init.py으로 초기 데이터 생성

6. http://127.0.0.1:8000/docs 에서 문서 조회되는지 확인

pytest 명령어
mocking test
uv run pytest
non-mocking test (비용 발생 주의)
uv run pytest --run-gemini-api

해커톤 이후 TODO
1. SSO 로그인 구현 OR clerk api 활용
2. int id에서 uuid 도입
3. sqlite에서 aws rds로 확장
4. alembic 도입
5. Redis dram 캐싱 설정

### 도움 되는 명령어들

import 관계 정리 명령어
uv run ruff check --fix --extend-select I .

심심할때 해보기
git ls-files | xargs wc -l
git ls-files | xargs wc -c
