# Kubernetes 대시보드 설치

---

## 배포 파일 수정 (NodePort 사용)

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
> NodePort 사용 아유는 외부 접속을 위해

---

## 대시보드 관리자 계정 생성

### 관리자 계정 구성
```bash
vi dashboard-admin.yaml
```

[대시보드 관리자 설정 파일 보기](yaml/dashboard-admin.yaml)


```bash
kubectl apply -f dashboard-admin.yaml
```

### 토큰 생성 및 로그인

```bash
kubectl -n kubernetes-dashboard create token admin-user
```
> 토큰으로 대시보드 로그인 가능


### 로그인 스킵 설정 (선택)

```bash
kubectl edit deploy kubernetes-dashboard -n kubernetes-dashboard
```

```yaml
- args:
  - --enable-skip-login             # 추가
  - --disable-settings-authorizer   # 추가
```

### 접속 확인

```bash
https://{NODE_IP}:31000
```

---

## Ingress 리소스 설정 (Ingress Controller 설정 끝난후 작업)

```bash
vi dashboard-ingress.yaml
```

[Ingress 설정 파일 보기](./nexus_repo.yaml)

```bash
kubectl apply -f dashboard-ingress.yaml
```

---

## 대시보드 리소스 확인용 Matrix Server 설치

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

### Matrix 설치후 인증 에러날시 deployment에 추가

```yaml
args:
    - --kubelet-insecure-tls
```

---

## 참고 URL

 - Kubernetes Dashboard: https://github.com/kubernetes/dashboard
