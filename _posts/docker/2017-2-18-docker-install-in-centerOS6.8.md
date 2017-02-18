---
layout: post
title: docker install in centerOs 6.8
category: docker
tags: [docker]
---

# docker安装条件

docker容器运行于64位架构平台，内核版本为2.6.32-431及以上（即>=CentOS 6.5，运行docker时实际提示3.8.0及以上），需要注意的是CentOS 6.5与7.0的安装是有一点点不同的，CentOS-6上docker的安装包叫docker-io，并且来源于Fedora epel库，这个仓库维护了大量的没有包含在发行版中的软件，所以先要安装EPEL，而CentOS-7的docker直接包含在官方镜像源的Extras仓库（CentOS-Base.repo下的[extras]节enable=1启用）。下面将介绍在centerOs 6.8中的安装步骤。

# 添加EPEL资源库

```
rpm -iUvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm

yum update -y
```

# 安装docker-io

```
yum -y install docker-io

```

# 启动docker

```
service docker start
```

# 设置开机启动

```
chkconfig docker on
```
