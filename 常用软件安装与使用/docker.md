# 一、安装docker

## 1、centos环境

### 1.1、移除以前docker相关包
```shell
sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine docker-ce dock-ce-cli containerd.io containerd
```
### 1.2、`在线安装`（与`离线安装`二选一）

#### 1.2.2、配置yum源
```shell
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
#### 1.2.3、安装docker-ce
```shell
sudo yum install -y docker-ce dock-ce-cli containerd.io
```

#### 1.2.4、启动
```shell
systemctl enable docker --now
```
### 1.3、`离线安装`（与`在线安装`二选一）

离线安装包下载地址：[链接](https://download.docker.com/linux/static/stable/x86_64/)

#### 1.3.1、解压安装包与`service`信息配置
```shell
tar -xvf docker-**.**.**.tar
cp docker/* /usr/bin/
# docker.service的内容在下面
vim /etc/systemd/system/docker.service
```

```shell
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target

[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/bin/dockerd -H unix:///var/run/docker.sock
ExecReload=/bin/kill -s HUP $MAINPID
# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNOFILE=infinity
LimitNPROC=infinity
LimitCORE=infinity
# Uncomment TasksMax if your systemd version supports it.
# Only systemd 226 and above support this version.
#TasksMax=infinity
TimeoutStartSec=0
# set delegate yes so that systemd does not reset the cgroups of docker containers
Delegate=yes
# kill only the docker process, not all processes in the cgroup
KillMode=process
# restart the docker process if it exits prematurely
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s

[Install]
WantedBy=multi-user.target
```
#### 1.3.2、赋予启动权限
```shell
chmod +x /etc/systemd/system/docker.service
```
#### 1.3.3、启动
```shell
systemctl enable docker --now
```
### 1.3、配置加速

这里添加了docker生产环境核心配置cgroup
```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "data-root": "/data/docker",
    "registry-mirrors": [
        "https://pwj62zxy.mirror.aliyuncs.com"
    ],
    "exec-opts": ["native.cgroupdriver=systemd"],
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "100m"
    },
    "storage-driver": "overlay2",
    "storage-opts": [
        "overlay2.override_kernel_check=true"
            ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
### 1.4、开启远程访问

#### 1.4.1、修改配置
```shell
# 修改Docker服务文件，需要先切换到root用户
vim /etc/systemd/system/docker.service
# 注释掉"ExecStart"这一行，并添加下面这一行信息
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock
```
#### 1.4.2、重新加载配置文件
```shell
# 重新加载配置文件
systemctl daemon-reload
# 重启服务
systemctl restart docker.service
# 查看配置的端口号（2375）是否开启（非必要）
netstat -nlpt | grep 2375
```
#### 1.4.3、参考
[Docker - Help | IntelliJ IDEA](https://link.zhihu.com/?target=https%3A//www.jetbrains.com/help/idea/docker.html)
[Docker connection settings - Help | IntelliJ IDEA](https://link.zhihu.com/?target=https%3A//www.jetbrains.com/help/idea/docker-connection-settings.html)

# 二、基础命令

## 1、找镜像
```shell
# 下载最新版
docker pull nginx
# 镜像名:版本名（标签）
docker pull nginx:1.20.1
```
## 2、启动容器
```shell
docker run [OPTIONS] IMAGE [COMMAND] [ARG……]
docker run [设置项] 镜像名 [镜像启动运行的命令]
# -d: 后台运行
# --restart=always: 开机自启
docker run -d --name=mynginx --reatart=always -p 88:80 nginx
# 查看正在运行的容器
docker ps
# 查看所有
docker ps -a
# 删除停止的容器
docker rm 容器id/名字
# 停止容器
docker stop 容器id/名字
# 再次启动
docker start 容器id/名字
# 应用开机自启
docker update 容器id/名字 --restart=always
```
## 3、修改容器内容

### 1、进入容器内部修改
```shell
# 进入容器内部
docker exec -it 容器id /bin/bash
```
### 2、挂载数据到外部进行修改
```shell
docker run --name=myngnix -d --restart=always -p 88:80 -v /data/html:/usr/share/nginx/html:rw
```
## 4、提交改变
```shell
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
docker commit -a "sheny" -m "首页变化" 234aq2wd:v1.0
```
### 1、储存到文件进行传输
```shell
# 将镜像保存成压缩包
docker save [OPTIONS] IMAGE [IMAGE...]
docker save -o abc.tar abc:0.1
# 别的机器加载这个镜像
docker load -i abc.tar
```
## 5、推送远程仓库
```shell
docker tag local-image:tagname new-repo:tagname
docker push new-repo:tagname

# 把仓库名字修改为带有仓库的名字
docker tag myngnix:1.0 sheny/myngnix:1.0
# 登录到docker hub
docker login
# 退出
docker logout
# 推送
docker push sheny/myngnix:1.0
# 别的机器下载
docker pull sheny/myngnix:1.0
```
## 6、补充
```shell
# 排错打印日志
docker logs 容器名/id
# 将指定容器中的东西复制出来
docker cp myngnix:/etc/profile /data/etc/profile
# 讲指定位置的东西复制到容器中
docker cp /data/etc/profile myngnix:/etc/profile
```