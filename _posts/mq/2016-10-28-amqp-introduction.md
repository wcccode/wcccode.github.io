---
published: true
title: AMQP介绍
layout: post
tags: [MQ]
categories: [MQ]
---

AMQP高级消息队列协议介绍

# 简介

相比非开源，开源应用有免费、快速迭代周期以及强大的社区支持。在应用选择时，作为许多开发者、产品管理者和一般用户强有力的选择。

开源应用成功的一个关键要素是在持续迭代的过程中，可以有来自多方的观点和建议，来共同优化应用功能，尽可能的和大家一起努力使应用更完善、可用。

本文将介绍一些新的东西帮助开发者来了解高级消息队列协议（AMQP）。AMQP是一个开放（技术）标准的中间件，在不同进程、应用、甚至无关系统需要进行相互通信
和消息传递时作为中间人，进行消息传递。

# 技术标准和开放标准

## 技术标准

技术标准由规则、规范、定义等组成，应用的开发和应用以及其他技术系统来共同制定。技术标准由定义好的方法和过程来制定，作为理论框架。

## 开放标准

在技术系统领域，对于应用的实现和开发，开放标准是一些可以被修改、免费使用的规则指令。开放标准可以被一个“开放组织”通过一个“开放过程”来创建和修改，这取决于制定他们的人。

# 开放系统互联

开放系统互联Open System Interconnection (OSI)是一个ISO标准，制定与1970年，目的是为了规范不同的网络方式。电脑系统通过OSI进行通信，实现系统的共同工作。

OSI标准由7层框架组成：

1.物理层：OSI标准的物理基础（如硬件）

2.数据链路层：负责网络节点的数据传输

3.网络层：负责传输的路由

4.传输层：确保数据传输的可靠性，数据流（速率）控制

5.会话层：负责管理应用间的会话

6.表示层：负责处理和展示数据的处理

7.应用层：设置和确保系统的共识，来满足系统间的通信

# 应用层

AMQP属于应用层，处于开放系统互联的顶层。如果我们仔细思考下，会发现应用层是唯一一个和用户进行交互并且定义了进程间（应用间）通信发生的地方。

除了AMQP，这里还有一些其他例子：

- IRC

- DNS

- FTP

- IMAP

- SSH

- 其他

# 通信协议

每个通信协议都由规则和清晰的规范定义，在不同系统之间形成统一的语言，使系统间能够进行通信，无需关注各自系统的内部实现。

协议有一些属性如数据格式、协议使用者定义、路由和流（速率）控制。

# 高级消息队列协议AMQP

高级消息队列协议AMQP为客户端（Client）和中间人（Broker）建立了互通性。目的就是为了使广泛的应用和系统能够一起工作，不管他们内部的设计，实现在工业领域企业消息的标准化。

AMQP包含网络通信方式定义和消息中间人应用工作的方式，规范如下：

- 消息中间人应用的路由和消息存储的方式，以及组件如何工作的规范

- 客户端和中间人之间的通信协议

# AMQP产生和应用的原因

在AMQP产生之前，不同开发商之间有着不同的消息中间人和传输应用。然而，有一个严重的问题是缺少互通性。没有一个简单的方式使用他们的系统能够一起工作。唯一的方式使不同系统使用不同的协议，通过引入额外的消息转换层-消息桥（messaging bridge）。这些系统使用信息桥能够接受其他系统的信息，能连接多个不同的消息系统。

AMQP通过定义明确的规范和说明，建立一个共同的规范来实现所有消息队列和中间人应用间的工作和互通性。

# AMQP的应用场景

无论何时，当应用、进程之间需要可靠、安全的消息传递时，就可以考虑实现AMQP的消息中间人解决方案。

## AMQP基本功能

- 可靠的消息投递

- 响应和确保消息的投递

- 消息确认（ACK）

## 高级功能

- 监控和全局共享更新

- 能够连接其他系统

- 允许服务器快速响应请求，把耗时任务放到后续处理

- 分发消息给多个消费者

- 离线客户端能够在重启后抓取数据

- 为系统提供完全异步的能力

- 增加应用的可靠性和在线时间

# AMQP的集合和技术术语

理解和使用AMQP要熟悉许多不同术语和技术。下面介绍一些关键部分：

- Broker（Server）：一个实现了AMQP模型的应用。能接受客户端连接请求，消息的路由和缓存队列等

- Message：数据传输、路由的基本单元，包含有效负载（payload）和消息熟悉

- Consumer：消费队列中发布者发布的消息

- Producer：发布信息到队列

注意：消息的payload部分并没有在AMQP中定义。所以各种不同的数据类型可以在协议中传输

# AMQP的主要组件

AMQP模型定义了消息如何接收、路由、存储、队列，以及应用如何处理这些任务。这些依赖于如下定义的组件：

- Exchange：broker的一部分，负责接收和路由消息到队列中

- Queue：相关消息的命名实体，消费者消息的来源之处

- Bindings：把信息从exchanges分发到queues的规则

# AMQP的Broker的工作方式

在AMQP中，Broker负责接收消息m，并路由到相应的地方

APPLICATION-------------> EXCHANGE-----------> TASK LIST----------> WORKER

[DATA]------------------> [DATA] ------------> [D]+[D][D][D] -----> [DATA]
   
Publisher---------------> EXCHANGE-----------> Queue -------------> Consumer
 
# AMQP的Exchange的工作方式

在接收到发布者消息后，Exchange会处理并路由消息到一个或多个队列上。信息路由方式取决于Exchange的类型，目前有四种Exchange类型：

- 直接路由（Direct Exchange）

直接路由基于路由密钥（routing keys）分发消息到队列中。Routing keys作为额外的数据定义，设置消息分发到何处。直接型的典型案例是用于负载均衡。

- 分散路由（Fanout Exchange）

分散路由完全忽略routing keys，直接分发消息到所有的队列中。分散型经常用于分发消息给多个客户端，类似通知所有人，如：

1.共享消息（如聊天服务）和更新（如新闻）

2.应用状态（如配置）

- 话题路由（Topic Exchange）

话题路由主要用于发布/订阅模式（pub/sub）。这种类型的routing keys会和队列一起绑定到Exchange上，用于消息路由的匹配和发送。

无论何时都需要有消费者参与，topic Exchange分发消息是基于routing keys和模式。

- 头部路由（Header Exchange）

头部路由由额外的消息属性组成，而不是使用routing keys来路由消息到队列。能够使用除了strings外的其他数据类型，使路由的机制有更多可能，但是和直接路由有点类似。

# AMQP的Broker 和 E-Mailing的区别

邮件由发送消息（publishing）组成，通过邮件服务器（Broker）去接收和处理消息到对应的邮箱中（queue），最后跟着请求获取信息（consumer）。


原文：[https://www.digitalocean.com/community/tutorials/an-advanced-message-queuing-protocol-amqp-walkthrough](https://www.digitalocean.com/community/tutorials/an-advanced-message-queuing-protocol-amqp-walkthrough)

# 相关资料

[https://netprototalk.wordpress.com/2015/10/01/amqp-as-a-network-protocol-2/](https://netprototalk.wordpress.com/2015/10/01/amqp-as-a-network-protocol-2/)

[https://paolopatierno.wordpress.com/2015/08/30/amqp-isnt-so-scary-if-you-know-how-to-start/](https://paolopatierno.wordpress.com/2015/08/30/amqp-isnt-so-scary-if-you-know-how-to-start/)

[http://www.drdobbs.com/open-source/io-multiplexing-scalable-socket-servers/184405553](http://www.drdobbs.com/open-source/io-multiplexing-scalable-socket-servers/184405553)

[https://onedrive.live.com/view.aspx?resid=123CCD2A7AB10107!732016&ithint=file%2cpptx&lor=shortUrl&app=PowerPoint](https://onedrive.live.com/view.aspx?resid=123CCD2A7AB10107!732016&ithint=file%2cpptx&lor=shortUrl&app=PowerPoint)
