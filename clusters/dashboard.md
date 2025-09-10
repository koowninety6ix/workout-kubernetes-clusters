# 대시보드

---

## 설치

```bash
wget https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
vi recommended.yaml
```

```yaml
spec:
  type: NodePort         # 추가
  ports:
    - port: 443
      targetPort: 8443
      nodePort: 31000    # 추가
```

```bash
kubectl apply -f recommended.yaml
```
> NodePort 사용 아유는 외부 접속을 위해, 외부 접속 필요 없거나 외부 접근 가능한 DNS 서버 사용시에는 NodePort는 사용하지 않음.

---

## 관리자 계정 구성

---

### 관리자 계정

[대시보드 관리자 설정 파일](yaml/dashboard-admin.yaml)

```bash
# 설정 파일로 파일 생성
vi dashboard-admin.yaml

kubectl apply -f dashboard-admin.yaml

# 토큰 생성, 해당 토큰으로 로그인 가능
kubectl -n kubernetes-dashboard create token admin-user
```


### 로그인 스킵 설정 (선택)

```bash
kubectl edit deploy kubernetes-dashboard -n kubernetes-dashboard
```

```yaml
- args:
  - --enable-skip-login             # 추가
  - --disable-settings-authorizer   # 추가
```

### 컨피그 파일 로그인 (선택)

```bash
vi kubeconfig
```

```yaml
apiVersion: v1
kind: Config
users:
  - name: kubernetes-admin
    user:
      token: {token} # 해당 부분에 secrets token 추가
```

---

## 인그레스 설정

[인그레스 설정 파일](./dashboard-ingress.yaml)

```bash
# 설정 파일로 파일 생성
vi dashboard-ingress.yaml

kubectl apply -f dashboard-ingress.yaml
```

---

## Matrix Server 설정

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

```yaml
# Matrix 설치후 인증 에러날시 deployment에 추가
args:
    - --kubelet-insecure-tls
```

---

## 참고 URL

 - [Kubernetes Dashboard](https://github.com/kubernetes/dashboard)
