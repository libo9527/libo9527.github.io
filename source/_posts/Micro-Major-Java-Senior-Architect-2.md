---
title: Micro Major-Java Senior Architect 2
description: 微专业 - Java 高级开发工程师
comments: false
hidden: false
top: false
date: 2020-10-20 16:40:23
categories:
tags:
---

<img src="https://www.forknowledge.icu/gallery/thumbnails/javagcs.png" width="100%"/>

<!-- more -->

## Redis

### 常用命令

### 数据结构

#### GEO

#### Stream

### 持久化

#### RDB

#### AOF

### 内存管理

#### 内存分配

- String 类型的 value 最大可存储 512M。
- List 类型，元素个数最多 2^32-1
- Set 类型，元素个数最多 2^32-1
- Hash 类型，键值对个数最多 2^32-1

#### 内存压缩

#### 过期数据

##### 主动处理

##### 被动处理

#### 内存挥手策略

##### LRU 算法

##### LFU 算法

### 主从复制

#### 主从复制流程

#### 主从复制应用场景

- 读写分离

### 哨兵高可用机制

1. 哨兵如何知道 Redis 主从信息（自动发现机制）
2. 什么是 master 主观下线
3. 什么是可观下线
4. 哨兵之间如何通信（哨兵之间的自动发现）
5. 哪个哨兵负责故障转移？（哨兵领导选举机制）
6. slave 选举机制
7. 最终主从切换的过程

### Redis 集群分片存储

1. 增加了 slot 槽的计算，是不是比单机性能差？
2. redis 集群大小，到底可以装多少数据？
3. 集群节点间是怎么通信的？
4. ask 和 moved 重定向的区别
5. 数据倾斜和访问倾斜的问题
6. slot 手动迁移怎么做？
7. 节点之间会交换信息，传递的消息包括槽点信息，带来宽带消耗。
8. Pub/Sub 发布订阅机制
9. 读写分离

#### 非官方集群方案

Codis

### Redis 监控

#### Monitor 命令

#### Info 命令

#### 图形化监控工具

##### RedisLive

## Memcached

### XMemcached

> [killme2008/xmemcached: High performance, easy to ... - GitHub](https://github.com/killme2008/xmemcached)

### Memcached 应用场景

1. 数据查询缓存
2. 计数器
3. 乐观锁
4. 防止重复处理

### Memcached 内存管理

### Memcached 缓存策略

#### LRU

#### 分段 LRU

#### LRU Crawler

### 集群方案

Twemproxy

### 分布式算法

##### Hash + 取模

##### 一致性 Hash 算法

### 缓存雪崩

### 缓存击穿

查询必然不存在的数据，请求透过 Redis，直击数据库。