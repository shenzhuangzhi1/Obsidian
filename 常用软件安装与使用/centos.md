# 换源
## centos 7

```shell
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.cloud.tencent.com/repo/centos7_base.repo
yum clean all
yum makecache
yum list updates
yum update -y
```
## centos 8
```shell
bash <(curl -sSLL https://gitee.com/SuperManito/LinuxMirrors/raw/main/ChangeMirrors.sh)
```
# 安装EPEL
```shell
rm /etc/yum.repos.d/epel*
yum -y install epel-release
wget -P /etc/yum.repos.d/ http://mirrors.aliyun.com/repo/epel-7.repo
yum repolist
```
# 安装常用工具

## htop
```shell
yum install htop -y
```
## ntpdate
```shell
yum install ntpdate -y
# 同步时间
ntpdate time.windows.com
```
# 修改文件描述符最大值
```shell
vim /etc/security/limits.conf
```
追加下面4行：
```shell
* soft noproc 655350
* hard noproc 655350
* soft nofile 655350
* hard nofile 655350
```

```shell
# 查询是否生效
su root
ulimit -n
```