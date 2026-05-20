# Task 3. 비용/크레딧 및 Guardrail 완성

담당자: 조원 3  
목표: 비용 예측, 발표일 기준 크레딧 소진 경고, 클러스터 리소스 기반 차단, Guardrail 검사 완성도를 높인다.

---

## 1. 배경

Grad-Deploy의 핵심 차별점은 단순 YAML 생성이 아니라, Kubernetes 운영 실패를 배포 전에 막는 Policy-as-Code 안전망이다. 현재 Resource Advisor, Guardrail Engine, Probe Generator, Network Analyzer, Validation Engine 구조는 있으나, 발표용 완성도를 위해 비용/리소스/검증 결과를 더 설득력 있게 다듬어야 한다.

---

## 2. 담당 범위

### 2.1 비용/크레딧 화면 완성

- 서비스별 예상 비용 표시
- replica 수, CPU request, memory request에 따른 비용 변화 반영
- 발표일 D-day 기준 크레딧 소진 경고 표시
- 비용 위험도를 Error/Warning/Info로 구분

### 2.2 클러스터 리소스 기반 추천/차단

- Cluster Advisor 결과를 Guardrail에 반영
- CPU request 총합과 클러스터 allocatable CPU 비교
- Memory request 총합과 클러스터 allocatable memory 비교
- 단일 노드 minikube와 운영 클러스터의 차이를 화면에 명확히 표시

### 2.3 Guardrail 검사 보강

우선순위 높은 검사:

- `VE-01`: deprecated API 사용
- `VE-02`: selector와 matchLabels 불일치
- `VE-04`: private registry 사용 시 imagePullSecrets 누락
- `NA-02`: Ingress TLS 누락 또는 외부 노출 위험
- `NA-03`: Ingress serviceName mismatch
- `PG-05`: probe periodSeconds 과도 설정
- `RA-04/RA-05`: ResourceQuota 기반 리소스 초과

### 2.4 발표용 설명 정리

가드레일이 실제로 막아주는 장애 예시를 정리한다.

- CPU 부족으로 Pending 발생
- GHCR 권한 문제로 ImagePullBackOff 발생
- metrics-server 부재로 HPA 확인 실패
- DB를 잘못된 이미지로 배포해 pull 실패
- Probe 누락으로 장애 감지 지연

---

## 3. 구현/정리할 산출물

### UI

- 비용 위험도 카드
- 클러스터 용량 대비 요청량 표시
- Guardrail 결과 요약 개선
- Error 발생 시 배포 차단 이유 명확화

### 엔진

- `src/engines/guardrail.js` 검사 규칙 보강
- 필요 시 `src/engines/ca.js` 또는 Cluster Advisor 파싱 보강
- 비용 계산 로직이 흩어져 있으면 별도 함수로 정리

### 문서

- 발표용 Guardrail 시나리오
- 비용/크레딧 계산 기준
- 리소스 차단 기준

---

## 4. 완료 기준

- 비용 탭에서 서비스 구성 변경에 따라 예상 비용이 바뀐다.
- 클러스터 리소스 초과 시 배포 전 Error로 차단된다.
- 최소 3개 이상의 실제 장애 케이스를 Guardrail이 설명할 수 있다.
- `npm run build`가 통과한다.
- 발표자가 “왜 이 툴이 Kubernetes 운영 안전망인지” 설명할 수 있다.

---

## 5. 우선순위

1. 비용/크레딧 UI 현재 상태 파악
2. Cluster Advisor → Guardrail 연결 검증
3. imagePullSecrets, Ingress, selector 검사 보강
4. 발표용 장애 시나리오 정리
5. ResourceQuota 기반 검사 고도화

---

## 6. 리스크

- 실제 클라우드 비용과 로컬 minikube 비용 모델은 다르므로 “예상 비용”으로 표현해야 한다.
- 너무 많은 Guardrail 규칙을 한 번에 추가하면 발표 전 버그가 생길 수 있다.
- Error/Warning 기준이 모호하면 사용자가 왜 차단됐는지 이해하기 어렵다.
