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

능)

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
