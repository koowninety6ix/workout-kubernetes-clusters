# Helm-Chart 구성

---

## 설치

### 옵션 1

```bash
wget https://get.helm.sh/helm-v{version}-linux-amd64.tar.gz
tar -zxvf helm-v{version}-linux-amd64.tar.gz
mv linux-amd64/helm /usr/local/bin/helm
```

### 옵션 2

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

---

## 명령어

### 패키지 업데이트

- Template 폴더 하위 yaml 파일 및 values.yaml 수정 후 Chart.yaml 수정

```bash
vi Chart.yaml
```

```yaml
apiVersion: v2
name: springchart
description: A Helm chart for Kubernetes
type: application
version: 0.1.3          # 해당 부분 수정
appVersion: "1.16.0"
```

```bash
helm package springchart

curl -u <username>:<password> \
  --upload-file <helm-chart.tgz> \
  https://<nexus-host>/repository/<helm-repo-name>/

helm repo index . --url https://nexus.example.com/repository/helm/

curl -u <username>:<password> \
  --upload-file index.yaml \
  https://<nexus-host>/repository/<helm-repo-name>/

```

### 무중단 배포 구성

1. rollback시 이전 이미지 버전으로 하기 위한 tag 관리

```bash
podman build --build-arg ENV=dev -t ${IMAGE_REPO}/dev/test-springboot-jdk17:$(git rev-parse --short HEAD) . # 이미지 빌드시 tag에 git 버전 명시
```



3. deployment 설정 -> value로 뺴서 사용

```yaml
# rollingUpdate 설정
rollingUpdate:
  maxSurge: 1               # 새 파드 몇 개까지 추가
  maxUnavailable: 0         # 업그레이드 중 몇 개 파드까지 다운 가능 > 0 이면 기존 파드가 모두 살아 있어야 새 파드 배포 시작

# readinessProbe 설정 (springboot actuator 사용, 다른 언어나 서비스는 직접 만들거나 다른 라이브러리 사용)
readinessProbe:
  httpGet:
    path: /actuator/health
    port: 8081              # 외부에 actuator 노출 하지 않기
    scheme: HTTP
  initialDelaySeconds: 30   # 컨테이너 시작 후 몇 초 뒤에 프로브를 시작할지
  timeoutSeconds: 2         # 요청 타임아웃 시간
  periodSeconds: 5          # 프로브의 작동 간격
  failureThreshold: 3       # 연속 실패시 컨테이너 재시작 할지

progressDeadlineSeconds: 60 # 60초 안에 Ready 안 되면 배포 실패로 간주 (해당 설정을 하는 이유는 밑에 기재)
```


4. helm upgrade 옵션 설정

- atomic: 배포 실패 시 자동 롤백 
- timeout: 모든 리소스가 준비 될 때까지 기다리는 시간 
- wait: 파드 ready 상태까지 기다림

```yaml
helm upgrade {APP_NAME} helm-repo/springchart --install --atomic --timeout 1m --wait --create-namespace -n {NAMESPACE} --set appName={APP_NAME} --set namespace={NAMESPACE} --set image.tag=$(git rev-parse --short HEAD)
```
>> ***[readinessProbe 실패하지만 Helm은 성공 반환 하는 이슈]***   
>> 이유는 readinessProbe 가 실패 해도 Deployment는 실패 상태가 되지 않음 Pod의 상태 Ready: false 로 만듬  
>> Pod가 준비 되지 않았으니 Deployment는 "Progressing" 상태로 있음  
>> helm 입장에서는 Helm timeout → Helm은 종료되지만...  
&nbsp;&nbsp;&nbsp;→ 이때 Deployment는 아직 "Progressing" 상태 (progressDeadlineSeconds 600초 남음)  
&nbsp;&nbsp;&nbsp;→ Kubernetes는 "아직 진행 중"이라고 판단  
&nbsp;&nbsp;&nbsp;→ --atomic이 작동하지 않음   
>> 해결책: progressDeadlineSeconds < Helm timeout으로 설정

## 참고 URL
- helm 설치 가이드 : https://helm.sh/docs/intro/install/
