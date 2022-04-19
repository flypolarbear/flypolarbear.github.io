---
title: linux-升级内核
date: 2022-04-19
menu: 
  sidebar: 
    name: linux-升级内核
    identifier: linux-升级内核
    parent: linux
    weight: 
---
# linux-升级内核
### 1. 从镜像站点下载内核rpm并安装
```bash
wget http://ftp.sjtu.edu.cn/sites/elrepo.org/linux/kernel/el7/x86_64/RPMS/kernel-ml-5.15.6-1.el7.elrepo.x86_64.rpm
rpm -ivh kernel-ml-5.15.6-1.el7.elrepo.x86_64.rpm
```
### 2. 修改默认内核版本
```bash
# 查看当前实际启动顺序
grub2-editenv list
# 查看内核插入顺序
grep "^menuentry" /boot/grub2/grub.cfg | cut -d "'" -f2
# 设置默认启动
grub2-set-default 'CentOS Linux (5.15.6-1.el7.elrepo.x86_64) 7 (Core)'
# 重新创建内核配置
grub2-mkconfig -o /boot/grub2/grub.cfg
# 重启服务器
reboot
# 验证当前内核版本
uname -r 
```
