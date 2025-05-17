# nexus pod구성 
image repo, helm_repo 역활, 내부 망일때는 dependency repo 역활

```bash
vi nexus_repo.yaml
```
```yaml
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
        resources:
          requests:
            memory: "2Gi"
            cpu: "500m"
          limits:
            memory: "4Gi"
            cpu: "1"
      volumes:
        - name: pvc
          persistentVolumeClaim:
            claimName: nexus-pvc
---
apiVersion: v1
kind: Service
metadata:
  namespace: cicd
  name: nexus-repo
spec:
  type: ClusterIP
  selector:
    app: nexus-repo
  ports:
    - name: repo
      protocol: TCP
      port: 5000
      targetPort: 5000
    - name: web
      protocol: TCP
      port: 8081
      targetPort: 8081
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nexus-repo-ingress
  namespace: cicd
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: repository.cluseters.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nexus-repo
            port:
              number: 8081
```
```bash
kubectl apply -f nexus_repo.yaml
```

## 내부 구성

1.로그인
id : admin
password: {초기 비밀번호는 /nexus-data/admin.password 파일안에 있음.}

2.blob store 생성

img-repo
helm-repo

3. repostory 생성

docker-hosted
helm-hosted

4. docker의 경우
Realms 설정이 필요 > bearer token active 설정
