# Ingress Controller 구성 (helm 사용)

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install nginx-ingress ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace
```

```yaml
Type: NodePort  < 변경
```
