# 快速启动
```shell
docker run \
  -p 9200:9200 \
  -p 5601:5601 \
  -p 5044:5044 \
  --restart=always \
  --name=elk-7.16.2 \
sebp/elk:7.16.2
```
这种启动方式仅仅推荐在开发环境中使用，测试环境或开发环境并不推荐使用这种方式。因为这种启动方式并不符合容器运行的标准规则，这种方式将多个进程放进一个容器中。
# 标准启动
## 8.X
### 创建网络
```shell
# 创建网络
docker network create elastic
```
### 启动elasticsearch
```shell
#!/bin/bash

docker run -t \
  --name elasticsearch \
  --net elastic \
  -v "$PWD"/data:/var/data/elasticsearch \
  -v "$PWD"/log:/var/log/elasticsearch \
  -p 9200:9200 \
  -p 9300:9300 \
  -e "discovery.type=single-node" \
  -e ES_JAVA_OPTS="-Xms8g -Xmx8g" \
  docker.elastic.co/elasticsearch/elasticsearch:8.5.1
```
启动后默认的用户`elastic`以及密码会自动生成，同时也会生成Kibana的token。可以使用docker logs 命令查看获得。
### 启动kibana
```shell
docker run -d \
  --name kibana \
  --net elastic \
  -p 5601:5601 \
  docker.elastic.co/kibana/kibana:8.5.1
```
启动后在终端会打印一个URL，在浏览器打开这个URL配置连接到es
## 7.17.7
### 对映射文件赋权
```shell
chmod 777 -R logs/
chmod 777 -R data/
```
### 创建网络
```shell
# 创建网络
docker network create elastic
```
### 启动elasticsearch
```shell
#!/bin/bash

docker run -d \
  --name elasticsearch \
  --net elastic \
  -v "$PWD"/data:/usr/share/elasticsearch/data \
  -v "$PWD"/logs:/usr/share/elasticsearch/logs \
  -p 9200:9200 \
  -p 9300:9300 \
  -e "discovery.type=single-node" \
  -e ES_JAVA_OPTS="-Xms8g -Xmx8g" \
  docker.elastic.co/elasticsearch/elasticsearch:7.17.7
```
