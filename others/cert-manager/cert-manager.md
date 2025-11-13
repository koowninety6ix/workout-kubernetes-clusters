# Cert-Manager 설치

인증서 자동 발급을 위한

lets encrypt 사용

## 설치

```bash
helm repo add jetstack https://charts.jetstack.io
helm repo add emberstack https://emberstack.github.io/helm-charts
helm repo update

# cert-manager 설치
kubectl create ns cert-manager
helm install cert-manager --namespace cert-manager jetstack/cert-manager

# emberstack reflector 설치
kubectl create ns emberstack
helm upgrade --install --namespace emberstack reflector emberstack/reflector

kubectl create secret generic cloudflare-api-token-secret 
  --from-literal=api-token=${Cloudflare_API_Token} 
  -n cert-manager

kubectl apply -f clusterissuer.yaml
kubectl describe clusterissuer letsencrypt-production # Ready: True 되어야함

kubectl apply -f certificate.yaml
kubectl get certificaterequest -n cert-manager  # Ready: True 되어야함
kubectl get order -n cert-manager
kubectl get challenge -n cert-manager
```
