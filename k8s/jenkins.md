# Jenkins 생성

---

## Custom Image 생성

```bash
vi Dockerfile

docker build tag jenkins-cicd .

docker image {nexushost:port}/jenkins-cicd

docker login {nexushost:port}

docker push {nexushost:port}/jenkins-cicd
```
> image안에 kubectl, podman, helm, jenkins 설치

---

## Jenkins 구성

```bash
vi jenkins.yaml
```

[Jenkins 설정 파일](yaml/jenkins.yaml)

```bash
kubectl apply -f jenkins.yaml
```
