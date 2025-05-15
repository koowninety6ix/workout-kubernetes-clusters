#nexus pod구성 
image repo, helm_repo 역활, 내부 망일때는 dependency repo 역활

```bash
vi nexus_repo.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: cicd
  name: nexus-repo
  labels:
    app: nexus-repo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nexus-repo
  template:
    metadata:
      labels:
        app: nexus-repo
    spec:
      containers:
      - name: nexus
        image: sonatype/nexus3:3.80.0
        ports:
        - containerPort: 5000
        - containerPort: 8081
        volumeMounts:
          - name: pvc
            mountPath: /nexus-data
      volumes:
        - name: pvc
          persistentVolumeClaim:
            claimName: nexus-pvc
---
kind: Service
metadata:
  namespace: cicd
  name: nexus-repo
spec:
  selector:
    app: nexus-repo
  ports:
    - name: repo
      port: 5000
      targetPort: 5000
    - name: web
      port: 8081
      targetPort: 8081
---


kubectl apply -f nexus_repo.yaml

```
