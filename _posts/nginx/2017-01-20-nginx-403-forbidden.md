---
layout: post
title: nginx 403 forbidden
category: nginx
tags: [nginx]
---

Nginx 403的问题一般是目录是否存在以及是否有权限引起的问题。

## 目前是否存在

可以根据nginx的访问日志、错误日志排查访问的路径，然后确认路径是否正确。

## 权限问题

- 给目录授权

chmod 777 /data/www

- 修改nginx的启动用户,在conf配置文件中，把用户改成root

```
user root

```

## selinux访问限制

若上面的方法还是不行，则查看selinux的状态是否启动

```
# getenforce
```

若启动则关闭

临时关闭（不用重启机器）

```
# setenforce 0  // 设置SELinux 成为permissive模式
                // setenforce 1 设置SELinux 成为enforcing模式
```

永久关闭，修改配置文件需要重启机器

修改/etc/selinux/config 文件, 将SELINUX=enforcing改为SELINUX=disabled,重启机器即可
