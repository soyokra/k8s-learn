## k8s基础虚拟机配置

#### 安装必要的软件
```shell script
apt install -y ebtables ethtool vim
```


#### 关闭防火墙
```shell script
ufw disable
```

#### 设置时区
```shell script
tzselect

cp /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime

apt-get install ntpdate
ntpdate time.windows.com

date -R
```

#### 关闭swap
```shell script
mount -n -o remount,rw /

vi /etc/fstab
// 注释 swap

reboot

free -m
```

#### 允许 iptables 检查桥接流量
```shell script
# 查看br_netfilter模块被加载
lsmod | grep br_netfilter

# 显式加载br_netfilter
modprobe br_netfilter
```

#### net.bridge.bridge-nf-call-iptables设置为1
```shell script
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF


cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sysctl --system
```