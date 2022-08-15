# 一、下载

官网下载：[链接](https://www.postgresql.org/download/)

# 二、Centos7安装PostgreSQL 12

## 2.1、下载

postgresql 12 Centos 7 X86_64：[链接](https://yum.postgresql.org/12/redhat/rhel-7-x86_64/repoview/postgresqldbserver12.group.html)

## 2.2、安装依赖以及rpm包

```shell
# 安装依赖
yum install libxslt libicu libperl.so
# 使用rpm命令安装rpm包
rpm -ivh ./*
```
## 2.3、 初始化数据库
```shell
# 初始化数据库
sudo /usr/pgsql-12/bin/postgresql-12-setup initdb
```
## 2.4、测试数据目录是否有效
```shell
export PGDATA=/var/lib/pgsql/12/data/
/usr/pgsql-12/bin/postgresql-12-check-db-dir ${PGDATA}
```
## 2.5、启动服务
```shell
export PGDATA=/var/lib/pgsql/12/data/
sudo -u postgres /usr/pgsql-12/bin/postmaster -D ${PGDATA} &
```
## 2.6、切换用户并进入数据库修改密码
```shell
sudo su - postgres
psql postgres
# 设置密码
\password
# 退出
\q
```
## 2.7、修改白名单使远程访问
```shell
vim /var/lib/pgsql/12/data/pg_hba.conf
```
添加图中红框框住的行：


## 2.8、修改监听地址
```shell
vim /var/lib/pgsql/12/data/postgresql.conf
listen_addresses = 'ip' # ip为实际的ip地址，不是字符串"ip"
```
例如：

![](https://cdn.nlark.com/yuque/0/2022/png/23085707/1651894747219-b91bd108-d41b-4ef3-92cd-d75f8fe9cd5a.png)

  

## 2.9、 杀掉进程并重启
```shell
ps -ef | grep postgres
export PGDATA=/var/lib/pgsql/12/data/
sudo -u postgres /usr/pgsql-12/bin/postmaster -D ${PGDATA} &
```
## 2.10、防火墙开放端口并重启防火墙
```shell
firewall-cmd --zone=public --add-port=5432/tcp --permanent
firewall-cmd --reload
```