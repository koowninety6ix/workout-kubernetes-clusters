# CI/CD 설계 문서

## 1. 개요

- **목적**  
  GitHub, Jenkins, Nexus를 이용한 프로젝트의 CI/CD 파이프라인 설계


- **대상 시스템**
    - 소스 관리: GitHub
    - 빌드 및 배포: Jenkins
    - 아티팩트 저장소: Nexus (Helm 차트 & Docker 이미지)

---

## 2. 전체 파이프라인 흐름

1. **개발자**
    - 코드 push/merge
2. **GitHub**
    - Webhook 트리거 (자동)
    - 또는 Jenkins에서 '빌드 실행' 수동 클릭
3. **Jenkins**
    - 프로젝트 빌드
    - 컨테이너 이미지 빌드
    - Helm Chart 배포
4. **Nexus**
    - 이미지 & Helm 차트 저장
5. **Kubernetes 클러스터**
    - Helm이 배포, `--atomic` 옵션으로 자동 롤백 처리

---

## 3. 빌드 및 배포 플로우

### 3.1. 빌드 트리거 및 단계

- **Trigger:**
    - GitHub Webhook을 통한 Jenkins 빌드 자동 시작

- **Jenkins Job 주요 단계:**
    1. 소스코드 Checkout
    2. 프로젝트 빌드
    3. Docker 이미지 빌드 및 Nexus 업로드
    4. Helm 배포 (자동 롤백 정책 포함)

### 3.2. Helm 배포 및 롤백 정책

- **배포 툴:**  
  Helm CLI 사용

- **옵션:**
    - `--atomic`  
      (배포 실패 시 자동 롤백)
    - `--wait`  
      (자원 준비 완료까지 대기 후 배포 성공 처리)

- **배포 예시:**
  ```bash
  helm upgrade $PROJECT helm-repo/springchart 
  --install 
  --atomic 
  --timeout 1m 
  --wait 
  --create-namespace -n $ENV
  --set appName=$PROJECT
  --set namespace=$ENV
  --set image.tag=$(git rev-parse --short HEAD)
  ```

---

## 4. 아티팩트 저장 정책

- **Docker 이미지:**
    - `{nexus_repo}/{namespace}/{project}/{commit-hash}`

- **Helm 차트:**
    - Chart 버전 기반 업로드 (Nexus Helm Repo)

---
