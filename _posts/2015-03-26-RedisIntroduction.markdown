---
layout:     post
title:      "Redis入门介绍"
subtitle:   "本文适用于Redis初学者，告诉读者：什么是Redis，为什么需要Redis，Redis有哪些特点，Redis的安装（Windows，Linux，OS），数据结构，Publication/Subscription，Configuration等"
date:       2015-03-26 22:36:00
author:     "anytimekaka"
header-img: "img/post-bg-04.jpg"
---

#1. 目录

* 什么是Redis
* Redis有什么特点
* Redis的安装
* 运行、连接和使用
* 数据结构
* Publication/Subscription
* Configuration
* 参考

#2. Redis是什么

简单来说，Redis是一种数据库，但是不是我们通常用的MySQL或者Oracle一类的**关系型数据库**。Redis是一个**基于内存的、键值对存储数据库**。是目前最流行的键值对数据库。

#3. Redis有什么特点

相对于其他键值对数据库来说，Redis的最大的不同在于：支持多种不同数据结构的值

* 字符串（一般键值对数据库只支持字符串）
* 字符串列表
* 无序、不重复的字符串集合
* 有序、不重复的字符串集合
* 键值都是字符串的哈希表

除此之外，Redis虽然作为一个内存数据库，其也支持**持久化**操作和**集群**部署。后面会对这些一一介绍。

#4. Redis的安装

Redis官方只提供Linux版本，没有Windows版本。实际部署中，也都是在Linux服务器环境下，所以重点介绍Linux下的安装和配置。

##4.1 Linux下安装Redis

使用源文件安装：

	wget http://redis.googlecode.com/files/redis-2.4.6.tar.gz
	tar -zvxf redis-2.4.6.tar.gz
	cd redis-2.4.6
	make

redis的版本可以自己选择。使用源码安装过程中，有可能因为长城防火墙的问题，无法获取到安装源码。这时可以使用一个代理。作者本人就创建了一个七牛存储空间用于代理。下载地址是http://7xi9yb.com1.z0.glb.clouddn.com/redis-2.8.19.tar.gz，版本：2.8.19。

##4.2 Windows下安装Redis

Github上有人发布Windows版本的Redis，我也测试过，能够正常使用，方便初学者使用。下载地址[https://github.com/dmajkic/redis/downloads](https://github.com/dmajkic/redis/downloads)

下载之后，解压。能看到有两个文件夹，win32 & win64，根据系统情况选择需要的，现在基本都是64位机器。文件夹里面有redis-server.exe和redis-cli.exe两个关键的程序，分别表示服务器程序和客户端程序。

#5. 运行、连接、使用

Redis服务的启动，只需要运行redis-server即可。Windows下为redis-server.exe文件，Linux下运行./redis-server。成功运行之后，能看到一些基本信息，例如Redis使用的端口等，也能看到客户端连接提示。

下图是Windows平台下运行redis服务器程序redis-server.exe。从命令行提示中我们可以看到一部分有用的信息。

<img src = "http://anytimekaka.github.io/img/postimg/201504111009.PNG"/>

好了！启动好了Redis服务，后面就可以来体验如何连接和使用了。

##5.1 Redis初体验

Redis中也有一个简单的客户端工具，redis-cli，可以方便学习和调试。如果没有修改默认设置，客户端可以自动连上服务器。

下面让我们来稍微体验一下Redis，感受一下：
下图是使用客户端redis-cli.exe，并添加了name字段。

<img src = "http://anytimekaka.github.io/img/postimg/201504111011.PNG"/>

从这个简单的示例中，我们也能看出，如Redis一类的NoSQL数据库与传统的关系型数据库的区别：不使用SQL语言，不以表结构存储。

在初学者学习过程中，我们可以使用上述这种简单的方式进行练习。实际应用中，都是以程序调用的形式来使用。所以，后面我们编写简单的程序来访问Redis。（这种形式其实类似使用数据库驱动来访问数据库）

##5.2 驱动程序访问Redis

关于如何在项目中使用redis，就需要使用特定语言的客户端驱动程序了。目前，针对每种语言基本都有了redis驱动程序可以使用，有人专门做了[总结在这里](http://redis.io/clients)。

此处准备编写一个简单程序（Java + C++)，用于说明程序的调用。

#6. 数据结构

前面的示例中，我们实际只用到了一种数据结构String。Redis提供了5种数据结构：

* 字符串（String）
* 散列（Hash）
* 列表（List）
* 集合（Set）
* 分类集合（Sorted Set）

在详细介绍每种数据结构之前，我们来回顾一下之前是如何使用client来设置和访问数据的：

	set name James
	get name

第一节中我们就介绍过，Redis是基于**键值对**存储的。此处使用set命令向数据库中添加了"name-James"这样一对数据信息。Redis中普遍使用如下形式的命令来查询和设置数据：

	command key value	//设置、添加数据
	command key			//根据key查询

注意：此处没有用set/get，这是因为：Redis使用不同的命令来管理不同的数据结构。例如对于String，使用的是get/set，而对于Hash，则使用hset/hget。

下面分别介绍每种数据结构。

##6.1 字符串

字符串是Redis中最基本的数据结构。除了之前介绍的set/get之外，还有一些常用的操作：

	strlen name		//获取name值的长度
	getrange name 2 5	//获取2-5区间内的value值
	append name " Carl"		//在name后面添加字符串

字符串的是最常用的数据结构，操作命令有很多，这里不一一列举说明。详细可参考[Redis文档--String](http://redis.io/commands#string)

##6.2 散列（Hash）

散列数据数据结构本身就是以一种键值对的形式保存数据，与String的区别在于：其对String做了一层封装，使得一个散列对象可以保存一组String数据。（官方文档中引入了一个Field的概念，即指散列中的键）

	hset user name James
	hset user age 25

散列user中保存了两个field：name和age，以及对应的value。一些其他的操作：

	hgetall user		//获取user中的所有元素
	hkeys user			//获取user中所有field
	hdel user age		//删除user中age field

关于散列的更多命令说明，请参考[Redis文档--散列](http://redis.io/commands#hash)。

##6.3 列表（List）

列表结构存储的是一组值，列表可以维护值的顺序，提供**基于索引**的操作。

	lpush citys shanghai
	lpush citys beijing
	lpop citys	//result:beijing
	//这两个命令一起使用，使得List具有栈的特征

	rpush citys guangzhou
	lindex citys 3	//根据index获取值
	
Redis文档中说，自2.4版本之后，lpush等命令支持批量插入，本人在做以上测试的过程中，使用的是Windows2.4版本，并不支持批量插入，Linux平台下应该没有这样的问题。

关于列表的更多命令说明，请参考[Redis文档--列表](http://redis.io/commands#list)

##6.4 集合（Set）

集合用于存储一组**不重复**的值，Redis提供了很多基于集合的操作，例如交集和并集等。

	sadd countrys American China
	sismember countrys China	//判断是否是集合成员 O(1)时间
	scard countrys		//集合中元素数量
	sinter oneSet anotherSet	//获取两个集合的交集

对于需要根据值进行操作的地方，或者需要求交集和并集的情况下，集合是很好的选择。关于集合的更多命令说明，请参考[Redis文档--集合](http://redis.io/commands#set)

##6.5 分类集合（Sorted Set）

分类集合相对复杂，其与集合的唯一区别就是：为每个集合中的成员添加了一个**score标记**。可以根据标记对集合元素进行**排序和秩划分**。

	zadd points 78 Carl 92 Xiaoming 89 James
	zcount points 80 90	//[80,90)之间的元素数量
	zrank points James	//result:1 默认从小到大排序，第一个为0

秩：元素在排序好的集合中的次序。

关于分类集合的更多命令说明，请参考[Redis文档--分类集合](http://redis.io/commands#sorted_set)

# Publication/Subscription
# Configuration

#参考
* [redis command](http://redis.io/command#)
* [redis中文官方网站](http://redis.cn/)
* [The little redis book](https://github.com/JasonLai256/the-little-redis-book/blob/master/cn/redis.md)
* [维基百科——Redis](http://zh.wikipedia.org/wiki/Redis)







