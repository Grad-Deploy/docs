# Task 2. 데모데이 대시보드 및 외부 주소 노출

담당자: 조원 2  
목표: 발표 시 사용자가 “배포 결과를 눈으로 확인할 수 있는” 데모데이 화면과 외부 접속 URL 노출 흐름을 완성한다.

---

## 1. 배경

현재 데모데이 화면은 공개 URL과 Pod 상태를 보여주는 방향이지만, 일부는 시뮬레이션 성격이 남아 있다. MVP 완성 단계에서는 실제 Argo CD/Kubernetes 상태와 연결하거나, 최소한 발표자가 신뢰할 수 있는 상태 확인 흐름으로 정리해야 한다.

또한 사용자가 만든 서비스가 클러스터 내부에만 있으면 발표 시 체감이 약하다. 외부 주소를 통해 브라우저에서 프론트엔드와 API를 직접 열 수 있어야 한다.

---

## 2. 담당 범위

### 2.1 데모데이 화면 개선

- 현재 DemoDay/DemoMode 화면의 시뮬레이션 요소 파악
- 실제 배포 상태와 연결 가능한 항목 분리
- 발표용 상태 카드 구성
  - GitHub Actions 상태
  - Argo CD sync 상태
  - Argo CD health 상태
  - Pod Ready 상태
  - 외부 URL 상태

### 2.2 Pod 상태 대시보드 정리

- 서비스별 Pod 상태 표시
- Ready/Running/Pending/Failed 구분
- 실패 상태일 때 간단한 원인 표시
  - ImagePullBackOff
  - Insufficient cpu
  - CrashLoopBackOff
  - Service/Ingress mismatch

### 2.3 외부 주소 노출 방식 확정

우선 MVP 방식은 다음 중 하나로 확정한다.

1. Ingress Controller + zrok reserved share
2. Ingress Controller + cloudflared tunnel
3. NodePort/port-forward 기반 로컬 시연

권장 MVP 방향:

```text
Ingress Controller 1개
zrok reserved share 1개
path routing으로 서비스 구분
```

예시:

```text
https://grad-demo.share.zrok.io/<proj>/frontend
https://grad-demo.share.zrok.io/<proj>/api
```

### 2.4 생성 YAML과 외부 URL 연결

- `Ingress` 생성 규칙 확인
- React/Nginx 프론트 서비스 path 확인
- backend API service path 확인
- DB는 외부 노출하지 않도록 확인
- 외부 URL을 DemoDay 화면에 표시

---

## 3. 구현/정리할 산출물

### UI

- DemoDay 화면에서 실제 배포 상태 카드 표시
- 서비스별 외부 URL 표시
- URL 복사 버튼 또는 링크 제공
- 실패 상태 메시지 표시

### 문서

- zrok/cloudflared 중 최종 선택한 방식의 실행 절차
- Ingress Controller 설치 절차
- 발표 당일 tunnel 재시작 절차
- 외부 URL 동작 확인 체크리스트

---

## 4. 완료 기준

- 발표자가 DemoDay 화면에서 배포 상태를 설명할 수 있다.
- 프론트엔드 테스트 서비스가 외부 URL로 열린다.
- 백엔드 API health endpoint가 외부 URL로 호출된다.
- DB는 외부로 직접 노출되지 않는다.
- tunnel 또는 Ingress가 끊겼을 때 복구 절차가 문서화되어 있다.

---

## 5. 우선순위

1. 외부 URL 노출 방식 확정
2. 프론트/백엔드 외부 접속 성공
3. DemoDay 화면에 URL과 상태 표시
4. Pod 실패 원인 메시지 표시
5. 발표용 복구 절차 작성

---

## 6. 리스크

- zrok/cloudflared 무료 tunnel은 주소가 바뀌거나 세션이 끊길 수 있다.
- Ingress path rewrite가 프론트엔드 SPA와 충돌할 수 있다.
- backend API가 `/health` 같은 확인 endpoint를 제공하지 않으면 상태 표시가 애매하다.
- DB를 실수로 Ingress에 노출하면 보안 문제가 생긴다.
