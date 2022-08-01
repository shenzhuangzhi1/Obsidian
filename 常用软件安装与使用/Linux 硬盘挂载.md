# 查看磁盘

```shell
# 查看磁盘占用情况，同时可以查看已挂载的磁盘及其挂载位置
df -lh
# 查看所有的磁盘分区
fdisk -l 
```

如图可见，两块硬盘`/dev/vda`、`/dev/vdb`,一个下面有分区，一个没有。没有分区表示未初始化分区信息。

# 硬盘分区
```shell

# 分区/dev/vdb
fdisk /dev/vdb

##fdisk命令
# 保存并退出
wq
```
# 格式化分区
```shell
mkfs -t ext4 /dev/vdb1
```
# 挂载分区
```shell
# 创建路径
mkdir /inspur
# 单独挂载到某个目录
mount /dev/vdb1 /inspur 
# 查看磁盘使用情况和挂载情况
df -lh
# 卸载某个分区
umount /dev/vdb1
```
说明：/inspur 表示默认挂载位置，使用mount挂在后重启系统挂载不再生效，需要添加开启启动

# 添加分区自动挂载

echo '/dev/vdb1 /inspur ext4 defaults 0 0' >> /etc/fstab
# 卸载分区
umount -v /dev/vdb1
# 挂载所有新分区
mount –a
# 查看写入分区信息
cat /etc/fstab