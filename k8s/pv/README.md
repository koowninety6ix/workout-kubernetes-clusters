
# nas nfs 연결 pv 설정

## jenkins pv / pvc

```bash
vi jenkins_pv_pvc.yaml

apiVersion: v1
kind: PersistentVolume
metadata:
  name: jenkins-pv
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  storageClassName: nfs
  nfs:
    server: xxx.xxx.xxx.xxx
    path: /k8s_pv1/jenkins_home
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  namespace: cicd                          < namespace 미리 생성
  name: jenkins-pvc
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 100Gi
  selector:
    matchLabels:
      name: jenkins-pv

kubectl apply -f jenkins_pv_pvc.yaml
```

## nexus pv / pvc

```bash
vi nexus_pv_pvc.yaml

apiVersion: v1
kind: PersistentVolume
metadata:
  name: nexus-pv
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  storageClassName: nfs
  nfs:
    server: xxx.xxx.xxx.xxx
    path: /k8s_pv1/nexus_repo
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  namespace: cicd
  name: nexus-pvc
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 100Gi
  selector:
    matchLabels:
      name: nexus-pv

kubectl apply -f jenkins_pv_pvc.yaml
```
