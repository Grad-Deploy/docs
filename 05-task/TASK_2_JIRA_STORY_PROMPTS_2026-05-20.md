# Task 2 Jira Story Prompts

Branch: `feature/task2-demoday-url`  
Replace `<JIRA-KEY>` with the real Jira key, for example `SCRUM-21`.

## How to Start a New Agent Thread

새 AI 작업 창에서는 먼저 아래 프롬프트를 보낸다. 이 단계에서는 코드 파일을 열지 않고, 어떤 파일을 만질지 먼저 보고받는다.

```text
Read only:
- TASK_2_DEMODAY_EXTERNAL_URL_2026-05-20.md
- TASK_2_JIRA_STORY_PROMPTS_2026-05-20.md

Do not open other files unless I ask.
Do not edit files yet.

You are working on Grad-Deploy v2.0.
Main branch is the priority reference.
Jira issue: <JIRA-KEY>
Story: <STORY_TITLE>

Before starting:
1. Tell me which files you expect to touch.
2. Tell me what you will do in each file.
3. Tell me which UI or manual checks are needed.
4. Tell me which verification command you plan to run.

Wait for my confirmation, then begin.

Project root:
your local Grad-Deploy project root

Run verification from project root:
npm run build

Do not touch:
- Mini Board backend/frontend implementation
- PostgreSQL schema/init logic
- Cost/Guardrail engine logic
- OAuth or Argo CD SSO/RBAC code
- package files unless absolutely required

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

## TASK-2.1: Decide External URL Exposure Method

```md
# <JIRA-KEY>: Decide external URL exposure method

## Context
The demo needs browser-accessible URLs for deployed services.

## Goal
Choose the MVP external access method.

## Scope
- Compare zrok, cloudflared, and local port-forward/NodePort.
- Pick the final demo method.
- Document setup and recovery steps.

## Do not touch
- Mini Board app implementation.
- Cost/Guardrail logic.
- Argo CD SSO/RBAC logic.

## Acceptance criteria
- One external URL method is selected.
- The reason and fallback plan are documented.
```

## TASK-2.2: Verify Ingress and Tunnel Access

```md
# <JIRA-KEY>: Verify Ingress and tunnel access

## Context
Frontend and backend services must be reachable from a browser through the chosen external URL method.

## Goal
Verify external access to frontend and backend health endpoint.

## Scope
- Check Ingress Controller availability.
- Check Ingress path routing.
- Connect zrok/cloudflared or chosen tunnel.
- Verify frontend URL and backend `/health`.
- Ensure PostgreSQL is not externally exposed.

## Do not touch
- DB schema or Mini Board API implementation unless required for verification.

## Acceptance criteria
- Frontend is reachable through external URL.
- Backend `/health` is reachable through external URL.
- DB is not exposed externally.
```

## TASK-2.3: Improve DemoDay Status Cards

```md
# <JIRA-KEY>: Improve DemoDay status cards

## Context
DemoDay should clearly show deployment status during presentation.

## Goal
Improve DemoDay cards for GitHub Actions, Argo CD, Pod Ready, and external URL status.

## Scope
- Inspect current DemoMode/DemoDay implementation.
- Separate simulated state from real or manually verified state.
- Add or improve status cards and failure messages.

## Do not touch
- Mini Board backend/frontend implementation.
- Argo CD SSO/RBAC setup.

## Acceptance criteria
- DemoDay screen clearly shows the main deployment states.
- UI builds successfully with `npm run build`.
- Screenshot or manual verification notes are attached.
```

## TASK-2.4: Display Per-Service External URLs

```md
# <JIRA-KEY>: Display per-service external URLs

## Context
Users should see the frontend and backend URLs for deployed services.

## Goal
Display service-level external URLs in the DemoDay or deployment result UI.

## Scope
- Show frontend URL.
- Show backend API URL.
- Do not show a public DB URL.
- Add copy or clickable link behavior if simple.

## Do not touch
- Cost/Guardrail engines.
- SSO/RBAC code.

## Acceptance criteria
- Frontend and backend URLs are visible.
- PostgreSQL is not shown as externally accessible.
- `npm run build` passes.
```

## TASK-2.5: Presentation-Day Recovery Guide

```md
# <JIRA-KEY>: Presentation-day recovery guide

## Context
Tunnel, Ingress, or Argo CD access may fail during presentation.

## Goal
Create a short recovery guide for presentation day.

## Scope
- Tunnel restart command.
- Ingress status checks.
- Argo CD access recovery.
- External URL health check.

## Do not touch
- Product feature code unless a doc link is needed.

## Acceptance criteria
- A teammate can recover the demo URL using the guide.
- Commands are tested or clearly marked as expected.
```

---

## Branch and PR Process

```bash
git checkout main
git pull origin main
git checkout -b feature/task2-demoday-url
```

작업 중에는 자기 Task 브랜치에 계속 push해도 된다.

```bash
git add .
git commit -m "SCRUM-21 update DemoDay external URL flow"
git push -u origin feature/task2-demoday-url
```

Task 2 작업이 어느 정도 끝나면 GitHub에서 PR을 만든다.

```text
Pull requests -> New pull request
base: integration/mvp-final
compare: feature/task2-demoday-url
```

`integration/mvp-final` 브랜치를 쓰지 않으면 base는 `main`으로 둔다.

PR title:

```text
[Task2] Complete DemoDay and external URL flow
```

PR description:

```md
## Jira
- SCRUM-21
- SCRUM-22
- SCRUM-23

## Summary
- 

## Verification
- [ ] External URL method selected
- [ ] Frontend URL checked
- [ ] Backend health URL checked
- [ ] PostgreSQL is not externally exposed
- [ ] npm run build

## Notes / Risks
- 
```
