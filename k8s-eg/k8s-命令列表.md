#### kubectl
```
# 查看命名空间
kubectl get namespaces

# 查看节点
kubectl get nodes

# 查看命名空间内的pods
kubectl get pods -n kube-system

# 节点详细信息
kubectl describe node master

#查看kubelet运行
systemctl status kubelet

# 查看kubelet日志
journalctl -xefu kubelet
```

#### kubeadm
```shell script
# 重新安装
kubeadm reset
```
