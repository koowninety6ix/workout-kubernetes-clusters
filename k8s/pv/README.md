
# nas nfs 연결 pv 설정

## jenkins pv / pvc

```bash
vi jenkins_pv_pvc.yaml

---------------------------------------------------------------------
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
  nfs:
    server: xxx.xxx.xxx.xxx
    path: /volume1/k8s_pv1/jenkins_home
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  namespace: cicd                          < namespace 미리 생성
  name: jenkins-pvc
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 100Gi
  volumeName: jenkins-pv
  volumeMode: Filesystem
---------------------------------------------------------------------

kubectl apply -f jenkins_pv_pvc.yaml
```

## nexus pv / pvc

```bash
vi nexus_pv_pvc.yaml

---------------------------------------------------------------------
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
  nfs:
    server: xxx.xxx.xxx.xxx
    path: /volume1/k8s_pv1/nexus_repo
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  namespace: cicd
  name: nexus-pvc
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 100Gi
  volumeName: nexus-pv
  volumeMode: Filesystem
# selector:                                < 해당 내용을 사용하려면 pv의 라벨 지정
#   matchLabels:
#     name: nexus-pv
---------------------------------------------------------------------

kubectl apply -f jenkins_pv_pvc.yaml
```
> nas의 nfs 설정이 끝나있어야함
