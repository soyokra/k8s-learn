## k8s节点

#### 设置主机名
```shell script
hostnamectl set-hostname ubuntu-node-001

vim /etc/hosts
127.0.0.1     ubuntu-node-001

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

#### 安转k8s
```shell script
kubeadm init --config kubeadm-init.yaml
```

#### 记录kubeadm join
```shell script
kubeadm join 192.168.220.134:6443 --token 0jlyqu.1atumyr6pomfhrav \
        --discovery-token-ca-cert-hash sha256:0fdf5f755f2d1871eeed832024cf7ee8ed0364c637aae0459d9901205f205872 
```

#### 配置文件
```shell script
cp /etc/kubernetes/admin.conf .kube/config
```

#### 安装网络插件
```shell script
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

#### flannel分配cidr
```shell script
vim /etc/kubernetes/manifests/kube-controller-manager.yaml

--allocate-node-cidrs=true
--cluster-cidr=10.244.0.0/16

systemctl restart kubelet
```

#### 解决k8s cordns CrashLoopBackOff 问题
```shell script
kubectl edit cm coredns -n kube-system

# 删除 loop 保存并退出

# 删除pod
kubectl delete pod coredns-xxx-xxxx  -n kube-system
```


#### 安装dashboard
```shell script
# 安转国内镜像源kubernetes-dashboard
wget https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml

image: k8s.gcr.io/kubernetes-dashboard-amd64:v1.10.1
改为
image: docker.io/mirrorgooglecontainers/kubernetes-dashboard-amd64:v1.10.1

kubectl apply -f kubernetes-dashboard.yaml

# 安转 ServiceAccount 
kubectl apply -f https://kuboard.cn/install-script/k8s-dashboard/auth.yaml

# 获取Bearer Token
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')

# 访问url
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```



