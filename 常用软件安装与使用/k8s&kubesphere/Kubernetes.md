# 快速安装

## 操作系统初始化配置
[[centos]]

## 客制化操作系统配置
```shell
# 停止防火墙
systemctl stop firewalld.service
# 停止防火墙的开机启动
systemctl disable firewalld.service

# 临时关闭selinux
setenforce 0
# 关闭selinux
sed -i 's/enforcing/disabled/' /etc/selinux/config

# 临时关闭swap
swapoff -a
# 永久关闭swap
sed -ri 's/.*swap.*/#&/' /etc/fstab

# 按照规划设置主机名字 
hostnamectl set-hostname <hostname>

# 在master添加hosts 只在master中添加，不在node中添加
cat >> /etc/hosts << EOF
10.110.96.76 k8s-master
10.110.96.67 k8s-node1
10.110.96.72 k8s-node2
EOF

# 允许 iptables 检查桥接流量
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system
```
## 安装必选组件与软件

### 安装docker

[[docker]]

### 安装kubernetes软件源
```shell
cat >> /etc/yum.repos.d/kubernetes.repo << EOF
[kubernetes]
name=kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```
### 安装kubeadm，kubelet和kubectl
```shell
yum install -y kubeadm-1.21.5 kubelet-1.21.5 kubectl-1.21.5
sudo systemctl enable --now kubelet
```
### 所有机器配置master host
```shell
echo "10.110.96.76 k8s-master" >> /etc/hosts
```
### 启动kubernetes Master
```shell
kubeadm init \
--apiserver-advertise-address=10.110.96.76 \
--kubernetes-version=v1.21.5 \
--control-plane-endpoint=k8s-master \
--image-repository=registry.cn-hangzhou.aliyuncs.com/google_containers \
--service-cidr=10.96.0.0/12 \
--pod-network-cidr=10.244.0.0/16
```
-   --apiserver-advertise-address: 当前机器对应集群的网卡的IP
-   --image-repository:修改仓库为aliyun的仓库
-   --kubernetes-version:当前kubernetes的版本
-   --service-cidr:虚拟IP
-   --pod-network-cidr:虚拟IP
**安装完成后记得保存token，执行需要执行的3条命令**

### 使master节点可被调度[多节点可选，单节点必选]
```shell
kubectl taint nodes --all node-role.kubernetes.io/master-
```
### 安装Calico网络组件
```shell
curl https://docs.projectcalico.org/manifests/calico.yaml -O
kubectl apply -f calico.yaml
```
# 快速删除
```shell
kubeadm reset -f
modprobe -r ipip
rm -rf ~/.kube/
rm -rf /etc/kubernetes/
rm -rf /etc/systemd/system/kubelet.service.d
rm -rf /etc/systemd/system/kubelet.service
rm -rf /usr/bin/kube*
rm -rf /etc/cni
rm -rf /opt/cni
rm -rf /var/lib/etcd
rm -rf /var/etcd
yum clean all
yum remove kubeadm kubelet kubectl
```