---
title: Redis
category: 技术
tag:
- 数据库
date: 2021-06-10
excerpt: 记录学习和应用redis的过程。
---

# NoSQL数据库
## 概述
NoSQL数据库，指的是非关系型的数据库。不依赖业务逻辑的方式存储，而是以简单的key-value模式存储。因此大大增加了数据库的扩展能力。
1. 不遵循SQL标准
2. 不支持ACID
	1. 原子性：事务的一组操作，要么都执行， 要么都不执行
	2. 一致性：满足数据完整性和一致性
	3. 隔离性：多个事务之间互不影响
		
		> 事务隔离分为不同级别：读未提交、读提交、可重复读、串行化 	
	5. 持久性：一旦事务提交，即使数据库故障也不会丢失信息
4. 远超SQL的性能
## NoSQL适用场景
- 对数据进行高并发的读写
- 海量数据的读写
- 对数据高可扩展性
> NoSQL数据的存在意义就是从关系型数据库以业务逻辑为依据的存储模式，变为以性能为最优先考虑的存储方式，目的就是为了提高性能。
## NoSQL不适用场景
- 需要事务支持
- 基于sql的结构化查询存储，处理复杂的关系，即需要**即席**查询
## 常见NoSQL
1. Memcache
	- 支持的数据类型比较单一
	- 不支持持久化操作 
	- **多线程+锁**
3. Redis
	- 数据都在内存中，**支持持久化**，主要用作备份恢复
	- 除了支持简单的key-value模式，还**支持多种数据结构的存储，比如list、set、hash、zset等**
	- 一般作为**缓存数据库**辅助持久化的数据库
	- **单线程+多路IO复用**
		- 实现了多线程的效果，比memcache效率更高
4. MongoDB
	-  高性能、开源、模式自由的**文档型数据库**
	- 数据都在内存中
	- 虽然是key-value模式，但是对value（尤其是**json**）提供了丰富的查询功能
	- 支持二进制数据和大型对象
	- 可以根据数据的特点**替代RDBMS**，成为独立的数据库。或者配合RDBMS，存储特定的数据。
# Redis安装
1. 安装gcc
2. redis官网找下载链接，用wget 下载安装包并解压
3. 解压目录下编译`make`然后安装`make install`
4. 安装后，将配置文件redis.conf复制到/etc下，并修改启动模式为后台启动，即修改redis.conf中`daemonize no`为yes
5. 启动redis
	- redis-server  /etc/redis.conf 以该配置启动（后台启动）
	- 打开客户端redis-cli
	- 查看进程ps -ef | grep redis
	- 测试验证：ping
		- 得到pong回复，则说明运行正常 
6. 关闭redis
	- redis cli shutdown
	- 或者查看进程号 kill -9 xxx

# Redis常用五大数据类型
## 库命令
- 默认16个数据库，下标从0开始，默认使用0号库
	- 使用`select <dbid>`来切换数据库，如select 1
	- 统一密码管理，所有库同样密码
- `dbsize`查看当前数据库的key的数量
- `flushdb` 清空当前库
- `flushall` 通杀所有库
***
## 五大数据类型
### 键的基本操作
- `keys*` 查看当前库所有key （匹配 `keys *1`）
- `exists key` 判断某个key是否存在
- `type key` 查看你的key是什么类型
- `del key` 删除执行的key数据
- `unlink key` 根据value选择非阻塞删除
	- 仅将keys从keyspace元数据中删除，真正的删除会在后续异步操作
- `expire key 10` 10秒种：为给定的key设置过期时间 
- `ttl key` 查看还有多少秒过期，-1表示永不过期，-2表示已过期 
### 字符串String
#### 简介
- String是Redis最基本的数据类型，它是**二进制安全的**（意味着redis的String可以包含任何数据，比如jpg图片或者序列化的对象）
- 一个Redis种字符串value最多可以是**512M**
#### 常用命令
- `set <key><value> `添加键值对
	- `*NX` 当数据库种key不存在时，可以将key-value添加进数据库
	- `*XX` 当数据库种key存在时，可以将key-value添加进数据库，与NX参数互斥
	- `*EX` key的超时秒数
	- `*PX` key的超时毫秒数，与EX互斥
- `get <key>` 查询对应键值
- `append <key><value>`将给定的`<value>`追加到原值的末尾
- `strlen <key>` 获得值的长度
- `setnx <key><value>` 只有在key不存在时，设置key的值
***
- `incr` 数字加一 
- `decr` 数字减一
- `incrby/decrby <value> <步长> ` 数字增减自定义长度
- **增减操作具有原子性**，即原子操作不会被线程调度机制打断。Redis单命令的原子性主要得益于Redis的单线程（单线程+IO多路复用）
	 - Java种的`i++`不是原子操作 
***
- `mset <key1><value1> <key2><value2>...` 同时设置多个key-value
- `mget <key1><key2>...` 同时获取多个value
- `msetnx <key1><value1> <key2><value2>...` 同时设置多个key-value，当且仅当所有给定key都不存在
- **以上三个操作具有原子性，有一个失败则都失败**
***
- `getrange <key> <起始位置><结束位置>` 获得某范围内的值（左右都是闭区间）
- `setrange <key><起始位置><value>` 用`value`覆盖从`起始位置`开始的值（**索引从0开始**）
***
- `setex <key><过期时间><value>` 设置键值的同时，设置过期时间，单位秒
- `getset<key><value>` 以旧换新，设置新值同时获得旧值
#### 数据结构
String的数据结构为简单动态字符串（Simple Dynamic String，缩写SDS）。是可以修改的字符串，内部结构类似Java的ArrayList，采用预分配冗余空间的方式来减少内存的频繁分配。
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210623161805293.png)
当字符串长度小于1M时，扩容时**加倍现有的空间**，超过1M，扩容时只会多扩1M的空间。字符串的最大长度是512M。
### 列表list

#### 简介
Redis列表是简单的字符串列表，按照插入顺序排序。可以添加一个元素到列表的头部（左边）或者尾部（右边）。它的底层实际上是**双向链表**，对两端的操作性能很高，通过索引下标的操作中间的节点性能比较差。
#### 常用命令
- `lpush/rpush <key><value1><value2><value3>...` 从左边/右边插入值
- `lpop/rpop <key>` 从左边/右边吐出一个值。
- `rpoplpush <key1><key2>` 从`key1`列表右边吐出一个值，插入到`key2`列表左边
- `lrange <key><start><stop>` 按照索引下标获得元素（从左到右）
	- `lrange <key> 0 -1` 获取所有元素 
- `index <key><index>` 按照索引下标获得元素（从左到右）
- `llen <key>` 获得列表长度
***
- `linsert <key> before/after <value> <newvalue>` 在`<value>`的前边或后边插入值
- `lrem <key> <n> <value> ` 从左边删除n各value
- `lset <key> <index> <value>` 将列表key下标为index的值替换为value
#### 数据结构
List的数据结构为快速链表quickList。
首先在列表元素较少的情况下会使用**一块连续的内存空间， 这个结构是ziplist**，即压缩列表。它将所有的元素紧挨着一块存储，分配的是一块连续的内存。**当数据量比较多的时候才会改成quicklist**。
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210623165456847.png)
Redis将链表和ziplist结合起来组成quicklist。也就是将多个ziplist使用双向指针串起来。这样既满足了快速的插入删除性能，又不会出现太大的空间冗余。
### 集合set
#### 简介
与list类似，但是具有**去重**功能。
Redis的Set是string类型的无序集合。它底层是一个value为null的哈希表，所以添加、删除、查找的**复杂度都是O（1）**
#### 常用命令
- `sadd <key><value1><value2>...` 将一个或多个member元素加入集合key种，已经存在的member元素被忽略
- `smembers <key>`取出该集合的所有值
- `sismember <key> <value>` 判断集合key是否含有value值
- `scard<key>` 返回该集合的元素个数
- `srem <key><value1><value2>...` 删除集合中的某个元素
- `spop <key>` 随机从该集合种吐出一个值
- `srandmember <key> <n>` 随机从该集合种取出n个值。不会删除成员。
- `smove <source> <des> value` 把集合中一个值移动到另一个集合
- `sinter <key1> <key2>` 返回两个集合的**交集**元素
- `sunion <key1><key2>` 返回两个集合的**并集**元素
- `sdiff <key1><key2> `返回两个集合的**差集**元素（在key1中，不在key2中） 
#### 数据结构
Set数据结构是dict字典，字典是用哈希表实现的。
### 哈希Hash
#### 简介
Redis Hash是一个键值对集合，是一个stirng类型的field和value的映射表，hash特别适合用于存储对象，类似Java中的Map<String,Object>。
<img src="https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210623171411979.png" width=50%>
#### 常用命令
- `hset <key><field><value>`给key集合中field键赋值
- `hget <key><field>` 从集合key中的field中取值value
- `hmset <key><field1><value1><field2><value2>...` 批量设置hash的值
- `hexists<key1><field>` 查看哈希表key中，给定域field是否存在
- `hkeys <key>` 列出该hash集合的所有field
- `hvals <key>` 列出该hash集合的所有value
- `hincrby <key><field><increment>` 为哈希表key中的域field的值加上增量1 -1
- `hsetnx <key><field><value>` 将哈希表key中的域field的值设置为value，当且仅当域不存在 
#### 数据结构
Hash类型对应的数据结构是两种：ziplist（压缩列表），hashtable（哈希表）。当field-value长度较短且个数较少时，是用ziplist，否则是用hashtable
### 有序集合Zset (sorted set)
#### 常用命令
- `zadd <key><score1><value1><score2><value2>...` 将一个或多个member元素及其score值（用于排序）添加到有序集合key中
- `zrange <key> <start> <stop> [withscores]` 返回有序集合key中，下标在start和stop之间的元素(0 -1展示全部），带withscores，可以让分数一起和值返回到结果集中。
- `zrangebyscore key minmax [withscores] [limit offset count]` 返回有序集合key中，所有score值介于min和max之间（闭区间）的成员。有序集成员按score值递增次序排列
- `zrevrangebyscore key maxmin [withscores] [limit offset count]` 同上，改为从大到小排列
- `zincryby <key><increment><value>` 为元素的score加上增量
- `zrem <key><value>` 删除该集合下，指定值的元素
- `zcount <key> <min> <max> ` 统计集合，分数区间内的元素个数
- `zrank <key> <value>` 返回该值在集合中的排名，从0开始

案例：如何利用zset实现文章访问量的排行榜
#### 数据结构
SortedSet(Zset)是Redis提供的一个非常特别的数据结构，一方面它等价于Java的数据结构`Map<String,Double>`，可以给每个元素value赋予一个权重score，另一方面它又类似于TreeSet，内部的元素会按照权重score进行排序，可以得到每个元素的名次，还可以通过score的范围来获取元素的列表
zset底层使用了两个数据结构
1. hash，hash的作用就是关联value和权重score，保障元素value的唯一性，通过元素value可以找到相应的score值
2. 跳跃表，跳跃表的目的在于给元素value排序，根据score的范围获取元素列表
![在这里插入图片描述](https://yfx-blog-image.oss-cn-hangzhou.aliyuncs.com/img/20210623200814255.png)
# Redis6的发布和订阅
## 什么是发布和订阅
Redis发布订阅(pub / sub)是一种**消息通信模式**：发送者（pub）发送消息，订阅者（sub）接收消息。
Redis客户端可以订阅任意数量的频道。
# Redis6新数据类型
## Bitmaps
## HyperLogLog
## Geospatial

# Jedis操作Redis6