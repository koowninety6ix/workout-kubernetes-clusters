# host 설정
hostnamectl set-hostname {hostname}
vi /etc/hosts
xxx.xxx.xxx.xxx {hostname}

# haproxy 설치
dnf install -y haproxy

vi /etc/haproxy/haproxy.cfg

--------------------------------------
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

frontend kubernetes-http-lb
    bind *:80
    mode tcp
    option tcplog
    default_backend kubernetes-http

backend kubernetes-http
    mode tcp
    balance roundrobin
    option tcp-check
    server master-node1 xxx.xxxx.xxx.121:8080 check
---------------------------------------

systemctl restart haproxy
systemctl enable haproxy

---------------------------------------------------------
dns 서버 설치

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
