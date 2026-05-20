# Task 1. Argo CD 운영 환경 및 관리자 검증

담당자: 조원 1  
목표: Argo CD까지 배포되는 흐름은 이미 구현된 것으로 보고, 별도 관리자 페이지 없이 운영자가 Argo CD admin 대시보드에서 전체 상태를 안정적으로 확인할 수 있게 마무리한다.

---

## 1. 배경

현재 Grad-Deploy는 GitHub push, GitHub Actions, Argo CD 연동, ApplicationSet 기반 Application 생성 흐름까지 큰 틀은 잡혀 있다. 이제 남은 핵심은 “발표/시연 때 운영자가 믿고 볼 수 있는 Argo CD 운영 환경”을 완성하는 것이다.

별도 Grad-Deploy 관리자 페이지는 만들지 않는다. 운영자는 Argo CD `admin` 계정으로 Argo CD 대시보드에 접속해서 전체 Application, ApplicationSet, AppProject, sync/health 상태를 확인한다. 새 클러스터를 추가로 만드는 작업이 아니라, 이미 사용 중인 VM/minikube 또는 최종 시연용 Kubernetes 클러스터를 기준 환경으로 확정하고 검증하는 작업이다.

---

## 2. 담당 범위

### 2.1 Argo CD 운영 클러스터 기준 확정

- 기존 VM/minikube를 그대로 쓸지, kind 또는 다른 시연 클러스터로 고정할지 결정
- 결정된 클러스터에 Argo CD가 설치되어 있고 접근 가능한지 확인
- Argo CD namespace, server, repo-server, application-controller 정상 구동 확인
- metrics-server 설치 여부 확인
- Ingress Controller 설치 여부 확인
- GHCR private image pull secret 적용 방식 확인

### 2.2 Argo CD admin 운영 절차 정리

- admin 초기 비밀번호 확인 명령 정리
- Argo CD 접속 URL 확보 절차 정리
- admin 로그인 후 확인할 메뉴 정리
  - Applications
  - ApplicationSets
  - Projects
  - Repositories
  - Settings

### 2.3 ApplicationSet 운영 검증

- `AppProject`가 먼저 생성되는지 확인
- `ApplicationSet`이 `k8s/projects/<proj>/services/*` 경로를 감시하는지 확인
- 서비스 폴더 추가 시 `<proj>-<service>` Application 자동 생성 확인
- 서비스 폴더 삭제 시 Application 삭제 또는 prune 동작 확인

### 2.4 장애 원인 확인 루틴 작성

- `ImagePullBackOff`
- `Pending`
- `CrashLoopBackOff`
- `OOMKilled`
- `OutOfSync`
- `Degraded`
- `Progressing`

각 상태별로 Argo CD UI에서 어디를 봐야 하는지와 `kubectl` 확인 명령을 정리한다.

---

## 3. 구현/정리할 산출물

### 문서

- Argo CD 운영 클러스터 확인 절차
- Argo CD admin 접속 절차
- ApplicationSet 검증 체크리스트
- 장애 상태별 확인 명령어

### 스크립트 후보

필요하면 다음 스크립트를 추가한다.

```text
scripts/check-admin-cluster.sh
scripts/check-argocd-status.sh
scripts/check-applicationset.sh
```

단, 발표 전 시간이 부족하면 스크립트보다 문서화와 수동 검증 명령 정리를 우선한다.

---

## 4. 완료 기준

- 운영자가 Argo CD admin 계정으로 전체 Application을 확인할 수 있다.
- ApplicationSet이 서비스별 Application을 자동 생성한다.
- 최소 1회 이상 실제 GitHub Actions → GHCR → Argo CD sync → Pod Ready 흐름을 확인한다.
- 실패 상태가 발생했을 때 원인을 Argo CD UI와 `kubectl`로 설명할 수 있다.
- 발표 당일 재현 가능한 관리자 클러스터 체크리스트가 있다.

---

## 5. 우선순위

1. Argo CD admin 접속과 전체 Application 확인
2. ApplicationSet 자동 생성 검증
3. Pod Ready까지 실제 배포 루프 검증
4. 장애 상태별 확인 루틴 정리
5. metrics-server, Ingress Controller 등 부가 환경 정리

---

## 6. 리스크

- VM/minikube 리소스가 부족하면 정상 매니페스트도 Pending 상태가 될 수 있다.
- GHCR package가 private이면 imagePullSecret 누락으로 ImagePullBackOff가 발생한다.
- metrics-server가 없으면 HPA 상태 확인이 제한된다.
- Argo CD URL이 port-forward/tunnel에 의존하면 발표 중 URL이 바뀔 수 있다.
