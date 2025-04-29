# cicd-k8s-cluster

하드웨어
- AMD Ryzen 8845HS (8코어 16스레드)
- RAM: 48GB
- 스토리지: SSD 1TB
- 외부 스토리지: 시놀로지 NAS 8T (NFS 제공)

cluster
- Control Plane 1개 (CPU: 2c, RAM: 4G, Storge: 40G)
- Wroker Node 3개 (CPU: 2c, RAM: 5G, STORGE: 50G)

jenkins 1개 (CPU: 1c, RAM: 6G, Storge: 30G)

nexus 1개 (CPU: 1c, RAM: 6G, Storge: 30G)

db 1개 (CPU: 2c, RAM: 8G, Storge: 40G)

haproxy + dns 1개 (CPU: 1c, RAM: 2G, Storge: 20G)

예상
