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
    server master-node1 xxx.xxxx.xxx.121:6443 check
---------------------------------------

systemctl restart haproxy
systemctl enable haproxy
