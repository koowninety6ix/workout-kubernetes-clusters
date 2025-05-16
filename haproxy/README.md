# haproxt & dns server 설치

## host 설정
hostnamectl set-hostname {hostname}
vi /etc/hosts
xxx.xxx.xxx.xxx {hostname}

## haproxy 설치

```bash
dnf install -y haproxy

vi /etc/haproxy/haproxy.cfg

----------------------------------------------------------------------
frontend kubernetes-master-lb
    bind *:6443
    mode tcp
    option tcplog
    default_backend kubernetes-masters

backend kubernetes-masters
    mode tcp
    balance roundrobin
    option tcp-check
    server control-plane1 xxx.xxxx.xxx.121:6443 check
    server control-plane2 xxx.xxxx.xxx.122:6443 check
    server control-plane3 xxx.xxxx.xxx.123:6443 check

# 외부에서 dashboard 보기용 
frontend kubernetes-dashboard-lb
    bind *:31000
    mode tcp
    option tcplog
    default_backend kubernetes-dashboard

backend kubernetes-dashboard
    mode tcp
    balance roundrobin
    option tcp-check
    server control-plane1 xxx.xxxx.xxx.121:31000 check
    server control-plane2 xxx.xxxx.xxx.122:31000 check
    server control-plane3 xxx.xxxx.xxx.123:31000 check

# ingress controller http
frontend kubernetes-http-lb
    bind *:80
    mode tcp
    option tcplog
    default_backend kubernetes-http

backend kubernetes-http
    mode tcp
    balance roundrobin
    option tcp-check
    server control-plane1 xxx.xxxx.xxx.121:30080 check
    server control-plane2 xxx.xxxx.xxx.122:30080 check
    server control-plane3 xxx.xxxx.xxx.123:30080 check

# ingress controller https
frontend kubernetes-https-lb
    bind *:443
    mode tcp
    option tcplog
    default_backend kubernetes-https

backend kubernetes-https
    mode tcp
    balance roundrobin
    option tcp-check
    server control-plane1 xxx.xxxx.xxx.121:30443 check
    server control-plane2 xxx.xxxx.xxx.122:30443 check
    server control-plane3 xxx.xxxx.xxx.123:30443 check
-------------------------------------------------------------------

systemctl restart haproxy
systemctl enable haproxy

```

## dns 서버 설치

```bash
dnf install -y bind bind-utils

vi /etc/named
-------------------------------------------------------------
listen-on port 53 { any; };         < any 변경
listen-on-v6 port 53 { any; };      < any 변경
allow-query     { any; };           < any 변경

# 추가
zone "{dns.name}" IN {                  < dns 
    type master;
    file "";                            < dns 파일 위치
    allow-update { none; };
};
------------------------------------------------------------
```
