---
layout: post
title: NSQ的设计思想
categories: [MQ]
tags: [MQ,NSQ]
---

NSQ的设计思想

NSQ继承于simplequeue，是一个实时分布式消息平台，设计目标有：

- 支持拓扑结构，实现高可用和消除单点故障

- 满足更强的消息投递的可靠性

- 限制单个进程的内存上限（超过的写入硬盘）

- 极大简化生产者和消费者的配置要求

- 提供简单直接的升级方式

- 提高效率

## 简化配置和管理

单个nsqd实例一次可以处理多个消息流。消息流被称为“topics”，一个topic有一个或者多个“channels”。每个channel会接收来自topic的所有消息。实际上，一个channel对应下游个服务消费的一个topic

topics和channels并不是预先配置的。topics是在首次发布或者订阅的时候创建的，channels是在首次订阅的时候创建的。

topics和channels的所有缓存数据都相互独立，目的是为了防止一个“慢”消费者造成消息积压而影响其他topic或channel。

一个channel通常有多个消费者连接，假如所有消费者都是在准备接收消息状态，每个消息会被随机投递到消费者中。

## 简单直接的升级方式

## 消除单点故障（SPOF）

## 消息投递的可靠性

## 内存限制

## 效率

## Go

