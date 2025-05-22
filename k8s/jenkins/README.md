# jenkins 생성

```bash
vi jenkins.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: cicd
  name: jenkins
  labels:
    app: jenkins
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jenkins
  template:
    metadata:
      labels:
        app: jenkins
    spec:
      imagePullSecrets:
      - name: nexus-registry-secret
      containers:
      - name: jenkins
        image: image-registry.clusters.com/jenkins-cicd
        ports:
          - containerPort: 8080
        volumeMounts:
          - name: pvc
            mountPath: /var/jenkins_home
        resources:
          requests:
            memory: "1Gi"
            cpu: "500m"
          limits:
            memory: "2Gi"
            cpu: "1"
      volumes:
        - name: pvc
          persistentVolumeClaim:
            claimName: jenkins-pvc
---
apiVersion: v1
kind: Secret
metadata:
  name: nexus-registry-secret
  namespace: cicd
type: kubernetes.io/dockerconfigjson
stringData:
  .dockerconfigjson: |
    {
      "auths": {
        "nexus-repo:5000": {
          "username": "admin",
          "password": "{user_password}",
          "auth": "base64encoded(admin:{user_password})"
        }
      }
    }
---

```
