# Branch and PR Process Guide

이 문서는 Grad-Deploy v2.0 최종 작업을 위한 Git branch, push, Pull Request(PR), Jira 상태 관리 규칙이다.

---

## 기본 원칙

작업은 각자 맡은 Task 브랜치에서 진행한다.

작업 중에는 자기 브랜치에 계속 push해도 된다.

단, `main`에는 직접 push하지 않는다.

---

## 1. 작업 브랜치 만들기

먼저 최신 `main`을 받은 뒤, 자기 Task 브랜치를 만든다.

```bash
git checkout main
git pull origin main
git checkout -b feature/task4-test-template
```

Task별 브랜치명 예시:

```text
feature/task1-argocd-ops
feature/task2-demoday-url
feature/task3-cost-guardrail
feature/task4-test-template
```

---

## 2. 작업 후 커밋하기

작업이 끝나거나 중간 저장이 필요하면 커밋한다.

커밋 메시지에는 Jira 티켓 번호를 넣는다.

```bash
git add .
git commit -m "SCRUM-41 update Mini Board test template"
```

예시:

```text
SCRUM-12 update Argo CD ops runbook
SCRUM-21 update DemoDay external URL flow
SCRUM-31 update cost and guardrail checks
SCRUM-41 update Mini Board test template
```

---

## 3. 자기 브랜치에 Push하기

처음 push할 때:

```bash
git push -u origin feature/task4-test-template
```

이미 한 번 push한 브랜치라면 다음부터는 아래처럼 해도 된다.

```bash
git push
```

---

## 4. PR 만들기

Task 작업이 어느 정도 끝나면 GitHub에서 PR을 만든다.

1. GitHub 저장소로 이동
2. `Pull requests` 탭 클릭
3. `New pull request` 클릭
4. 아래처럼 설정

```text
base: integration/mvp-final
compare: feature/task4-test-template
```

`integration/mvp-final` 브랜치를 쓰지 않는 경우에는 base를 `main`으로 둔다.

```text
base: main
compare: feature/task4-test-template
```

용어 설명:

```text
base    = 내 작업이 합쳐질 대상 브랜치
compare = 내가 작업한 브랜치
```

---

## 5. PR 제목 작성

PR 제목은 아래 형식으로 작성한다.

```text
[Task4] Complete Mini Board test service template
```

Jira 티켓 번호를 같이 넣어도 된다.

```text
[SCRUM-41][Task4] Complete Mini Board test service template
```

Task별 PR 제목 예시:

```text
[Task1] Complete Argo CD operating environment verification
[Task2] Complete DemoDay and external URL flow
[Task3] Complete cost, credit, and Guardrail finish work
[Task4] Complete Mini Board test service template
```

---

## 6. PR 설명 작성

PR 설명은 아래 템플릿을 사용한다.

```md
## Jira

- SCRUM-41
- SCRUM-42
- SCRUM-43

## Summary

- 

## Verification

- [ ] `npm run build`
- [ ] Manual UI check
- [ ] Argo CD / Kubernetes verification if relevant

## Notes / Risks

- 
```

Task별 Verification 예시:

```md
## Verification

- [ ] `npm run build`
- [ ] Task-specific UI checked
- [ ] Related Jira Story acceptance criteria checked
- [ ] Screenshots or command output attached if needed
```

---

## 7. Jira 상태 변경

PR을 만들면 Jira에서 해당 Story 상태를 `Review`로 옮긴다.

팀장이 리뷰하고 merge한 뒤에는 `Done`으로 옮긴다.

```text
To Do -> In Progress -> Review -> Done
```

상태 기준:

```text
To Do       = 아직 시작 전
In Progress = 작업 중
Review      = PR 생성 후 리뷰 대기
Done        = 리뷰 완료, merge 완료, 검증 완료
```

---

## 핵심 규칙

- `main` 직접 push 금지
- 작업은 자기 Task 브랜치에서 진행
- 작업 중 자기 브랜치에는 계속 push 가능
- 커밋 메시지에 Jira 티켓 번호 포함
- Task가 어느 정도 끝나면 PR 생성
- PR의 base는 `integration/mvp-final` 권장
- `integration/mvp-final`을 쓰지 않으면 base는 `main`
- PR 생성 후 Jira 상태를 `Review`로 변경
- 팀장 리뷰 후 merge

---

## 전체 흐름 요약

```text
main 최신화
-> 자기 Task 브랜치 생성
-> 작업
-> 커밋
-> 자기 브랜치에 push
-> GitHub에서 PR 생성
-> Jira 상태 Review로 변경
-> 팀장 리뷰
-> merge
-> Jira 상태 Done으로 변경
```
