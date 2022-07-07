# k8s 主节点安装
[参考文档](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/)

## 安装kubeadm

#### 设置主机名
```shell script
hostnamectl set-hostname ubuntu-k8s-master

vim /etc/hosts
127.0.0.1 ubuntu-k8s-master

reboot
```

#### 安转kubeadm
```shell script
apt-get update

apt-get install -y apt-transport-https ca-certificates curl

curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

apt-get update

apt-get install -y docker.io  kubelet kubeadm kubectl

apt-mark hold kubelet kubeadm kubectl
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

## 创建集群

#### 初始化
```shell script
kubeadm init --config kubeadm-init.yaml
```

#### 记录kubeadm join
```shell script
kubeadm join 192.168.213.134:6443 --token 4y5dz8.5j5okqdj8gapgi5u \
        --discovery-token-ca-cert-hash sha256:ff028d285a64cbec5f558572673f20f88a079bc00caf79a1a4f464dad2df02ae  
```

#### 设置配置文件
```shell script
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config
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



