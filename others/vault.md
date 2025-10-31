# Vault 설치

### PV, PVC 설정

[Vault PV,PVC 설정 파일](yaml/vault.yaml)


### 헬름 업그레이드

```bash
cat <<EOF > override-values.yaml
global:
  enabled: true
  tlsDisable: true

server:
  image:
    repository: "hashicorp/vault"
    tag: "1.19.0"
  standalone:
    enabled: true
    replicas: 1
    config: |
      ui = true

      listener "tcp" {
        address = "[::]:8200"
        cluster_address = "[::]:8201"
        tls_disable = 1
      }

      storage "file" {
        path = "/vault/data"
      }
      
      # Kubernetes Auth 활성화
      auth "kubernetes" {
        path = "kubernetes"
        type = "kubernetes"
      }

  dataStorage:
    enabled: false
   
  volumes:
    - name: vault-data
      persistentVolumeClaim:
        claimName: vault-pvc
  
  volumeMounts:
    - name: vault-data
      mountPath: /vault/data
      readOnly: false
      
  # 권한 문제 해결을 위한 initContainer
  # 임시 컨테이너를 만든 후 동일한 폴더를 마운트해 권한을 변경해줌 임시 컨테이너는 root 권한
  extraInitContainers:
    - name: data-permissions
      image: busybox:1.35
      command:
        - /bin/sh
        - -c
        - |
          chown -R 100:1000 /vault/data
          chmod -R 755 /vault/data
#          if [ -f /vault/data/key/unseal.key ]; then
#            key=$(cat /vault/data/key/unseal.key)
#            vault operator unseal $key
#          fi
      volumeMounts:
        - name: vault-data
          mountPath: /vault/data
      securityContext:
        runAsUser: 0

  # Security Context 설정
  statefulSet:
    securityContext:
      pod:
        runAsUser: 100
        runAsGroup: 1000
        fsGroup: 1000
      container:
        allowPrivilegeEscalation: false
     
  service:
    enabled: true
    type: ClusterIP
    port: 8200
    targetPort: 8200

injector:
  enabled: true

helm upgrade vault hashicorp/vault -n vault -f override-values.yaml --install
```

### Vault Unsealed

```bash

```

-- [쿠버네티스 vault 가이드](https://developer.hashicorp.com/vault/tutorials/kubernetes/kubernetes-raft-deployment-guide)