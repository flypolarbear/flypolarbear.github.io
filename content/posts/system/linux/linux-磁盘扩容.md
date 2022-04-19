---
title: "linux-磁盘扩容"
date: 2022-04-19
menu:
  sidebar:
    name: linux-磁盘扩容
    identifier: linux-磁盘扩容
    parent: linux
    weight: 10
---
# linux-磁盘扩容

【云硬盘 在线扩展分区及文件系统 - 操作指南 - 文档中心 - 腾讯云】 https://cloud.tencent.com/document/product/362/54127?from=copy

[ ](https://cloud.tencent.com/document/product/362/54127?from=copy)

![image-20220419162846034](http://oss.topol.xyz/blog-img/image-20220419162846034.png)

unexpected output in sfdisk --version解决方法:

执行:LANG=en_US.UTF-8 就可以了,不行可以重启下物理机试一下.(编码问题)

lsblk——查看分区大小

growpart——扩容分区大小

xfs_growfs——对挂载进行扩容

### 具体方法：

## 操作步骤

### 查看云硬盘分区信息

1. 登录云服务器，详情请参见 [使用标准登录方式登录 Linux 实例（推荐）](https://cloud.tencent.com/document/product/213/5436)。

1. 执行以下命令，查询云硬盘的分区信息。  

fdisk -l

返回结果如下图所示：

![image-20220419163057083](http://oss.topol.xyz/blog-img/image-20220419163057083.png)

可从图中获取以下信息：

- /dev/vdb 数据盘容量为60GB，包含 MBR 分区 /dev/vdb1，容量为50GB。

- /dev/vdc 数据盘容量为60GB，包含 GPT 分区 /dev/vdc1，容量为50GB。3. 执行以下命令，确认已有分区的文件系统类型。  

df -TH

返回结果如下图所示：

![image-20220419163118071](http://oss.topol.xyz/blog-img/image-20220419163118071.png)

可从图中获取以下信息：

- /dev/vdb1 文件系统类型为 ext4，已挂载至 /mnt/disk1。

- /dev/vdc1 文件系统类型为 xfs，已挂载至 /mnt/disk2。

### 扩容分区

1. 根据实际情况执行命令，安装 gdisk 工具。

- 若分区类型为 MBR，则请跳过此步骤。

- 若分区类型为 GPT，则请对应云服务器操作系统类型，执行以下命令安装工具。

- CentOS
   \* Ubuntu 或 Debian

```
yum install gdisk -y
```

1. 对应云服务器操作系统类型，执行以下命令，安装 growpart 工具。

- CentOS
   \* Ubuntu 或 Debian

```
yum install -y cloud-utils-growpart
```

2. 执行以下命令，使用 growpart 工具扩容分区。
   本文以扩容 /dev/vdb1 分区为例，命令中 /dev/vdb 与 1 间需使用空格分隔。您可按需修改命令。  

```
growpart /dev/vdb 1
```

返回结果如下图所示，则表示分区扩容成功。

![image-20220419163543344](http://oss.topol.xyz/blog-img/image-20220419163543344.png)

### 扩容文件系统

1. 根据 [步骤3](https://cloud.tencent.com/document/product/362/54127?from=copy#Step3) 获取到的文件系统类型，执行对应命令扩容文件系统：

- 扩容 ext 文件系统
   \* 扩容 xfs 文件系统

执行以下命令，扩容 ext 文件系统。  

~~~shell
resize2fs /dev/vdb1 
~~~

返回结果如下图所示：

![image-20220419163556483](http://oss.topol.xyz/blog-img/image-20220419163556483.png)

2. 执行以下命令，查看扩容结果。  

~~~shell
df -TH
~~~

返回结果如下图所示，则表示已扩容成功。



![image-20220419163615137](http://oss.topol.xyz/blog-img/image-20220419163615137.png)

3. 扩容成功后请检查数据完整性，观察云服务器中的业务是否正常运行。
   如果有异常可以使用回滚快照的方式进行数据恢复，详情请参见 [从快照回滚数据](https://cloud.tencent.com/document/product/362/5756)。