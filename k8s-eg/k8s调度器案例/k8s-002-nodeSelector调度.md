#### 参考链接
```shell script
https://kubernetes.io/zh/docs/concepts/scheduling-eviction/assign-pod-node/
```

#### 节点标签
```shell script
# 增加节点标签
kubectl label nodes ubuntu-node-001  test-label=node001

# 删除节点标签
kubectl label nodes ubuntu-node-001  test-label-

# 查看节点所有标签
kubectl get node ubuntu-node-001 --show-labels
```

#### 配置调度pod到特定节点labels
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
      nodeSelector:
          test-label: node001
```
