# kubeernetes的基本使用

# kubectl

### `get`-查找
```shell
kubectl get [res-name] -A
kubectl get pod -A
```
### `create`-创建

#### 创建namespace
```shell
kubectl create ns [nsname]
kubectl create ns kube-ops
```
### `apply`-申请
```shell
kubectl apply -f [*.yaml]
kubectl apply -f kubesphere-installer.yaml
```
### `describe`-描述
```shell
kubectl describe -n [name-space-Name] [type] [pod-name]
kubectl describe -n default pod nfs-client-provisioner-66897bdc58-vsxsw
```
### `logs`-日志
```shell
kubectl logs -n [name-space-Name] [pod-name]
kubectl logs -n kubesphere-monitoring-system thanos-ruler-kubesphere-0
kubectl logs -f [pod-name] -n [name-space-Name] -c [container_name]
```
## kubeadm

### `token`-令牌
```shell
# 创建新的令牌
kubeadm token create --print-join-command
```
### 未分类用法
```shell
# 使得master成为可调度的
kubectl taint nodes --all node-role.kubernetes.io/master-
# 编辑coreDNS的CM
KUBE_EDITOR="vim" kubectl edit configmap coredns -n kube-system
# 改变资源的期望个数
kubectl scale deployment coredns -n kube-system --replicas=2
```
# helm的基本使用

## `pull`下载
```shell
helm pull [repoName]/[name]
helm pull bitnami/jenkins
```
## `install`-安装
```shell
helm install -f values.yaml [name]  jenkins/jenkins -n [nsname]
helm install -f values.yaml jenkins  jenkins/jenkins -n kube-ops
```