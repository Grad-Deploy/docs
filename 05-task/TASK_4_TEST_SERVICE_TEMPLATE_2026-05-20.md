# Task 4. Mini Board 테스트 서비스 템플릿

담당자: 조원 4  
목표: Grad-Deploy 사용자가 별도 코드를 준비하지 않아도, 우리 툴에서 바로 Mini Board 테스트 서비스(frontend + Node.js backend + PostgreSQL DB)를 생성하고 배포 검증할 수 있게 한다.

---

## 1. 배경

현재 Grad-Deploy는 서비스 타입을 선택하고 YAML을 생성할 수 있지만, 발표와 실제 테스트에는 “바로 배포해서 눈으로 볼 수 있는 샘플 앱”이 필요하다. Mini Board는 게시글 작성/조회만 제공하는 작은 게시판 앱으로, 프론트엔드, 백엔드, 데이터베이스가 함께 동작해야 한다.

이 테스트 서비스는 GitOps 자동 배포, 환경변수 전파, Service/Ingress, Secret/ConfigMap, Guardrail의 가치를 한 번에 보여주는 시연용 기준 앱이다.

---

## 2. 담당 범위

### 2.1 Mini Board 서비스 구성 확정

최종 구성:

```text
frontend-svc
  type: react-nginx 또는 nginx
  역할: 브라우저에서 접속 가능한 게시판 화면

backend-svc
  type: node
  역할: /health, /api/db-check, /api/posts 제공

postgres-svc
  type: postgres
  역할: posts 테이블 저장
```

MVP에서는 Node.js + PostgreSQL 조합을 기준으로 한다. Spring/MySQL 등 다른 조합은 확장 과제로 둔다.

### 2.2 Mini Board 기능 범위

필수 기능:

```text
GET /health
  backend 생존 확인

GET /api/db-check
  PostgreSQL 연결 확인

GET /api/posts
  게시글 목록 조회

POST /api/posts
  게시글 작성
```

선택 기능:

```text
DELETE /api/posts/:id
  게시글 삭제
```

삭제 기능은 시간이 남을 때만 구현한다. MVP 필수는 게시글 작성과 목록 조회다.

### 2.3 기본 샘플 앱 제공 방식

권장 MVP 방향:

- Grad-Deploy가 repo에 Mini Board용 최소 파일을 생성한다.
- backend는 Node.js Express 기반으로 생성한다.
- backend는 `/health`, `/api/db-check`, `/api/posts`를 제공한다.
- frontend는 backend API 호출 결과를 화면에 표시한다.
- DB는 PostgreSQL 공식 이미지를 사용한다.
- 게시글은 PostgreSQL `posts` 테이블에 저장한다.

### 2.4 PostgreSQL 테이블

테이블 예시:

```sql
CREATE TABLE IF NOT EXISTS posts (
  id SERIAL PRIMARY KEY,
  title TEXT NOT NULL,
  content TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

초기화 방식은 둘 중 하나로 결정한다.

1. backend 시작 시 `CREATE TABLE IF NOT EXISTS` 실행
2. PostgreSQL init SQL 파일 생성

MVP에서는 backend 시작 시 테이블을 생성하는 방식이 단순하다.

### 2.5 환경변수 자동 연결 검증

- frontend → backend API URL 전달
- backend → PostgreSQL host/port/database/user/password 전달
- Secret과 ConfigMap 분리 확인
- DB password가 Git에 평문 저장되지 않는지 확인
- PostgreSQL은 외부 URL/Ingress로 직접 노출하지 않도록 확인

### 2.6 배포 검증 시나리오

최소 시나리오:

1. Grad-Deploy에서 “Mini Board 테스트 서비스 추가” 클릭
2. `frontend-svc`, `backend-svc`, `postgres-svc` 3개 서비스 자동 생성
3. Guardrail 통과
4. GitHub push
5. GitHub Actions 이미지 빌드
6. Argo CD ApplicationSet이 Application 생성
7. Pod Ready
8. 외부 URL 접속
9. frontend 화면에서 Backend/Database 상태 확인
10. 게시글 작성
11. 새로고침 후 게시글이 DB에 저장되어 남아 있는지 확인

---

## 3. 구현/정리할 산출물

### UI

- “Mini Board 테스트 서비스 추가” 버튼 또는 템플릿 선택
- frontend/backend/postgres 서비스가 한 번에 추가되는 preset
- 생성 후 의존성 DAG에서 연결 관계 표시
- Mini Board frontend 화면:
  - Backend 상태
  - Database 연결 상태
  - 게시글 제목 입력
  - 게시글 내용 입력
  - 작성 버튼
  - 게시글 목록

### 생성 파일

예상 생성 파일:

```text
Dockerfile.frontend-svc
Dockerfile.backend-svc
sample-apps/frontend/
sample-apps/backend/
k8s/projects/<proj>/services/frontend-svc/
k8s/projects/<proj>/services/backend-svc/
k8s/projects/<proj>/services/postgres-svc/
```

시간이 부족하면 `sample-apps/` 없이 Dockerfile에서 최소 앱 파일을 생성하는 방식도 가능하다.

### API

backend 최소 endpoint:

```text
GET /health
GET /api/db-check
GET /api/posts
POST /api/posts
```

MVP 필수는 `/health`, `/api/db-check`, `/api/posts` GET/POST다.

---

## 4. 완료 기준

- 사용자가 한 번의 preset 추가로 `frontend-svc`, `backend-svc`, `postgres-svc`를 만들 수 있다.
- 생성된 서비스들이 환경변수로 서로 연결된다.
- backend `/health`가 200 OK를 반환한다.
- backend `/api/db-check`가 PostgreSQL 연결 성공을 반환한다.
- frontend가 backend API 결과를 화면에 표시한다.
- frontend에서 게시글을 작성할 수 있다.
- 새로고침 후에도 게시글이 DB에 저장되어 목록에 남아 있다.
- Argo CD까지 배포한 뒤 외부 URL에서 실제 Mini Board 동작을 확인할 수 있다.

---

## 5. 우선순위

1. Mini Board preset으로 서비스 3개 자동 추가
2. Node.js backend `/health`, `/api/db-check`, `/api/posts` 제공
3. PostgreSQL `posts` 테이블 자동 초기화
4. frontend 게시글 목록/작성 화면 구현
5. 외부 URL과 DemoDay 화면 연결

---

## 6. 리스크

- 샘플 앱 코드까지 생성하면 파일 생성기가 복잡해질 수 있다.
- DB 초기화가 늦으면 backend가 초반에 CrashLoopBackOff 될 수 있다.
- frontend가 backend 주소를 잘못 받으면 화면은 뜨지만 API 호출이 실패한다.
- 외부 URL path routing을 쓰면 frontend의 API base URL 처리가 까다로울 수 있다.

---

## 7. Jira Story 제안

Task 4는 다음 Story로 나눠 작업한다.

```text
[Task4] Mini Board 테스트 서비스 preset 추가
[Task4] Node.js backend 게시판 API 구현
[Task4] PostgreSQL posts 테이블 초기화
[Task4] frontend 게시글 목록/작성 화면 구현
[Task4] 환경변수/Secret/ConfigMap 연결 검증
[Task4] Argo CD 실제 배포 테스트
```

앞의 4개 Story가 핵심 구현이고, 뒤의 2개 Story는 통합 검증 성격이다.
