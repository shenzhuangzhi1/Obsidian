# quickly start
```shell
docker run \
  -p 9200:9200 \
  -p 5601:5601 \
  -p 5044:5044 \
  --restart=always \
  --name=elk-7.16.2 \
sebp/elk:7.16.2
```
This startup method is only recommended for use in a development environment, and is not recommended for a test or development environment. Because this startup method does not conform to the standard rules of container operation, this method will put multiple processes into a container.
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
  --restart=always \
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
  --restart=always \
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
### 创建`elasticsearch.yml`配置文件
```yaml
cluster.name: "docker-cluster"
network.host: 0.0.0.0
xpack.security.enabled: true   #这一步是开启x-pack插件
```
### 启动elasticsearch
```shell
#!/bin/bash

docker run -d \
  --restart=always \
  --name elasticsearch \
  --net elastic \
  -v "$PWD"/data:/usr/share/elasticsearch/data \
  -v "$PWD"/logs:/usr/share/elasticsearch/logs \
  -v "$PWD"/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml \
  -p 9200:9200 \
  -p 9300:9300 \
  -e "discovery.type=single-node" \
  -e ES_JAVA_OPTS="-Xms8g -Xmx8g" \
  docker.elastic.co/elasticsearch/elasticsearch:7.17.7
```
### 创建
```yaml
server.host: "0.0.0.0"
server.shutdownTimeout: "5s"
elasticsearch.hosts: [ "http://elasticsearch:9200" ]
monitoring.ui.container.elasticsearch.enabled: true
```
### 启动kibana
```shell
#!/bin/bash

docker run -d \
  --restart=always \
  --name kibana \
  --net elastic \
  -v "$PWD"/config/kibana.yml:/usr/share/kibana/config/kibana.yml \
  -p 5601:5601 \
  docker.elastic.co/kibana/kibana:7.17.7
```