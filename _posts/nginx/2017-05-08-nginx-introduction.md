---
layout: post
title: nginx introduction
category: nginx
tags: [nginx]
---

# Nginx简介

Nginx ("engine x") 是一个高性能的HTTP和反向代理服务器，也是一个IMAP/POP3/SMTP服务器。Nginx是由Igor Sysoev为俄罗斯访问量第二的Rambler.ru站点开发的，第一个公开版本0.1.0发布于2004年10月4日。

## 为什么要使用Nginx

- 高性能

- 提升后端应用响应速度

- 提供简单的负载均衡

- 伸缩性好

- 动态更新配置

如解决了c10k问题

- 安装维护方便

在低配置服务器中也表现良好

- 简单快速上手

简单强大的配置指令

## Nginx主要特点

- 不仅是web服务器，同时也是反向代理和邮件服务器

- 模块化设计，可按需编译需要的模块

- 异步服务器

基于异步、事件的设计架构

- 反向代理和负载均衡

- 资源占用少、消耗低

- 极高的性能

- 支持htt(s)、webSocket、IMAP、POP3、SMTP等协议

- SSL终止转换

- 可扩展的监控和日志功能

- 优雅重启、无需停机



# Nginx的设计架构

## 核心指令

Nginx提供了一些指令集，通过指令集来配置Nginx的运行方式。Nginx有2种指令集，简单指令和块指令。

简单指令就是名称和参数，以分号结尾。格式为：名称 参数; 如worker_processes 1;

块指令就是许多简单指令集，并通过{}来括起来。

常用的简单指令有

```
user // 指定用户，默认值nobody

worker_process // 指定worker的进程数

error_log // 指定错误日志的路径和输出级别

pid // 指定进程号输出的文件路径
```

事件上下文

```
worker_connections // 指定worker的最大连接数

use // 指定事件模式，默认不指定，则选择最佳方式
```

http上下文

```
include // 引用其他配置文件

default_type // 设置默认type

log_format // 日志格式

access_log // 访问日志
```

## 核心模块

## 核心架构

# Nginx的应用

## web服务
## 反向代理
## 负载均衡
