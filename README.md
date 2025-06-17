# cicd-k8s-cluster

---

## 하드웨어
- AMD Ryzen 8845HS (8코어 16스레드)
- RAM: 48GB
- 스토리지: SSD 1TB
- 외부 스토리지: 시놀로지 NAS 8T (NFS 제공)

---

## 리소스 구성

### Cluster
- Control Plane 3개 (CPU: 2c, RAM: 4G, Storge: 40G)
- Wroker Node 4개 (CPU: 2c, RAM: 7G, Storge: 50G)

### 기타
- haproxy + dns 1개 (CPU: 1c, RAM: 2G, Storge: 20G)

---

## Iptable

### Proxy 11x
- haproxy + dns xxx.xxx.xxx.110

### Control Plane 12x
- Control Plane 1 xxx.xxx.xxx.121
- Control Plane 2 xxx.xxx.xxx.122
- Control Plane 3 xxx.xxx.xxx.123

### Worker Node 13x
- Wroker Node 1 xxx.xxx.xxx.131
- Wroker Node 2 xxx.xxx.xxx.132
- Wroker Node 3 xxx.xxx.xxx.133
- Wroker Node 4 xxx.xxx.xxx.134
---

![image](https://github.com/user-attachments/assets/6482c8db-5806-4b61-a188-317c502ee41e)


## 남는 리소스
cpu 1
ram 6G
