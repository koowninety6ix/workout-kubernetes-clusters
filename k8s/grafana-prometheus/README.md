
# Grafana-Prometheus 설치 (Monitoring 도구)

---

## 설치

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

helm install prometheus prometheus-community/kube-prometheus-stack -f values.yaml -n monitoring
```

---

## 설치시 에러 해결

### 기본 values.yaml 사용시 에러
```bash
helm install prometheus prometheus-community/kube-prometheus-stack -n monitoring
```

### 나오는 에러 내용
```bash
Error: INSTALLATION FAILED: 1 error occurred:
        * Prometheus.monitoring.coreos.com "prometheus-kube-prometheus-prometheus" is invalid: spec.maximumStartupDurationSeconds: Invalid value: 0: spec.maximumStartupDurationSeconds in body should be greater than or equal to 60
```

### values.yaml 파일 다운로드 및 수정
```bash
wget https://raw.githubusercontent.com/prometheus-community/helm-charts/refs/heads/main/charts/kube-prometheus-stack/values.yaml
vi values.yaml
```

```yaml
  maximumStartupDurationSeconds: 60  # 60 이상의 값으로
```

---

## 기본 로그인 비밀번호 확인

```bash
kubectl --namespace monitoring get secrets prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 -d ; echo
```

---

## 참고 URL

- Prometheus 설치 가이드: https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack
