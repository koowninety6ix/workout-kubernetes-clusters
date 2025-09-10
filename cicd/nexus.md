# Nexus 생성 
image repo, helm_repo 역활, 내부 망일때는 dependency repo 역활

---

## Nexus 구성

[Nexus 설정 파일](yaml/nexus.yaml)
```bash
vi nexus.yaml

kubectl apply -f nexus.yaml
```

## containerd 수정 (노드 별로 추가)

```bash
vi /etc/containerd/confing.toml
```

```yaml
    [plugins."io.containerd.grpc.v1.cri".registry]
      [plugins."io.containerd.grpc.v1.cri".registry.mirrors]
        # 밑에 내용 추가
        [plugins."io.containerd.grpc.v1.cri".registry.mirrors."image-registry.clusters.com"]
          endpoint = ["http://image-registry.clusters.com"]
```

```bash
systemctl restart containerd
```
> 이미지 저장소의 프로토콜이 https가 아닌 http 이기에 필요
---

## 내부 구성

### 로그인
- id : admin 
- password: {초기 비밀번호는 /nexus-data/admin.password 파일안에 있음.}

### blob store 생성
- img-store
- helm-store 

### repository 생성
- docker-hosted
- helm-hosted

### docker의 경우
- Realms 설정이 필요 > bearer token active 설정
