# Kubernetes 대시보드 설치

---

### 배포 파일 수정 (NodePort 사용)

```bash
wget https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
vi recommended.yaml
```

```yaml
spec:
  type: NodePort         < 추가
  ports:
    - port: 443
      targetPort: 8443
      nodePort: 31000    < 추가
```

```bash
kubectl apply -f recommended.yaml
```

---

## 대시보드 관리자 계정 생성

```bash
vi dashboard-admin.yaml
```

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

```bash
kubectl apply -f dashboard-admin.yaml
```

---

### 토큰 생성 및 로그인

```bash
kubectl -n kubernetes-dashboard create token admin-user
```

토큰으로 대시보드 로그인 가능

---

### 로그인 스킵 설정 (선택)

```bash
kubectl edit deploy kubernetes-dashboard -n kubernetes-dashboard
```

```yaml
- args:
  - --enable-skip-login             < 추가
  - --disable-settings-authorizer   < 추가
```

---

## 접속 확인

```bash
https://{NODE_IP}:31000
```

## Ingress 리소스 설정 (Ingress Controller 설정 끝난후 작업)

```bash
vi kubernetes-dashboard-ingress.yaml
```

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: kubernetes-dashboard
  namespace: kubernetes-dashboard
  annotations:
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
spec:
  ingressClassName: nginx
  rules:
    - host: dashboard.clusters.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: kubernetes-dashboard
                port:
                  number: 443
  tls:
    - hosts:
        - dashboard.clusters.com
      secretName: dashboard-tls
```

```bash
kubectl apply -f kubernetes-dashboard-ingress.yaml
```
> ingress 설정이 끝나면 nodePort 삭제

---

## 참고 URL

 - Kubernetes Dashboard: https://github.com/kubernetes/dashboard
