# Task 3 Jira Story Prompts

Branch: `feature/task3-cost-guardrail`  
Replace `<JIRA-KEY>` with the real Jira key, for example `SCRUM-31`.

## How to Start a New Agent Thread

새 AI 작업 창에서는 먼저 아래 프롬프트를 보낸다. 이 단계에서는 코드 파일을 열지 않고, 어떤 파일을 만질지 먼저 보고받는다.

```text
Read only:
- TASK_3_COST_GUARDRAIL_FINISH_2026-05-20.md
- TASK_3_JIRA_STORY_PROMPTS_2026-05-20.md

Do not open other files unless I ask.
Do not edit files yet.

You are working on Grad-Deploy v2.0.
Main branch is the priority reference.
Jira issue: <JIRA-KEY>
Story: <STORY_TITLE>

Before starting:
1. Tell me which files you expect to touch.
2. Tell me what you will do in each file.
3. Tell me which Guardrail or cost behavior you will verify.
4. Tell me which verification command you plan to run.

Wait for my confirmation, then begin.

Project root:
your local Grad-Deploy project root

Run verification from project root:
npm run build

Do not touch:
- Mini Board preset or sample app code
- DemoDay external URL implementation
- Argo CD SSO/RBAC code
- GitHub push/deploy flow unless this story explicitly requires it
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

## TASK-3.1: Cost and Credit Calculation Baseline

```md
# <JIRA-KEY>: Cost and credit calculation baseline

## Context
Grad-Deploy must explain cost and credit risk for demo deployments.

## Goal
Clarify and improve the cost/credit calculation baseline.

## Scope
- Inspect current CreditPanel and cost logic.
- Document CPU, memory, replica, and D-day assumptions.
- Improve labels or warnings if needed.

## Do not touch
- Argo CD SSO/RBAC.
- Mini Board sample app.

## Acceptance criteria
- Cost assumptions are documented.
- Cost UI remains functional.
- `npm run build` passes if code changed.
```

## TASK-3.2: Verify Cluster Advisor Resource Blocking

```md
# <JIRA-KEY>: Verify Cluster Advisor resource blocking

## Context
Cluster capacity should prevent deployments that exceed available CPU or memory.

## Goal
Verify Cluster Advisor output is reflected in Guardrail errors.

## Scope
- Check CPU request total vs allocatable CPU.
- Check memory request total vs allocatable memory.
- Verify Guardrail error messages.
- Record minikube/VM verification.

## Do not touch
- DemoDay external URL code.
- Mini Board implementation.

## Acceptance criteria
- Resource overflow creates Guardrail Error.
- Normal resource settings do not block deployment.
- Verification result is recorded.
```

## TASK-3.3: Add imagePullSecrets, Ingress, and Selector Guardrail Checks

```md
# <JIRA-KEY>: Add missing high-value Guardrail checks

## Context
Common Kubernetes failures should be caught before deployment.

## Goal
Add or verify Guardrail checks for imagePullSecrets, Ingress service mismatch, and selector mismatch.

## Scope
- Check private registry without imagePullSecrets.
- Check Ingress serviceName mismatch.
- Check selector vs matchLabels mismatch.
- Add clear error or warning messages.

## Do not touch
- SSO/RBAC code.
- Mini Board app behavior.

## Acceptance criteria
- Each rule can be triggered with a sample bad config.
- Messages explain how to fix the issue.
- `npm run build` passes.
```

## TASK-3.4: Improve Cost and Guardrail UI

```md
# <JIRA-KEY>: Improve Cost and Guardrail UI

## Context
The presentation should make Grad-Deploy's safety value easy to understand.

## Goal
Improve cost and Guardrail result display without redesigning the whole UI.

## Scope
- Improve Error/Warning/Info summaries.
- Improve cost risk labels.
- Make resource overflow messages easy to read.

## Do not touch
- DemoDay external URL implementation.
- Mini Board implementation.

## Acceptance criteria
- Main risks are visible at a glance.
- Text does not overflow or break the UI.
- `npm run build` passes.
```

## TASK-3.5: Presentation Failure Scenarios

```md
# <JIRA-KEY>: Write presentation failure scenarios

## Context
The team needs clear examples of failures Grad-Deploy prevents.

## Goal
Write concise demo scenarios for Kubernetes safety failures.

## Scope
- CPU Pending scenario.
- ImagePullBackOff scenario.
- HPA metrics-server missing scenario.
- Probe missing scenario.
- Map each scenario to Grad-Deploy Guardrail or Advisor value.

## Do not touch
- Product code unless adding docs only.

## Acceptance criteria
- At least three scenarios are documented.
- Each scenario includes cause, Grad-Deploy detection, and demo explanation.
```

---

## Branch and PR Process

```bash
git checkout main
git pull origin main
git checkout -b feature/task3-cost-guardrail
```

작업 중에는 자기 Task 브랜치에 계속 push해도 된다.

```bash
git add .
git commit -m "SCRUM-31 update cost and guardrail checks"
git push -u origin feature/task3-cost-guardrail
```

Task 3 작업이 어느 정도 끝나면 GitHub에서 PR을 만든다.

```text
Pull requests -> New pull request
base: integration/mvp-final
compare: feature/task3-cost-guardrail
```

`integration/mvp-final` 브랜치를 쓰지 않으면 base는 `main`으로 둔다.

PR title:

```text
[Task3] Complete cost, credit, and Guardrail finish work
```

PR description:

```md
## Jira
- SCRUM-31
- SCRUM-32
- SCRUM-33

## Summary
- 

## Verification
- [ ] Cost/credit behavior checked
- [ ] Cluster Advisor resource blocking checked
- [ ] Guardrail checks verified
- [ ] npm run build

## Notes / Risks
- 
```
