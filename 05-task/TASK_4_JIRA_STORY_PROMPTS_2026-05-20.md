# Task 4 Jira Story Prompts

Branch: `feature/task4-test-template`  
Replace `<JIRA-KEY>` with the real Jira key, for example `SCRUM-41`.

## How to Start a New Agent Thread

새 AI 작업 창에서는 먼저 아래 프롬프트를 보낸다. 이 단계에서는 코드 파일을 열지 않고, 어떤 파일을 만질지 먼저 보고받는다.

```text
Read only:
- TASK_4_TEST_SERVICE_TEMPLATE_2026-05-20.md
- TASK_4_JIRA_STORY_PROMPTS_2026-05-20.md

Do not open other files unless I ask.
Do not edit files yet.

You are working on Grad-Deploy v2.0.
Main branch is the priority reference.
Jira issue: <JIRA-KEY>
Story: <STORY_TITLE>

Before starting:
1. Tell me which files you expect to touch.
2. Tell me what you will do in each file.
3. Tell me how the Mini Board behavior will be verified.
4. Tell me which verification command you plan to run.

Wait for my confirmation, then begin.

Project root:
your local Grad-Deploy project root

Run verification from project root:
npm run build

Mini Board target:
- frontend-svc
- backend-svc
- postgres-svc
- GET /health
- GET /api/db-check
- GET /api/posts
- POST /api/posts

Do not touch:
- Argo CD SSO/RBAC implementation
- DemoDay external URL implementation unless this story explicitly asks for integration
- Cost/Guardrail rules outside Mini Board compatibility
- global UI redesign
- package files unless dependency changes are absolutely required

Keep the change scoped to this Jira story.
```

두 번째 메시지:

```text
confirmed. go.
```

## Common Rules

```md
You are working on Grad-Deploy v2.0.
Main branch is the priority reference. Do not redesign the whole architecture.
Work only on this Jira story.
Avoid unrelated refactors, formatting churn, or touching unrelated files.
Before editing, inspect the existing code patterns and follow them.
After implementation, run `npm run build` if code changed.
Summarize changed files, verification results, and remaining risks.
Jira issue: <JIRA-KEY>
```

---

## TASK-4.1: Mini Board Preset

```md
# <JIRA-KEY>: Add Mini Board test service preset

## Context
Task 4 provides a generated Mini Board app: frontend + Node.js backend + PostgreSQL.

## Goal
Add a preset that creates `frontend-svc`, `backend-svc`, and `postgres-svc`.

## Scope
- Define the Mini Board preset.
- Set default service names, ports, resources, and dependencies.
- Connect frontend -> backend -> postgres.
- Ensure topology shows the dependency chain.

## Do not touch
- Argo CD SSO/RBAC.
- DemoDay external URL code unless needed for labels.
- Cost/Guardrail rules outside preset compatibility.

## Acceptance criteria
- One action creates all three services.
- Dependencies are configured correctly.
- Guardrail has no fatal error by default.
- `npm run build` passes.
```

## TASK-4.2: Node.js Backend Board API

```md
# <JIRA-KEY>: Implement Mini Board Node.js backend API

## Context
The generated backend service must prove backend and DB connectivity.

## Goal
Implement a generated Node.js backend sample with board APIs.

## Scope
- Generate or include Express backend sample code.
- Implement `GET /health`.
- Implement `GET /api/db-check`.
- Implement `GET /api/posts`.
- Implement `POST /api/posts`.
- Use PostgreSQL connection settings from environment variables.

## Do not touch
- SSO/RBAC code.
- DemoDay UI.
- Cost/Guardrail engines.

## Acceptance criteria
- Backend container starts.
- `/health` returns 200.
- `/api/db-check` reports DB connection status.
- GET/POST `/api/posts` work.
- `npm run build` passes.
```

## TASK-4.3: PostgreSQL Posts Table Initialization

```md
# <JIRA-KEY>: Initialize PostgreSQL posts table

## Context
Mini Board needs persistent posts stored in PostgreSQL.

## Goal
Ensure the `posts` table is automatically created.

## Scope
- Use PostgreSQL official image.
- Define database name, user, and password variables.
- Create `posts(id, title, content, created_at)`.
- Prefer backend startup `CREATE TABLE IF NOT EXISTS` unless an init SQL file is cleaner.
- Ensure DB is internal only.

## Do not touch
- External URL exposure for DB.
- Argo CD SSO/RBAC.

## Acceptance criteria
- PostgreSQL pod starts.
- `posts` table exists automatically.
- Backend connects through Kubernetes service DNS.
- DB password is not committed as plaintext.
```

## TASK-4.4: Frontend Board List and Create Form

```md
# <JIRA-KEY>: Implement Mini Board frontend

## Context
The frontend should prove that external users can reach the deployed app and use backend/DB.

## Goal
Implement the Mini Board UI for listing and creating posts.

## Scope
- Show backend status.
- Show DB connection status.
- Show post list from `GET /api/posts`.
- Add title/content form.
- Submit posts to `POST /api/posts`.
- Refresh list after create.
- Show API failure messages.

## Do not touch
- Argo CD SSO/RBAC.
- Cost/Guardrail engines.

## Acceptance criteria
- Frontend loads in browser.
- User can create a post.
- Post remains after refresh.
- API errors are visible.
- `npm run build` passes.
```

## TASK-4.5: Env, Secret, and ConfigMap Verification

```md
# <JIRA-KEY>: Verify Mini Board env, Secret, and ConfigMap wiring

## Context
Mini Board must demonstrate Grad-Deploy's environment propagation and secret separation.

## Goal
Verify frontend/backend/postgres environment variables are generated correctly.

## Scope
- Check frontend backend API URL.
- Check backend DB host, port, name, user, password.
- Check Secret vs ConfigMap separation.
- Ensure sensitive values are not committed in plaintext.

## Do not touch
- SSO/RBAC.
- DemoDay external URL implementation.

## Acceptance criteria
- Generated manifests contain correct env wiring.
- Sensitive values are separated into Secret flow.
- Backend can connect to PostgreSQL using generated values.
```

## TASK-4.6: Mini Board Argo CD Deployment Test

```md
# <JIRA-KEY>: Run Mini Board Argo CD deployment test

## Context
The final proof is deploying Mini Board through GitHub Actions and Argo CD.

## Goal
Verify the Mini Board preset works end-to-end in the demo cluster.

## Scope
- Generate Mini Board services.
- Push to GitHub.
- Verify GitHub Actions image builds.
- Verify Argo CD ApplicationSet creates Applications.
- Verify frontend/backend/postgres pods are Ready.
- Verify external URL works.
- Create a post and confirm it persists after refresh.

## Do not touch
- New feature implementation unless fixing a blocker discovered during this test.

## Acceptance criteria
- Argo CD shows Mini Board Applications synced/healthy or pods Ready.
- External frontend URL works.
- Post creation and persistence are verified.
- Screenshots or command outputs are attached.
```

---

## Branch and PR Process

```bash
git checkout main
git pull origin main
git checkout -b feature/task4-test-template
```

작업 중에는 자기 Task 브랜치에 계속 push해도 된다.

```bash
git add .
git commit -m "SCRUM-41 update Mini Board test template"
git push -u origin feature/task4-test-template
```

Task 4 작업이 어느 정도 끝나면 GitHub에서 PR을 만든다.

```text
Pull requests -> New pull request
base: integration/mvp-final
compare: feature/task4-test-template
```

`integration/mvp-final` 브랜치를 쓰지 않으면 base는 `main`으로 둔다.

PR title:

```text
[Task4] Complete Mini Board test service template
```

PR description:

```md
## Jira
- SCRUM-41
- SCRUM-42
- SCRUM-43

## Summary
- 

## Verification
- [ ] Mini Board preset creates frontend/backend/postgres
- [ ] Backend health and DB check verified
- [ ] Frontend post create/list verified
- [ ] npm run build
- [ ] Argo CD deployment test if available

## Notes / Risks
- 
```
