#### kubectl
```shell
# 查看命名空间
kubectl get namespaces

# 查看节点
kubectl get nodes

# 查看命名空间内的pods
kubectl get pods -n kube-system

# 查看全面命名空间的pod
kubectl get pod --all-namespaces

# 查看某一个pod情况
kubectl describe pod ks-installer-b6954b57f-svccp -n kubesphere-system

# 节点详细信息
kubectl describe node master

#查看kubelet运行
systemctl status kubelet

# 查看kubelet日志
journalctl -xefu kubelet


kubeadm token create --print-join-command
```

#### kubeadm
```shell script
# 重新安装
kubeadm reset
```
