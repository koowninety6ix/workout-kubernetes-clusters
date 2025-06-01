# helm 설치

---

## 설치

### option1

```bash
wget https://get.helm.sh/helm-v{version}-linux-amd64.tar.gz
tar -zxvf helm-v{version}-linux-amd64.tar.gz
mv linux-amd64/helm /usr/local/bin/helm
```

### option2

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

---

## 참고 URL
- helm 설치 가이드 : https://helm.sh/docs/intro/install/