
nas nfs 연결 pv 설정

vi jenkinspv.yaml
----------------------------------------------------------------------
apiVersion: v1
kind: PersistentVolume
metadata:
  name: jenkins
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
--------------------------------------------------------------------


pvc 연결
