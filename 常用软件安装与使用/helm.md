# 下载

下载地址:[链接](https://github.com/helm/helm/releases)

# 安装
```shell
tar -zxvf helm-vX.X.X-linux-amd64.tar.gz
cp linux-amd64/* /usr/bin/
```
# 配置源

当连接失败的时候记得更换`/etc/hosts`
```shell
helm repo remove stable
helm repo add stable http://mirror.azure.cn/kubernetes/charts/
helm repo add incubator http://mirror.azure.cn/kubernetes/charts-incubator/
helm repo add kubesphere https://charts.kubesphere.io/main
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```