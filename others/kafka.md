# Kafka 설치

### Helm Chart 사용

---

### 저장소 추가

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm show values bitnami/kafka > values.yaml
```

### values.yaml 수정

```yaml
image:
  registry: docker.io
  repository: bitnami/kafka > bitnamilegacy/kafka 로 변경
  tag: 4.0.0-debian-12-r10
```

### 헬름 배포

```bash
helm install -n dev kafka bitnami/kafka --values values.yaml --set namespaceOverride=dev
```