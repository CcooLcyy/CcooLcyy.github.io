---
title: mqtt
date: 2024-04-28 07:45:18
tags:
---
MQTT(Message Queuing Telemtry Transport，消息队列遥测传输协议)，是一种基于发布/订阅模式的轻量级通讯协议，该协议构建在TCP/IP协议上。MQTT的优点是用**极少的代码**和**有限的带宽**为连接远程设备提供**实时可靠**的消息服务。MQTT是一种应用层协议。

## 发布订阅模式

在MQTT服务模型中有三种角色：
1. 发布者（publisher）: 发布者将消息通过代理人发送。
2. 代理人（broker）: 进行消息的转发。
3. 订阅者（subscriber）: 接受消息。

模型可以与微博关注进行类比，发布者和订阅者订阅同一个主题（topic），发布者对于一个主题向代理人发送一个消息，代理人会知道都有哪些设备（订阅者）订阅了这个主题，代理人就会将发布者发送的消息转发给订阅者。