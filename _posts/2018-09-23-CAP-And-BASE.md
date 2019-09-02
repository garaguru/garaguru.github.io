---
layout: post
title: 详解 CAP BASE
excerpt: 介绍 CAP 定律 和 BASE 原则
date: 2018-09-23 20:18:56
categories: 分布式
tags: 分布式
author: Garaguru
mathjax: true
---

* content
{:toc}

# CAP
所谓 CAP 定律，即一个分布式系统无法同时满足以下三种性质
![](../assets/nosql-triangle.png)

## Consistency 强一致性
对一个分布式系统中的所有机器来说，一个数据的值，是一致的，不会出现 A 机器读到这个数据是10，B机器读到这个数据是11 这种情况，这就是强一致性

>在分布式系统中，更新操作执行成功后所有的用户都应该读到最新的值，这样的系统被认为是具有强一致性的

## Availability 高可用性
每个客户端执行的操作都能在**一定时间**内得到一个返回的结果（或成功或失败），则这个系统具有高可用性

>这个一定时间，是指可以容忍的时间


## Partition Tolerance 分区容错性
在存在网络分区的情况下，分布式系统在遇到某节点或网络分区故障的时候，仍然能够对外提供满足一致性和可用性的服务

## CAP 权衡
由于无法同时满足 CAP，实际在系统中我们就必须在三者中做权衡

### CA 没有 P
这种情况在分布式系统中几乎是不存在的。首先在分布式环境下，网络分区是一个自然的事实。

因为分区是必然的，所以如果舍弃P，意味着要**舍弃分布式系统**（比如做一个单点的数据库）。那也就没有必要再讨论CAP理论了。

所以，对于一个分布式系统来说。P是一个基本要求，CAP三者中，只能在CA两者之间做权衡，并且要想尽办法提升P。

### CP 没有 A
一个保证了CP而一个舍弃了A的分布式系统，一旦发生网络故障或者消息丢失等情况，就要**牺牲用户的体验，等待所有数据全部一致了之后再让用户访问系统**。

设计成CP的系统其实也不少，其中最典型的就是很多分布式数据库，他们都是设计成CP的。在发生极端情况时，优先保证数据的强一致性，代价就是舍弃系统的可用性。

如Redis、HBase等，还有分布式系统中常用的Zookeeper也是在CAP三者之中选择优先保证CP的。

### AP 没有 C
放弃强一致性，保证**最终一致性**

要高可用并允许分区，则需放弃强一致性。一旦网络问题发生，节点之间可能会失去联系。为了保证高可用，需要在用户访问时可以马上得到返回，则每个节点只能用本地数据提供服务，而这样会导致全局数据的不一致性。

对于很多业务系统来说，比如淘宝的购物，12306的买票。都是在可用性和一致性之间舍弃了一致性而选择可用性。

你在12306买票的时候肯定遇到过这种场景，当你购买的时候提示你是有票的（但是可能实际已经没票了），你也正常的去输入验证码，下单了。但是过了一会系统提示你下单失败，余票不足。这其实就是先在可用性方面保证系统可以正常的服务，然后在数据的一致性方面做了些牺牲，会影响一些用户体验，但是也不至于造成用户流程的严重阻塞。



# BASE
BASE 理论是对 CAP 的扩展，核心思想是要**做到最终一致性，放弃强一致性**，适用于追求高可用性的场景

## Basically Available 基本可用

基本可用是指分布式系统在出现故障的时候,**允许损失部分可用性,即保证核心部分可用**。

电商大促时,为了应对访问量激增,部分用户可能会被引导到降级页面,服务层也可能只提供降级服务，这就是损失部分可用性的体现。

## Soft State 软状态

软状态是指允许系统存在中间状态,而该中间状态不会影响系统整体可用性。

分布式存储中一般**一份数据至少会有三个副本,允许不同节点间副本同步的延时**就是软状态的体现。mysql replication的异步复制也是一种体现。

## Eventual Consistency 最终一致性

最终一致性是指系统中的所有数据副本经过一定时间后,最终能够达到一致的状态。

弱一致性和强一致性相反,最终一致性是弱一致性的一种特殊情况。