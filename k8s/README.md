
# Kubernetes 1.33 클러스터 구축 (Rocky Linux 9.5)

---

## 시스템 사전 설정

### 방화벽 비활성화

```bash
systemctl stop firewalld
systemctl disable firewalld
reboot
```

> 방화벽 설정 (특정 포트만 허용시 에러 일단 제외하는걸로)

---

### 호스트네임 및 hosts 설정

```bash
hostnamectl set-hostname {hostname}
vi /etc/hosts
# 예시:
# xxx.xxx.xxx.xxx {hostname}
```

---

### 기본 패키지 설치 및 업데이트

```bash
dnf update -y
dnf install -y net-tools wget nfs-utils
```

---

### 스왑 비활성화

```bash
swapoff -a && sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```

---

### 커널 모듈 및 네트워크 설정

```bash
cat <<EOF | tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

modprobe overlay
modprobe br_netfilter

cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

sysctl --system
```

---

## 컨테이너 런타임 (containerd)

### 옵션 1. 수동 설치

```bash
wget https://github.com/containerd/containerd/releases/download/v1.7.21/containerd-1.7.21-linux-amd64.tar.gz
tar Cxzvf /usr/local containerd-1.7.21-linux-amd64.tar.gz

wget https://raw.githubusercontent.com/containerd/containerd/main/containerd.service -P /usr/local/lib/systemd/system/
systemctl daemon-reload
systemctl enable --now containerd

wget https://github.com/opencontainers/runc/releases/download/v1.1.14/runc.amd64
install -m 755 runc.amd64 /usr/local/sbin/runc
```

### 옵션 2. 패키지 설치

```bash
dnf install -y dnf-plugins-core
dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
dnf install -y containerd.io

mkdir -p /etc/containerd
containerd config default | tee /etc/containerd/config.toml
sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml

systemctl restart containerd
systemctl enable containerd
```

---

## Kubernetes 설치

### 저장소 추가 및 설치

```bash
cat <<EOF | tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes v1.33
baseurl=https://pkgs.k8s.io/core:/stable:/v1.33/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.33/rpm/repodata/repomd.xml.key
EOF

setenforce 0
sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
systemctl enable --now kubelet
```

---

## 클러스터 초기화 (Control Plane)

```bash
kubeadm init \
  --control-plane-endpoint "xxx.xxx.xxx.110:6443" \
  --upload-certs \
  --pod-network-cidr=192.168.0.0/16
```

> 내부 네트워크가 192.168.x.x 대역일 경우 Calico CIDR 변경 필요


## 클러스터 조인 명령어 재 발행

```bash
kubeadm token create --print-join-command
> 조인 명령어 생성
kubeadm join xxx.xxx.xxx.110:6443 --token {token} \
  --discovery-token-ca-cert-hash {discovery-token-ca-cert-hash}


kubeadm init phase upload-certs --upload-certs
> certificate-key 얻기
[upload-certs] Storing the certificates in Secret "kubeadm-certs" in the "kube-system" Namespace
[upload-certs] Using certificate key:
{certificate-key}

```


### kubectl 설정

```bash
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config
```

## 클러스터 조인 (Control Plane)

```bash
kubeadm join xxx.xxx.xxx.110:6443 --token {token} \
  --discovery-token-ca-cert-hash {discovery-token-ca-cert-hash} \
  --control-plane --certificate-key {certificate-key}
```

## 클러스터 조인 (Worker Node)

```bash
kubeadm join xxx.xxx.xxx.110:6443 --token {token} \
  --discovery-token-ca-cert-hash {discovery-token-ca-cert-hash} 
```

---

## 네트워크 플러그인 (Calico)

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
kubectl get pods -n kube-system
```

---

## 참고 URL

- Kubernetes 설치 가이드: https://kubernetes.io/ko/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
- containerd 설치: https://github.com/containerd/containerd/blob/main/docs/getting-started.md
- runc 설치: https://github.com/opencontainers/runc/releases
- Calico 공식 문서: https://projectcalico.docs.tigera.io/
