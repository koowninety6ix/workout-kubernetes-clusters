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


## 참고 URL
- helm 설치 가이드 : https://helm.sh/docs/intro/install/