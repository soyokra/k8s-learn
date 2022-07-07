## k8s节点

#### 设置主机名
```shell script
hostnamectl set-hostname ubuntu-node-003

vim /etc/hosts
127.0.0.1     ubuntu-node-003

reboot
```

#### 安转kubeadm
```shell script
apt-get install -y apt-transport-https ca-certificates curl
curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
apt-get update
apt-get install -y docker.io kubeadm
```

#### 修改docker cgroup driver
```shell script
cat > /etc/docker/daemon.json <<EOF
{
"exec-opts": ["native.cgroupdriver=systemd"]
}
EOF

systemctl restart docker
```

#### 镜像加速器
```shell script
vim /etc/docker/daemon.json

{
  "registry-mirrors": ["https://nd7ypnze.mirror.aliyuncs.com"]
}

systemctl daemon-reload
systemctl restart docker
```