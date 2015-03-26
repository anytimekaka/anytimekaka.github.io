---
layout:     post
title:      "Redis入门介绍"
subtitle:   "本文适用于Redis初学者，告诉读者：什么是Redis，为什么需要Redis，Redis有哪些特点，Redis的安装（Windows，Linux，OS），数据结构，Publication/Subscription，Configuration等"
date:       2015-03-26 22:36:00
author:     "anytimekaka"
header-img: "img/post-bg-04.jpg"
---

#目录

* 什么是Redis
* 为什么需要Redis
* Redis有哪些特点
* Redis的安装
* 数据结构
* Publication/Subscription
* Configuration
* 参考

#什么是Redis

简单来说，Redis是一种数据库，但是不是我们通常用的MySQL或者Oracle一类的**关系型数据库**。Redis是一个**基于内存的、键值对存储数据库**。是目前最流行的键值对数据库。

相对于其他键值对数据库来说，Redis的最大的不同在于：支持多种不同数据结构的值

* 字符串（一般键值对数据库只支持字符串）
* 字符串列表
* 无序、不重复的字符串集合
* 有序、不重复的字符串集合
* 键值都是字符串的哈希表

除此之外，Redis虽然作为一个内存数据库，其也支持**持久化**操作和**集群**部署。后面会对这些一一介绍。

#为什么需要Redis


#参考

[维基百科——Redis](http://zh.wikipedia.org/wiki/Redis)
