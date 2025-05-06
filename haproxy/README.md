// dnf 업데이트
dnf update -y
dnf isntall -y net-tools
dnf isntall -y wget
dnf install -y tar

# 스왑 메모리 비활성화
swapoff -a && sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

# 방화벽 설정 port 허용
firewall-cmd --add-port=22/tcp --permanent
firewall-cmd --add-port=80/tcp --permanent

// k8s port (https://kubernetes.io/ko/docs/reference/networking/ports-and-protocols/)
for port in 6443 2379-2380 10250-10252 26443
do
 firewall-cmd --add-port=$port/tcp --permanent
done

firewall-cmd --permanent --add-port=179/tcp
firewall-cmd --permanent --add-port=4789/udp
firewall-cmd --permanent --add-port=5473/tcp
firewall-cmd --permanent --add-port=51820/udp
firewall-cmd --permanent --add-port=51821/udp
firewall-cmd --permanent --add-port=4789/udp

firewall-cmd --reload
firewall-cmd --list-all


# 스왑 메모리 비활성화
swapoff -a && sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

# iptables 설정
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

sysctl --system

# 필요 패지키 설치
dnf install -y tar


# 컨테이너 런타임 설치 (containerd) (https://github.com/containerd/containerd/blob/main/docs/getting-started.md)

wget https://github.com/containerd/containerd/releases/download/v1.7.21/containerd-1.7.21-linux-amd64.tar.gz
tar Cxzvf /usr/local containerd-1.7.21-linux-amd64.tar.gz

// systemd 설정
wget https://raw.githubusercontent.com/containerd/containerd/main/containerd.service -P /usr/local/lib/systemd/system/
systemctl daemon-reload
systemctl enable --now containerd


// runc 설치 (https://github.com/opencontainers/runc/releases)
wget https://github.com/opencontainers/runc/releases/download/v1.1.14/runc.amd64
install -m 755 runc.amd64 /usr/local/sbin/runc

// 설정 파일 세팅
mkdir -p /etc/containerd
containerd config default | tee /etc/containerd/config.toml
sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml

systemctl restart containerd


# k8s 설치 (https://kubernetes.io/ko/docs/setup/production-environment/tools/kubeadm/install-kubeadm)

cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.33/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.33/rpm/repodata/repomd.xml.key
exclude=kubelet kubeadm kubectl cri-tools kubernetes-cni
EOF


# permissive 모드로 SELinux 설정(효과적으로 비활성화)
setenforce 0
sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
systemctl enable --now kubelet

/// 마스터 노드 초기화

sudo kubeadm init \
  --control-plane-endpoint "192.168.0.110:6443" \
  --upload-certs \
  --pod-network-cidr=192.168.0.0/16
  
// 조인 명령어 (worker-node 및 추가적인 control-plane 추가시)
kubeadm join 192.168.0.110:6443 --token f6q53z.v0hd47wj4o87fulq \
        --discovery-token-ca-cert-hash sha256:fce984a51660635bb962453fc1a7f6375c71c9b60742afd222bbc384817f1422

# kubeconfig 파일 설정
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# 네트워크 플러그인 설치
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
kubectl get pods -n kube-system
