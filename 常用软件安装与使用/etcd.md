# 安装

## 下载

离线包下载地址：[链接](https://github.com/etcd-io/etcd/tags)

## 部署

### 单机部署

#### 复制
```shell
tar -zxvf etcd-****.gz
cp etcd etcdctl etcdutl /usr/bin/
etcd --version
```

#### 修改配置文件
```shell
mkdir /var/lib/etcd
mkdir /etc/etcd
```

```shell
# 添加内容在下面
vim /etc/etcd/etcd.conf
```

```shell
#etcd的名字，使用发现时，每个成员必须具有唯一的名称
ETCD_NAME="etcd01"

#数据保存的目录
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"

#对外提供服务的地址
ETCD_LISTEN_CLIENT_URLS="http://192.168.135.130:2379"

#此成员的客户端URL列表，用于通告群集的其余部分，对外公告的该节点客户端监听地址
ETCD_ADVERTISE_CLIENT_URLS="http://192.168.135.130:2379"
```
#### 配置服务

添加内容在下面
```shell
vim /usr/lib/systemd/system/etcd.service
```

```server
[Unit]
Description=Etcd Server
After=network.target

[Service]
Type=simple
WorkingDirectory=/var/lib/etcd/
EnvironmentFile=/etc/etcd/etcd.conf
ExecStart=/usr/bin/etcd

[Install]
WantedBy=multi-user.target
```
#### 启动服务
```shell
systemctl enable etcd
systemctl start etcd
systemctl status etcd
```
### 集群部署

在集群模式下，`endpoint`只需要填一个节点就可以使得所有的节点使用最新的配置

# 命令

## auth

auth有两个状态：

-   `enable`
-   `disable`

```shell
# etcd开启访问认证
etcdctl auth enable
# etcd关闭访问认证
etcdctl auth disable
```
## role
管理角色信息
```shell
# 获取root权限细节
etcdctl role get root
# 新增root权限
etcdctl role add root
```
## user

管理用户信息
```shell
# 添加用户root
etcdctl user add root 
# 为root用户设置密码
etcdctl user passwd root 
# 赋予root用户root权限
etcdctl user grant-role root root
```
# 可选配置项
```shell
# 设置要访问的etcd节点
--endpoints=http://15.72.185.13:2379
# 登录时填写密码
--password="Szzf@2022"   
# 登录时填写用户名
--user="root" 
```
# 例子

## 开启root用户
```shell
etcdctl --endpoints=http://192.168.0.111:2379 user add root

etcdctl --endpoints=http://192.168.0.111:2379 auth enable

etcdctl --endpoints=http://192.168.0.111:2379 \
--user="root" \
--password="szzf@2022" \
user grant-role root root
```
## 备份与升级
```shell
# 查看集群状态
etcdctl --endpoints=http://10.3.248.99:2379 \
--insecure-skip-tls-verify endpoint status --write-out=table

# 备份
mkdir -p /inspur/backup/etcd
etcdctl --endpoints=http://10.110.96.55:2379 \
snapshot save /inspur/backup/etcd/etcd_`date +%Y%m%d%H%M`.db --user=root

cp -p -r /var/lib/etcd/default.etcd /inspur/backup/etcd/

# 停止鉴权
etcdctl --endpoints=http://10.110.96.55:2379 auth disable --user=root

# 停止所有节点的服务
systemctl stop etcd

# 安装新版
tar -zxvf etcd-v3.5.4-linux-amd64.tar.gz
cd etcd-v3.5.4-linux-amd64
sudo cp -a etcd etcdctl /usr/bin/

# 查看版本
etcd --version

# 启动
systemctl enable etcd
systemctl start etcd
systemctl status etcd

# 开启认证
etcdctl --endpoints=http://10.110.96.55:2379 auth enable

# 查看集群状态
etcdctl --endpoints=http://10.110.96.55:2379 \
--insecure-skip-tls-verify endpoint status --write-out=table

# 如果升级失败，可以用以下命令恢复步骤2备份etcd快照数据
etcdctl snapshot restore /inspur/backup/etcd/etcd_202206272133.db  --data-dir=/var/lib/etcd/default.etcd
```