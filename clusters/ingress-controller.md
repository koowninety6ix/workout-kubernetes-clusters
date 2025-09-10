# Ingress Controller 구성 (helm 사용)

## 설치

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install nginx-ingress ingress-nginx/ingress-nginx --namespace ingress-nginx --create-namespace
```

```yaml
Type: NodePort  < 변경
```
> Type을 NodePort로 변경한건 내부 dns서버를 사용하고 있어서 외부에서 dashboard 접근 불가 하기 때문에 NodePort사용
