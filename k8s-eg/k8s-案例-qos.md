#### 参考链接
```shell script
https://kubernetes.io/zh/docs/tasks/configure-pod-container/quality-service-pod/
```

#### 命名空间
```shell script
# 创建命名空间
kubectl create namespace qos-example
```

#### 创建pod
```shell script
kubectl apply -f https://k8s.io/examples/pods/qos/qos-pod.yaml --namespace=qos-example

## 查看 pod情况
```