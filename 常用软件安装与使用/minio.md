# 下载
```shell
wget https://dl.min.io/server/minio/release/linux-amd64/minio
```
# 启动
### 最简单模式运行Minio
# 修改应用的权限
```shell
chmod +x minio
MINIO_ROOT_USER=minioadmin MINIO_ROOT_PASSWORD=minioadmin ./minio server /mnt/data1 --console-address ":9001"
```
### 以纠删码模式运行Minio
Minio使用纠删码**erasure code**以及校验和**checksum**来保护数据免受硬件故障和无声数据损坏。 即便您丢失一半数量（N/2）的硬盘，您仍然可以恢复数据。
示例: 使用Minio，在8个盘中启动Minio服务。
```shell
# 修改应用的权限
chmod +x minio
MINIO_ROOT_USER=minioadmin MINIO_ROOT_PASSWORD=minioadmin ./minio server /mnt/data1 /mnt/data2 /mnt/data3 /mnt/data4 /mnt/data5 /mnt/data6 /mnt/data7 /mnt/data8 --console-address ":9001"
```
### 以集群模式运行Minio
所有的机器都需要执行同一个启动命令
```shell
# 修改应用的权限
chmod +x minio
MINIO_ROOT_USER=minioadmin MINIO_ROOT_PASSWORD=minioadmin ./minio server --address :9001 http://192.168.10.11/mnt/data1 http://192.168.10.12//mnt/data2 http://192.168.10.13/mnt/data3 http://192.168.10.14//mnt/data4 --console-address ":9001"
```