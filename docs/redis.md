# Redis

## Nosql

### 为什么要使用NoSQL

#### 1.单机MySQL的时代

app ------》DAL------》MySQL



90年代，一个基本的网站访问量一般不会太大，单个数据库完全够用

那个时候，更多地使用静态网页html，服务器压力不大



网站的瓶颈：

1.如果数据量太大，一个机器放不下

2.数据的索引（B+Tree），一个机器的内存也放不下

3.访问量（读写混合），一个服务器也承受不下

 

只有出现以上三种情况之一，那么必须要升级



#### 2.Memcached（缓存）+MySQL+垂直拆分（读写分离）

网站80%的情况都在读，每次都去查询数据库非常麻烦，服务器压力也大。为了减轻数据的压力，我们可以使用缓存来保证效率。

发展过程：优化数据结构和索引---》文件缓存（IO）---》Memcached（当时最热门的技术）

![image-20200614125948835](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614125948835.png)

#### 3.分库分表+水平拆分+MySQL集群

==本质：数据库（读+写）==

解决写的问题

早些年MyISAM：表锁，十分影响效率，高并发下会出现严重的锁问题

之后的InnoDB：行锁

慢慢的开始使用分库分表来解决写的压力！MySQL在那个年代推出了表分区，并没有多少公司使用！

MySQL的集群，满足了那个年代几乎所有的需求

![image-20200614132117224](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614132117224.png)

#### 4.最近的年代

技术爆发：2010-2020这十年之间，世界已经发生了翻天覆地的变化（定位，音乐热榜）

MySQL等关系数据库已经不够用了。数据量太大，变化快

MySQL如果用来存储一个较大的数据，博客，图片，视频...压力太大，效率低，这时如果有一种专门的数据库来处理，就能减轻MySQL的压力

大数据的IO压力下，表几乎无法更改



#### 5.目前一个最基本的互联网项目

![image-20200614134632231](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614134632231.png)



#### 6.为什么要用NoSQL

用户的个人信息，社交网络，地理位置，用户自己产生的数据，用户日志爆发式增长

这时我们就需要使用NoSQL数据库了



### 什么是NoSQL

NoSQL = Not Only SQL （不仅仅是SQL）

泛指非关系型数据库，随着web2.0互联网的诞生！传统的关系型数据库很难对付web2.0时代，尤其是超大规模的高并发社区，爆发出很多难以对付的问题，这时就需要NoSQL了，NoSQL在当今大数据环境下发展迅速，当下必须掌握

用户的个人信息，社交网络，地理位置，自己产生的数据...，这些数据类型不需要一个固定的格式，不需要多余的操作就可以横向扩展

* Map<String,Object> （其中的一种）  
  * 以键值对来控制



#### NoSQL特点

1.方便扩展（数据库之间没有关系，很好扩展）

2.大数据量高性能（Redis，一秒写8万次，读11万次，NoSQL的缓存(记录级)，是一种细粒度的缓存，性能会比较高）

3.数据类型是多样型的（不需要事先设计数据库！随取随用！）（如果是数据库量十分大的表，很难设计）

4.传统的 RDBMS（Relational Database Management System） 和 NoSQL

```sql
# 传统的 RDBMS
-- 结构化组织
-- 数据和关系都存储在单独的表中  （row column）
-- 数据定义语言（Data Definition Language）
-- 严格的一致性
-- 基础的事务
-- ...
```

```sql
# NoSQL
-- 不仅仅是数据
-- 没有固定的查询语言
-- 键值对存储，列存储，文档存储，图形数据库（社交关系）
-- 最终一致性（过程无需严格制约）
-- CAP定理 和 BASE （异地多活）
-- 高性能，高可用，高可扩
-- ...
```



#### 了解：3V + 3高

* 大数据时代的3V：

1. 海量Volume
2. 多样Variety
3. 实时Velocity

* 互联网需求的3高：

1. 高并发
2. 高可扩（扩展机器）
3. 高性能



#### 实践

真正的公司实践： RDBMS + NoSQL 

技术没有高低之分，而取决于如何使用



## 阿里巴巴演进分析

![image-20200614150122175](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614150122175.png)

对于架构问题，没有什么是加一层解决不了的

```bash
# 1. 商品的基本信息
	名称，价格，商家信息 
解决了关系型数据库的问题 # 关系型数据库  MySQL/Oracle （淘宝早年就去IOE【去掉IBM的小型机、Oracle数据库、EMC存储设备】）
				    #	（王坚 -  推荐文章 ： 阿里云的这群疯子 https://yq.aliyun.com/articles/653511  ）
	阿里巴巴内部的MySQL 和我们平时使用的不是一个
# 2. 商品的描述、评论（文字较多）
	文档型数据库 ： MongoDB
# 3. 图片
	- 分布式文件系统  FastDFS
	- Google的		GFS
	- Hadoop		HDFS
	- aliyun	    OSS
# 4. 商品的关键字（搜索）
	- 搜索引擎  solr ， elastic search
    	- 淘宝	ISerach  （多隆）
# 5. 商品热门的波段信息
	- 内存数据库
	- Redis  Tair,memache...
# 6.商品的交易，外部的支付接口
	- 三方交易
```

一个看似简单的网页背后的技术可能很复杂

大型互联网应用问题：

* 数据类型太大
* 数据源繁多，经常重构
* 数据要改造的问题（解决：统一的数据服务层 UDSL）
  * 解决问题：

![image-20200614153639990](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614153639990.png)

![image-20200614153918008](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614153918008.png)

![image-20200614154001430](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614154001430.png)



## NoSQL的四大分类

**KV键值对：**

* 新浪：==Redis==
* 美团：Redis + Tair
* 阿里、百度：Redis + memcache



**文档型数据库**（BSON格式：二进制的JSON）：

* ==MongoDB==（一般必须掌握）
  * MongoDB：基于分布式文件存储的数据库（C++编写），主要用于处理大量的文档
  * MongoDB是一个介于 RDBMS 和 NoSQL之间的产品！MongoDB是非关系型数据库中功能最丰富，最像关系型数据库的

* ConthDB



**列存储数据库：**

* ==HBase==
* 分布式文件系统



 **图关系数据库**：不是存图形的，存放的是关系（如：朋友圈的社交网络，广告推荐）

* ==Neo4j==
* InfoGrid





## Redis入门

### 概述

#### Redis是什么	

* Redis（==Re==mote ==Di==ctionary ==S==erver )，即远程字典服务
* 是一个**开源**的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型
* Key-Value数据库
* 并提供多种语言的API
* 也被称为结构化数据库



#### Redis能干嘛

1.内存存储，持久化，内存中是断电及失，这需要持久化（rdb，aof）

2.效率高，可以用于高速缓存

3.发布订阅系统

4.地图信息分析

5.计时器，计数器（浏览量）

...



#### 特性

1.多样的数据类型

2.持久化

3.集群

4.事务

...



#### 学习参考：

1.官网： https://redis.io/

2.中文网：http://www.redis.cn/

3.GitHub

4.不建议在windows下使用 Redis，一般在LInux



### windows 安装：

1.安装  https://github.com/microsoftarchive/redis/releases/tag/win-3.2.100

2.解压到常用环境目录

![image-20200614163423665](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614163423665.png)

3.开启Redis（点击server）

默认端口：  6379

![image-20200614163716780](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614163716780.png)



4.使用redis客户端连接Redis

虽然windows下使用看起来很简单，但是Redis推荐我们使用Linux去开发，这里只是简单的了解下

![image-20200614164502574](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614164502574.png)



![image-20200614164840126](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614164840126.png)



### linux 安装：

1.下载安装包

![image-20200614165419048](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614165419048.png)

2.上传安装包到linux

![image-20200614171734796](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614171734796.png)

![image-20200614171833539](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614171833539.png)



3.解压Redis安装包（程序一般放在opt目录下）

![image-20200614172345397](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614172345397.png)

![image-20200614172416359](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614172416359.png)



4.进入解压后的文件，可以看到配置文件

![image-20200614172601500](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614172601500.png)



5.基本的环境安装  

```bash
 yum install gcc-c++
 make
```

安装报错

![image-20200614173911342](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614173911342.png)



原因是因为,这里安装gcc的时候
如果执行下面的命令,默认安装的版本是4.8.5版本的

```powershell
yum install gcc-c++
```

用这个版本安装6.0以上的redis就会报错
原因是因为gcc版本过低，yum安装的gcc是4.8.5的。
因此需要升级gcc，
用下面的命令对gcc进行升级：

```bash
yum -y install centos-release-scl 

yum -y install devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils 

#这句是临时的 
scl enable devtoolset-9 bash 

#修改环境变量 
echo "source /opt/rh/devtoolset-9/enable" >> /etc/profile 
   
gcc -v 
```

 

之后就可以 make 和 make install

![image-20200614174458871](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614174458871.png)



![image-20200614174619594](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614174619594.png)



5.Redis的默认安装路径  `usr/local/bin` 目录下

![image-20200614175113401](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614175113401.png)



7.将redis配置文件，复制到当前目录下

```bash
# 新建目录 myconf
mkdir myconf
# 复制到这里使用，是怕以后出现问题，可以用原生的配置文件拿来替换这个出问题的
cp /opt/redis-6.0.5/redis.conf myconf
```

![image-20200614175751118](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614175751118.png)



8.Redis默认不是后台启动的，需要修改配置文件  redis.conf

![image-20200614180225168](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614180225168.png)

9.启动Redis服务

```bash
redis-server  myconf/redis.conf
```



![image-20200614180806970](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614180806970.png)



10.启动客户端，连接服务器

```bash
redis-cli -p 6379
```



![image-20200614181009467](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614181009467.png)

![image-20200614181301229](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614181301229.png)



11.查看Redis的进程是否开启

```bash
ps -ef|grep redis
```



![image-20200614181559620](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614181559620.png)

12.如何关闭Redis服务

```bash
shutdown
# 然后再查查Redis的进程是否开启
ps -ef|grep redis
```

![image-20200614181920228](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614181920228.png)

![image-20200614182110844](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614182110844.png)

可以发现客户端、服务端都关闭了



### 测试性能

**redis-benchmark** 是一个压力测试工具（官方自带）

![image-20200614182321419](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614182321419.png)



| 序号 | 选项      | 描述                                       | 默认值    |
| :--- | :-------- | :----------------------------------------- | :-------- |
| 1    | **-h**    | 指定服务器主机名                           | 127.0.0.1 |
| 2    | **-p**    | 指定服务器端口                             | 6379      |
| 3    | **-s**    | 指定服务器 socket                          |           |
| 4    | **-c**    | 指定并发连接数                             | 50        |
| 5    | **-n**    | 指定请求数                                 | 10000     |
| 6    | **-d**    | 以字节的形式指定 SET/GET 值的数据大小      | 2         |
| 7    | **-k**    | 1=keep alive 0=reconnect                   | 1         |
| 8    | **-r**    | SET/GET/INCR 使用随机 key, SADD 使用随机值 |           |
| 9    | **-P**    | 通过管道传输 <numreq> 请求                 | 1         |
| 10   | **-q**    | 强制退出 redis。仅显示 query/sec 值        |           |
| 11   | **--csv** | 以 CSV 格式输出                            |           |
| 12   | **-l**    | 生成循环，永久执行测试                     |           |
| 13   | **-t**    | 仅运行以逗号分隔的测试命令列表。           |           |
| 14   | **-I**    | Idle 模式。仅打开 N 个 idle 连接并等待。   |           |



#### 测试

简单地测试一下

```bash
# 在一个窗口中开启服务  在另外一个窗口进入bin目录
cd /usr/local/bin
# 测试： 100个并发连接    100000 请求   
redis-benchmark -h localost -p 6379 -c 100 -n 100000
```

![image-20200614183808957](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614183808957.png)

![image-20200614183933655](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614183933655.png)



### 基础知识

* redis默认有16个数据库

![image-20200614184331088](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614184331088.png)

* 默认使用第0个数据库

  * 从第5个切换到第0个数据库，可以看到跟默认的时候的样子的一样的
    * 默认是  127.0.0.1:6379>     而不是    127.0.0.1:6379[0]> 

  ![image-20200614185514951](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614185514951.png)

  * 默认数据库0 初始化的key（除了name，那么 是我们自己加入的）

  ![image-20200614185942987](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614185942987.png)

* 可以使用 select 进行切换（这里切换到数据库5）

  * 查看数据库大小  dbsize
  * 默认是0，set一个值之后正常加1了
  * 切换数据库（切换到数据库6）
    * 可以发现 dbsize 为 0
    * get name  get不到 （本来这个数据库就没set name）
  * 切换回去（数据库5）
    * get name 正常
    * keys *  ：查看当前所有的key 

![image-20200614184838935](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614184838935.png)

![image-20200614185134924](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614185134924.png)

```bash
# 查看当前数据库所有的key
keys *  
# 清空当前数据库数据
flushdb
# 清空所有数据库数据
flushall
```

![image-20200614185312359](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614185312359.png)

#### Redis是单线程的

* Redis读写操作快，它是基于内存操作的

* cpu不是Redis的性能瓶颈，Redis的性能瓶颈是根据机器的内存和网络带宽而定的，既然可以使用单线程来实现，那就使用单线程了



Redis是C语言写的，官方提供的数据为 10万+ 的QPS，完全不必Memecache查！

#### 为什么Redis单线程还这么快

误区1：认为高性能的服务器一定是多线程的

误区2：认为多线程（CPU会进行上下文切换）一定比单线程效率高

（这里需要对CPU>内存>硬盘的速度有所了解）

核心在于：Redis是把所有的数据都放在==内存==中，所以使用单线程去操作的效率就是最高的，如果使用多线程（CPU会上下文切换：耗时的操作！！）。对应内存系统而言，如果没有上下文切换效率就是最高的（同样是基于内存而言，多线程需要上下文切换）。

多个读写都在同一个CPU的，这是，选择只在内存读写，是这种情况下的最佳方案



## 五大数据类型

![image-20200614203044425](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614203044425.png)

Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作==数据库==、==缓存==和==消息中间件MQ==。 它支持多种类型的数据结构，如 字符串（strings）， 散列（hashes）， 列表（lists）， 集合（sets）， 有序集合（sorted sets） 与范围查询， bitmaps， hyperloglogs 和 地理空间（geospatial） 索引半径查询。 Redis 内置了 复制（replication），LUA脚本（Lua scripting）， LRU驱动事件（LRU eviction），事务（transactions） 和不同级别的 磁盘持久化（persistence）， 并通过 Redis哨兵（Sentinel）和自动 分区（Cluster）提供高可用性（high availability）。



==查看命令==

![image-20200614210540914](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614210540914.png)

### Redis-Key

redis不区分大小写命令

```bash
 # 判断当前的key是否存在
 EXISTS name
 EXISTS age
 # 将这个key移动到1号数据库
 move name 1
 # 设置一个key的过期时间  # 存活10秒
 EXPIRE name 10   
 # 查看当前key的剩余时间
 ttl name    
 # 查看当前key是什么类型
 type age
```

![image-20200614204100299](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200614204100299.png)

```bash
127.0.0.1:6379> set name gokufriday
OK
127.0.0.1:6379> keys *
1) "name"
2) "age"
127.0.0.1:6379> EXPIRE name 10   # 存活10秒
(integer) 1
127.0.0.1:6379> ttl name    # 查看还剩下几秒
(integer) -2
127.0.0.1:6379> keys *
1) "age"
127.0.0.1:6379> type age	# 查看当前key是什么类型
string
```



### String（字符串）

大部分 的java程序员使用redis只会使用一个String类型

#### 使用场景

String一般的使用场景：value除了是字符串还可以是数字

* 计数器
* 统计多单位的数量   uid:785647744:follow 0  incr/decr
* 对象缓存存储

#### 指令

```bash
# 这里的 key1、views  都是测试的时候新建的key
####################################################
# 拼接字符串   如果当前字符串不存在，还是会追加（相当于set key）
append key1 "gokudu"
# 获取字符串长度
strlen key1
# ------------ 步长 ----------------
# 每次加1   i++
incr views
# 每次减1	 i--
decr views
# 设置步长,指定增量
incrby views 10    # 追加10
decrby views 5	   # 减少5
# ----------- 字符串范围  ---------------
# 截取字符串  [0,3]闭区间
getrange key1 0 3 
# 截取全部的字符串  
getrange key1 0 -1
# 替换
# 替换指定位置开始的字符串  这里从第二个字符串开始替换  替换位 uuu
setrange key2 2 uuu
##############################################
# setex (set with expire)  设置过期时间
# setnx （set if not exist） 不存在再去设置 （在分布式锁中会经常使用到）
setex key3 60 "helloworld"  # 设置 key3 60秒后过期
"gokudududu" #如果原先mykey不存在，可以创建！否则创建失败！
##############################################
# 批量操作
# mset  同时设置多个值
# mget	同时获取多个值
# msetnx 同时设置多个值，并且需要key不存在，才能设置（原子性的操作，要么一起成功，要么失败）
mset k1 v1 k2 v2 k3 v3  # 同时设置多个值
mget k1 k2 k3   # 同时获取多个值
msetnx k1 v1 k4 v4  # msetnx是原子性的  只要k1、k4有一个是存在的，就创建失败
#################################################
# 对象  
# 方式1    使用mset，mget  设置和获取对象
mset user:1:name gokufriday user:1:age 22  # 给对象 user：1 设置多个属性    
										   # user:1:name、user:1:age  这些就是key
mget user:1:name user:1:age  # 获取对象 user：1 的多个属性
# 方式2    使用set，get  设置和获取对象
set user:2 {name:zhangsan,age:33}
get user:2
#############################################
# getset   先get 然后再 set （相当于更新）  
getset db redis	     # 如果不存在值，返回 nil，然后给这个key设置值为“redis”
(nil)
127.0.0.1:6379> get db   # 查看db的值
"redis"
127.0.0.1:6379> getset db mongodb  # 如果存在值，则返回当前值，并且给这个key设置新的值
"redis"
127.0.0.1:6379> get db
"mongodb"
```

#### 测试

```bash
127.0.0.1:6379> set key1 k1		# 设置值
OK
127.0.0.1:6379> keys *		  # 获取所有key
1) "key1"
127.0.0.1:6379> EXISTS key1		# 判断某一个key是否存在
(integer) 1
127.0.0.1:6379> append key1 "gokudu"  # 拼接字符串   如果当前字符串不存在，还是会追加（相当于set key）
(integer) 8
127.0.0.1:6379> get key1
"k1gokudu"
127.0.0.1:6379> strlen key1		# 获取字符串长度
(integer) 8
127.0.0.1:6379> append key1 ",sayhello"
(integer) 17
127.0.0.1:6379> get key1
"k1gokudu,sayhello"
################################################
# 步长
127.0.0.1:6379> set views 0  # 设置views为0
OK
127.0.0.1:6379> get views
"0"
127.0.0.1:6379> INCR views  # views++
(integer) 1
127.0.0.1:6379> get views
"1"
127.0.0.1:6379> incr views  
(integer) 2
127.0.0.1:6379> get views
"2"
127.0.0.1:6379> decr views   # views--
(integer) 1
127.0.0.1:6379> get views
"1"
127.0.0.1:6379> incrby views 10  # views + 10
(integer) 11
127.0.0.1:6379> get v
(nil)
127.0.0.1:6379> get views
"11"
127.0.0.1:6379> incr views    # views++
(integer) 12
127.0.0.1:6379> get views
"12"
127.0.0.1:6379> decrby views 5   # views - 5
(integer) 7
127.0.0.1:6379> get views
##################################################
# 字符串范围  range
127.0.0.1:6379> set key1  "hello,redis"  # 设置key1 的值
OK
127.0.0.1:6379> get key1
"hello,redis"
127.0.0.1:6379> getrange key1 0 3  # 截取字符串  [0,3]闭区间
"hell"
127.0.0.1:6379> getrange key1 0 -1  # 截取全部的字符串

# 替换字符串
127.0.0.1:6379> set key2 abcdefghi
OK
127.0.0.1:6379> get key2	
"abcdefghi"
127.0.0.1:6379> setrange key2 2 uuu	# 从第二个字符串开始替换  替换位 uuu
(integer) 9
127.0.0.1:6379> get key2
"abuuufghi"
##############################################
# setex (set with expire)  设置过期时间
# setnx （set if not exist） 不存在再去设置 （在分布式锁中会经常使用到）
127.0.0.1:6379> setex key3 60 "helloworld"   # 设置 key3 的值为“helloworld”   60秒后过期
OK
127.0.0.1:6379> ttl key3	# 可以看到在倒计时
(integer) 55
127.0.0.1:6379> ttl key3
(integer) 54
127.0.0.1:6379> ttl key3
(integer) 51
# ----------
127.0.0.1:6379> setnx mykey "gokudududu" #如果mykey不存在，可以创建，返回1
(integer) 1
127.0.0.1:6379> setnx mykey "newgoku"  # 如果mykey存在，创建失败，返回0
(integer) 0
127.0.0.1:6379> get mykey  # 可以发现get到是第一次设置的值
"gokudududu"
##############################################
# 批量操作
# mset  同时设置多个值
# mget	同时获取多个值
# msetnx 同时设置多个值，并且需要key不存在，才能设置（原子性的操作，要么一起成功，要么失败）
127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> mset k1 v1 k2 v2 k3 v3  # 同时设置多个值
OK
127.0.0.1:6379> keys *  
1) "k2"
2) "k1"
3) "k3"
127.0.0.1:6379> mget k1 k2 k3   # 同时获取多个值
1) "v1"
2) "v2"
3) "v3"
# 测试 msetnx 可以发现msetnx是原子性的  只要有一个是存在的，就创建失败
127.0.0.1:6379> msetnx k1 v1 k4 v4
(integer) 0
127.0.0.1:6379> get k4
(nil)
127.0.0.1:6379> keys *
1) "k2"
2) "k1"
3) "k3"
# ***   msetnx 假如设置的所有的key1都不存在，可以设置成功
127.0.0.1:6379> msetnx k4 v4 k5 v5
(integer) 1
127.0.0.1:6379> keys *
1) "k2"
2) "k5"
3) "k1"
4) "k4"
5) "k3"
############################################
# 对象  
# 方式1    mset，mget  设置和获取对象
127.0.0.1:6379> mset user:1:name gokufriday user:1:age 22
OK
127.0.0.1:6379> mget user:1:name user:1:age
1) "gokufriday"
2) "22"
# 方式2    set，get  设置和获取对象
127.0.0.1:6379> set user:2 {name:zhangsan,age:33}
OK
127.0.0.1:6379> get user:2
"{name:zhangsan,age:33}"
#############################################
# getset   先get 然后再 set    getset好了之后，查看db的值，无论这个可以存不存在，都会设置（可以理解为更新）
127.0.0.1:6379> getset db redis	# 如果不存在值，返回 nil，然后给这个key设置值为“redis”
(nil)
127.0.0.1:6379> get db			# 查看db的值
"redis"
127.0.0.1:6379> getset db mongodb  # 如果存在值，则返回 当前值，并且给这个key设置新的值
"redis"
127.0.0.1:6379> get db
"mongodb"
```





### List（链表）

基本的数据类型，列表

在redis里面，可以把list完成：栈、队列、阻塞队列

#### 总结

* 实际上是个链表，before node after ， left ， right 都可以插入值
* 如果key不存在，创建新的链表
  * lpush，rpush
* 如果key存在，新增内容
* 如果移除了链表中所有的值，那么这个列表就不存在了
* 在两边插入或者改动值，效率最高！中间元素，相对来说，效率更低一点

消息队列  Lpush Rpop

栈   Lpush Lpop

#### 指令

```bash
###################################################
## push
#  lpush
#  rpush
LPUSH list one   # 将一个或多个值，插入到列表头部（左）
LPUSH list two
LPUSH list three
LRANGE list 0 -1  # 获取list中全部的值
LRANGE list 0 1   # 获取具体区间的值
# --
rpush list rightone  # 将一个或多个值，插入到列表头部（右）
#######
## pop
lpop list   # 移除列表的一个元素（左）
rpop list  # 移除列表的最后一个元素（右）
########
##   Lindex
lindex list 1  #  通过下标获取 list 中的某一个值（下标从0开始）
#######
## Llen   返回列表的长度
llen list  
#######
##  Lrem   移除指定的值 移除值的个数
##  Lrem key count element
lrem list 1 one  # 移除list列表中指定个数的value
lrem list 2 three   # 移除list列表中指定个数（2个）的 key 为 “three” 的值
######
##  trim 修剪  截断
ltrim mylist 1 2     # 通过下标截取指定的长度，可以发现list已经被改变了，只剩下截取的元素
######
##  rpoplpush 移除列表的最后一个元素，并把这个元素移动到新（目标）列表中去
rpoplpush mylist myotherlist # 移除列表的最后一个元素，并把这个元素移动到目标列表中去
#####
##  lset 将列表中指定下标的值替换为另一个，相当于更新操作（key，index都存在才能更新）
#   lset key index element
##  EXISTS 判断这个列表是否存在
lset list 0 item  # 存在 key、index，更新对应下标的值
######
##  Linsert  将某一个具体的value插入到指定的值的前面，或者后面
LINSERT mylist before hello hellobefore   # 在列表mylist中，将 hellobefore 插入到hello前面
LINSERT mylist after hello helloafter	  # 在列表mylist中，将 hellobefore 插入到hello后面
```



#### 测试

```bash
###################################################
## push
#  lpush
#  rpush
127.0.0.1:6379> LPUSH list one   # 将一个或多个值，插入到列表头部（左）
(integer) 1
127.0.0.1:6379> LPUSH list two
(integer) 2
127.0.0.1:6379> LPUSH list three
(integer) 3
127.0.0.1:6379> LRANGE list 0 -1  # 获取list中全部的值
1) "three"
2) "two"
3) "one"
127.0.0.1:6379> LRANGE list 0 1    # 获取具体区间的值
1) "three"
2) "two"
# ----
127.0.0.1:6379> rpush list rightone  # 将一个或多个值，插入到列表头部（右）
(integer) 4
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "two"
3) "one"
4) "rightone"
###################################################
## pop
#  lpop
#  rpop
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "two"
3) "one"
4) "rightone"  
127.0.0.1:6379> lpop list   # 移除列表的一个元素（左）
"three"
127.0.0.1:6379> lrange list 0 -1
1) "two"
2) "one"
3) "rightone"
127.0.0.1:6379> rpop list  # 移除列表的最后一个元素（右）
"rightone"
127.0.0.1:6379> lrange list 0 -1
1) "two"
2) "one"
###################################################
## Lindex
127.0.0.1:6379> lrange list 0 -1
1) "two"
2) "one"
127.0.0.1:6379> lindex list 1  # 通过下标获取 list 中的某一个值
"one"
127.0.0.1:6379> lindex list 0
"two"
###################################################
## Llen
127.0.0.1:6379> llen list  # 返回列表的长度
(integer) 2
###################################################
##  Lrem   移除指定的值 移除值的个数
##  Lrem key count element
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "three"
3) "two"
4) "one"
127.0.0.1:6379> lrem list 1 one  # 移除list列表中指定个数的value
(integer) 1
127.0.0.1:6379> lrange list 0 -1 
1) "three"
2) "three"
3) "two"
127.0.0.1:6379> lrem list 1 three
(integer) 1
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "two"
127.0.0.1:6379> lpush list three
(integer) 3
127.0.0.1:6379> lrange list 0 -1
1) "three"
2) "three"
3) "two"
127.0.0.1:6379> lrem list 2 three  # 移除list列表中指定个数（2个）的 key 为 “three” 的值
(integer) 2
127.0.0.1:6379> lrange list 0 -1
1) "two"
###################################################
##  Ltrim 修剪  截断     通过下标截取指定的长度，可以发现list已经被改变了，只剩下截取的元素
#   Ltrim key start stop
127.0.0.1:6379> rpush mylist hello
(integer) 1
127.0.0.1:6379> rpush mylist hello1
(integer) 2
127.0.0.1:6379> rpush mylist hello2
(integer) 3
127.0.0.1:6379> rpush mylist hello3
(integer) 4
127.0.0.1:6379> lrange mylist 0 -1
1) "hello"
2) "hello1"
3) "hello2"
4) "hello3"
127.0.0.1:6379> ltrim mylist 1 2  # 这里截取了下标1到2的值，剩下的都被剪掉了
OK
127.0.0.1:6379> lrange mylist 0 -1
1) "hello1"
2) "hello2"
###################################################
##  rpoplpush 移除列表的最后一个元素，并把这个元素移动到新（目标）列表中去
127.0.0.1:6379> rpush mylist hello
(integer) 1
127.0.0.1:6379> rpush mylist hello1
(integer) 2
127.0.0.1:6379> rpush mylist hello2
(integer) 3
127.0.0.1:6379> rpoplpush mylist myotherlist # 移除列表的最后一个元素，并把这个元素移动到目标列表中去
"hello2"
127.0.0.1:6379> lrange mylist 0 -1 # 查看原来的列表
1) "hello"
2) "hello1"
127.0.0.1:6379> lrange myotherlist 0 -1 # 查看新的列表
1) "hello2"
###################################################
##  lset 将列表中指定下标的值替换为另一个，相当于更新操作
#   lset key index element
##  EXISTS 判断这个列表是否存在
127.0.0.1:6379> EXISTS list  # 判断这个列表是否存在
(integer) 0
127.0.0.1:6379> lset list 0 item # 不存在key，就不能set
(error) ERR no such key
127.0.0.1:6379> lpush list value1
(integer) 1
127.0.0.1:6379> lrange list 0 0
1) "value1"
127.0.0.1:6379> lset list 0 item  # 存在 key、index，更新对应下标的值
OK
127.0.0.1:6379> lrange list 0 0
1) "item"
127.0.0.1:6379> lset list 1 item1 # 即使key存在，但是不存在index，也同样不能set
(error) ERR index out of range
###################################################
##  Linsert  将某一个具体的value插入到指定的值的前面，或者后面
127.0.0.1:6379> rpush mylist hello
(integer) 1
127.0.0.1:6379> rpush mylist world
(integer) 2
127.0.0.1:6379> LINSERT mylist before hello hellobefore   # 在列表mylist中，将 hellobefore 插入到hello前面
(integer) 3
127.0.0.1:6379> LINSERT mylist after hello helloafter   # 在列表mylist中，将 helloafter 插入到 hello 后面
(integer) 4
127.0.0.1:6379> lrange mylist 0 -1
1) "hellobefore"
2) "hello"
3) "helloafter"
4) "world"
127.0.0.1:6379> 

```



### Set（集合）

==set是无需不重复集合==

set中的值是不能重读的！

#### 使用场景

微博，某用户所有关注的人放在一个set，所有粉丝放入另一个set集合中（通过交集就可以拿到共同用户）

共同关注，共同爱好，推荐好友

#### 命令测试

```bash
###########################################################
##  sadd        set集合中添加值
#   SMEMBERS     查看指定set的值
#   sismember     判断某一个元素是不是在set集合中
127.0.0.1:6379> sadd myset hello	# set集合中添加值
(integer) 1
127.0.0.1:6379> sadd myset world
(integer) 1
127.0.0.1:6379> sadd myset redis
(integer) 1
127.0.0.1:6379> SMEMBERS myset    # 查看指定set的值
1) "world"
2) "hello"
3) "redis"
127.0.0.1:6379> sismember myset redis  # 判断某一个元素是不是在set集合中
(integer) 1
###########################################################
##  scard		# 获取set集合中的内存元素个数
127.0.0.1:6379> scard myset   # 获取set集合中的内存元素个数
(integer) 3
###########################################################
##  srem   # 移除set中指定的值
127.0.0.1:6379> srem myset world  # 移除set中指定的值world
(integer) 1
127.0.0.1:6379> smembers myset
1) "hello"
2) "redis"
###########################################################
## set是无序不重复集合
##  srandmember # 随机抽取元素  可以指定个数
127.0.0.1:6379> srandmember myset  # 随机抽取一个元素
"hello"
127.0.0.1:6379> srandmember myset
"redis"
127.0.0.1:6379> srandmember myset
"world"
127.0.0.1:6379> srandmember myset
"world"
127.0.0.1:6379> srandmember myset
"redis"
127.0.0.1:6379> srandmember myset 2  # 随机抽取两个元素
1) "hello"
2) "world"
127.0.0.1:6379> srandmember myset 2
1) "hello"
2) "redis"
127.0.0.1:6379> srandmember myset 2
1) "hello"
2) "redis"
127.0.0.1:6379> srandmember myset 2
1) "world"
2) "redis"
127.0.0.1:6379> 
###########################################################
##	spop  随机删除key
127.0.0.1:6379> sadd myset redis
(integer) 1
127.0.0.1:6379> sadd myset hello
(integer) 1
127.0.0.1:6379> sadd myset world
(integer) 1
127.0.0.1:6379> spop myset   #  随机删除key
"redis"
127.0.0.1:6379> spop myset
"world"
127.0.0.1:6379> spop myset
"hello"
###########################################################
##  smove  将一个指定的值  移动导另一个set集合中
# 	smove source destination member
127.0.0.1:6379> sadd myset "hello"
(integer) 1
127.0.0.1:6379> sadd myset "world"
(integer) 1
127.0.0.1:6379> sadd myset "redis"
(integer) 1
127.0.0.1:6379> sadd myset2 "set2"
(integer) 1
127.0.0.1:6379> smove myset myset2 hello
(integer) 1
127.0.0.1:6379> smembers myset
1) "world"
2) "redis"
127.0.0.1:6379> smembers myset2
1) "hello"
2) "set2"
###########################################################
##  数学集合类
#		差集   sdiff key1 key2
#       交集   sinter key1 key2
#       并集   sunion key1 key2
127.0.0.1:6379> sadd key a
(integer) 1
127.0.0.1:6379> sadd key b
(integer) 1
127.0.0.1:6379> sadd key c
(integer) 1
127.0.0.1:6379> sadd key2 c
(integer) 1
127.0.0.1:6379> sadd key2 d
(integer) 1
127.0.0.1:6379> sadd key2 e
(integer) 1
127.0.0.1:6379> sdiff key key2    # 差集
1) "b"
2) "a"
127.0.0.1:6379> sdiff key2 key    # 差集  
1) "e"
2) "d"
127.0.0.1:6379> sinter key key2   #	交集  一些社交网站中的共同好友可以这样实现
1) "c"
127.0.0.1:6379> sunion key key2   #	并集
1) "c"
2) "a"
3) "e"
4) "d"
5) "b"
```





### Hash（哈希）

Map集合  key-map    key-<key，value>

Hash本质和String类型没有多大区别，还是一个简单的key-value

#### 运用场景

* hash存储变更的数据（尤其是用户信息之类，经常变动的信息。）

* hash更适合存储对象
* String更适合存储字符串

#### 测试指令

```bash
###########################################################
##   hset，hget    set/get 一个 具体的 key-value
##	 hmset，hmget   set/get 多个 字段的值
##  hgetall   获取全部数据（包括key，value）
# hset key field value [field value ...] 
127.0.0.1:6379> hset myhash name gokudu  # set一个具体的 key-value
(integer) 1
127.0.0.1:6379> hget myhash name   # 获取一个字段的值
"gokudu"
127.0.0.1:6379> hmset myhash age 22 phone 13076525421  # hmset  set多个具体的 key-value
OK
127.0.0.1:6379> hmget myhash name age phone   # 获取多个字段的值
1) "gokudu"
2) "22"
3) "13076525421"
127.0.0.1:6379> hgetall myhash    # 获取全部数据
1) "name"
2) "gokudu"
3) "age"
4) "22"
5) "phone"
6) "13076525421"
###########################################################
##  hdel  删除hash指定可以的字段，对应的value也同时被删除了
127.0.0.1:6379> hdel myhash phone	# 删除hash指定可以的字段，对应的value也同时被删除了
(integer) 1
127.0.0.1:6379> hgetall myhash
1) "name"
2) "gokudu"
3) "age"
4) "22"
###########################################################
##  hlen 获取hash表的字段数量
127.0.0.1:6379> hlen myhash   # 获取hash表的字段数量
(integer) 2
###########################################################
##  hexists   判断hash中的指定字段存不存在
127.0.0.1:6379> hexists myhash phone   # 判断hash中的指定字段存不存在
(integer) 1
127.0.0.1:6379> hexists myhash weixin
(integer) 0
###########################################################
##   hkeys  只获得所有的key  
##   hvals  只获取所有的value
127.0.0.1:6379> hkeys myhash   # 获取所有的key
1) "name"
2) "age"
3) "phone"
4) "QQ"
5) "fathername"
127.0.0.1:6379> hvals myhash    # 获取所有的value
1) "gokudu"
2) "22"
3) "1216354856"
4) "152522512"
5) "gokufather"
127.0.0.1:6379> 
###########################################################
##  hincrby     指定增量（可以为负） 
##  hsetnx   如果存在字段，设置失败，如果不存在字段，设置成功
127.0.0.1:6379> hincrby myhash num 1 
(integer) 11
127.0.0.1:6379> hincrby myhash num -1
(integer) 10
127.0.0.1:6379> hincrby myhash num 5
(integer) 15
127.0.0.1:6379> hsetnx myhash num 1   # 如果存在字段，设置失败
(integer) 0
127.0.0.1:6379> hsetnx myhash newnum 100   # 如果不存在字段，设置成功
(integer) 1

```



### Zset（有序集合）

在set的基础上，增加一个值

* set：  set k1 v1
* Zset:  zset k1 score1 v1



#### 使用场景

* set排序：存储班级成绩，工资表排序
* 带权判断：1 普通消息，2 重要消息
* 排行榜应用实现，topN 



#### 指令测试

```bash
###########################################################
##  zadd  添加值       
127.0.0.1:6379> zadd myzset 1 one  # 添加一个值
(integer) 1
127.0.0.1:6379> zadd myzset 2 two 3 three  # 添加多个值
(integer) 2
127.0.0.1:6379> zrange myzset 0 -1  # 获取所有的值
1) "one"
2) "two"
3) "three"
###########################################################
##    排序
##     ZRANGE 升序     ZREVRANGE 降序
##  ZRANGEBYSCORE key min max [WITHSCORES]     #　显示[min,max]区间中的全部用户，升序[携带成绩]
##  ZREVRANGEBYSCORE key min max    #　显示[min,max]区间中的全部用户，降序
127.0.0.1:6379> zadd salary 25000 zhangsan    # zset中添加3个薪水
(integer) 1
127.0.0.1:6379> zadd salary 2500 lisi
(integer) 1
127.0.0.1:6379> zadd salary 500 gokufriday
(integer) 1
127.0.0.1:6379> zrangebyscore salary -inf +inf   #　显示全部用户，升序
1) "gokufriday"
2) "lisi"
3) "zhangsan"
127.0.0.1:6379> zrangebyscore salary -inf +inf withscores 　　#　显示全部用户，升序，附带成绩
1) "gokufriday"
2) "500"
3) "lisi"
4) "2500"
5) "zhangsan"
6) "25000"
127.0.0.1:6379> zrangebyscore salary -inf 2500 withscores  #　显示工作小于2500的全部用户，升序，附带成绩
1) "gokufriday"
2) "500"
3) "lisi"
4) "2500"
127.0.0.1:6379> ZREVRANGEBYSCORE salary +inf -inf withscores  #　显示全部用户，降序，附带成绩
1) "zhangsan"
2) "25000"
3) "lisi"
4) "2500"
5) "gokufriday"
6) "500"
###########################################################
##  zrem  移除有序集合中的指定元素
## zcard  获取集合中的个数
127.0.0.1:6379> zrange myzset 0 -1
1) "one"
2) "two"
3) "three"
127.0.0.1:6379> zrange salary 0 -1
1) "gokufriday"
2) "lisi"
3) "zhangsan"
127.0.0.1:6379> zrem salary lisi   # 移除有序集合中的指定元素
(integer) 1
127.0.0.1:6379> zrange salary 0 -1
1) "gokufriday"
2) "zhangsan"
127.0.0.1:6379> zcard salary	# 获取集合中的个数
(integer) 2
###########################################################
##   zcount  获取指定区间的成员数量
127.0.0.1:6379> zrange myzset 0 -1
1) "one"
2) "two"
3) "three"
127.0.0.1:6379> zcount myzset 1 3   # 获取指定区间的成员数量
(integer) 3
127.0.0.1:6379> zcount myzset 1 2
(integer) 2
###########################################################
##   剩下还有许多api，这时如果有需要，就去查官方文档
```



## 三种特殊数据类型

### geospatial（地理位置）

Redis 的 Geo 在Redis3.2版本就推出了！该功能可以推算地理位置的信息，两点之间的距离，方圆几里的人

可以查询一些测试数据：http://www.jsons.cn/lngcode/

#### 使用场景

* 朋友圈定位
* 附近的人
* 打车距离计算



#### 测试指令

* geoadd

添加一个或多个地理空间位置到sorted set

```bash
###########################################################
##  GEOADD key longitude[经度] latitude[纬度] member [longitude latitude member ...]
#  添加一个或多个地理空间位置到sorted set
127.0.0.1:6379> geoadd china:city 121.17 31.23 shanghai
(integer) 1
127.0.0.1:6379> geoadd china:city 116.63 23.46 chaozhou
(integer) 1
127.0.0.1:6379> geoadd china:city 113.28 23.13 guangzhou
(integer) 1
127.0.0.1:6379> geoadd china:city 114.09 22.54 shenzhen 114.30 30.58 wuhan
(integer) 2
```

* geopos

获取当前位置（经纬度坐标）  GEOPOS key member [member ...]

```bash
## 获取当前位置（经纬度坐标）  GEOPOS key member [member ...]
127.0.0.1:6379> geopos china:city shanghai
1) 1) "121.17000192403793335"
   2) "31.22999903975783553"
```

* GEODIST

返回两个地理空间之间的距离   GEODIST key member1 member2 [unit]

```bash
## 返回两个地理空间之间的距离   GEODIST key member1 member2 [unit]
127.0.0.1:6379> geodist china:city chaozhou guangzhou
"344187.6120"
127.0.0.1:6379> geodist china:city chaozhou guangzhou km  # 查看潮州和广州的距离 （km）
"344.1876"
127.0.0.1:6379> geodist china:city chaozhou shanghai km  # 查看潮州和上海的距离 （km）
"973.3968"
```

* GEORADIUS（通过坐表）

查询指定经纬度半径内所有的地理空间元素的集合。

使用场景

​		附近的人（获取附近的人的地址，定位）通过半径来查询

​		获取附近指定的人  

```bash
#	GEORADIUS  查询指定经纬度半径内所有的地理空间元素的集合
127.0.0.1:6379> GEORADIUS china:city 110 30 500 km # 以100,30 这个经纬度为中心，查找方圆500km的城市 china:city
1) "wuhan"
127.0.0.1:6379> GEORADIUS china:city 110 30 1000 km
1) "shenzhen"
2) "guangzhou"
3) "chaozhou"
4) "wuhan"
127.0.0.1:6379> GEORADIUS china:city 110 30 1000 km withdist  # 显示到中心距离的位置
1) 1) "shenzhen"
   2) "924.3749"
2) 1) "guangzhou"
   2) "830.7427"
3) 1) "chaozhou"
   2) "980.8157"
4) 1) "wuhan"
   2) "417.9614"
127.0.0.1:6379> GEORADIUS china:city 110 30 1000 km withcoord  # 显示他人的定位信息
1) 1) "shenzhen"
   2) 1) "114.09000009298324585"
      2) "22.53999903789756587"
2) 1) "guangzhou"
   2) 1) "113.27999979257583618"
      2) "23.13000101271457254"
3) 1) "chaozhou"
   2) 1) "116.62999838590621948"
      2) "23.45999889517006665"
4) 1) "wuhan"
   2) 1) "114.30000096559524536"
      2) "30.58000021509926825"
127.0.0.1:6379> GEORADIUS china:city 110 30 1000 km withcoord withdist count 2 #显示指定个数，并筛选其他两条件
1) 1) "wuhan"
   2) "417.9614"
   3) 1) "114.30000096559524536"
      2) "30.58000021509926825"
2) 1) "guangzhou"
   2) "830.7427"
   3) 1) "113.27999979257583618"
      2) "23.13000101271457254"
      
```

* GEORADIUSBYMEMBER

GEORADIUSBYMEMBER key member radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count]

查询指定元素周围的其他元素

使用创建

```bash
# 查询指定元素周围的其他元素
127.0.0.1:6379> GEORADIUSBYMEMBER china:city wuhan 1000 km
1) "shenzhen"
2) "guangzhou"
3) "chaozhou"
4) "wuhan"
5) "shanghai"
127.0.0.1:6379> GEORADIUSBYMEMBER china:city wuhan 500 km
1) "wuhan"
127.0.0.1:6379> GEORADIUSBYMEMBER china:city wuhan 700 km
1) "wuhan"
2) "shanghai"

```

* GEOHASH

返回一个标准的地理空间的Geohash字符串

```bash
# 将二维的经纬度转为一维的字符串。如果两个字符串越接近，那么距离越近
127.0.0.1:6379> geohash china:city wuhan
1) "wt3mbyytrj0"
127.0.0.1:6379> geohash china:city guangzhou shenzhen
1) "ws0e9dxyp20"
2) "ws10hpmc180"
```



#### Geo的底层原理：zset

```bash
127.0.0.1:6379> geohash china:city guangzhou shenzhen
1) "ws0e9dxyp20"
2) "ws10hpmc180"
127.0.0.1:6379> zrange china:city 0 -1  # 移除具体范围内的元素
1) "shenzhen"
2) "guangzhou"
3) "chaozhou"
4) "wuhan"
5) "shanghai"
127.0.0.1:6379> zrem china:city shenzhen  # 移除指定元素
(integer) 1
127.0.0.1:6379> zrange china:city 0 -1
1) "guangzhou"
2) "chaozhou"
3) "wuhan"
4) "shanghai"
```



### Hyperloglog

#### 简介

Redis2.8.9版本更新了Hyperloglog数据结构

Redis Hyperloglog 基数统计的算法

#### 优点：

​	占用内存是固定的（2^64不同元素的技术，只需要12kb内存）从内存角度考虑Hyperloglog

#### 什么是基数

A{1,2,3,5,7}

B{1,3,5,7,7}

基数（不重复的元素）： 4  



#### 网页的UV

UV（一个人访问一个网站多次，但还算作是一个人）

​	传统的方式：set保存用户的id，根据统计set中的元素数量作为标准判断

​	这个方式如果保存大量的用户Id，会比较麻烦。因为我们的目的并不是保存id，而是计数

​	0.81%错误率！这个在统计UV任务中，可忽略不计



#### 使用场景

* 如果允许略微容错，一定可以考虑使用Hyperloglog

* 如果不允许容错，就使用set或者自己的数据类型即可



#### 测试指令

```bash
# pfadd   pfcount  pfmerge
127.0.0.1:6379> pfadd mykey a b c d e f g   # 创建第一组元素
(integer) 1
127.0.0.1:6379> pfadd mykey2  e f g x y z m n a
(integer) 1
127.0.0.1:6379> pfcount mykey    # 统计第一组元素
(integer) 7
127.0.0.1:6379> pfcount mykey2
(integer) 9
127.0.0.1:6379> pfmerge mykey3 mykey mykey2  # 合并两组（并集） mykey 并 mykey2  ==》 mykey3
OK
127.0.0.1:6379> pfcount mykey3
(integer) 12
```



### BitMaps

BitMaps 位图，数据结构！操作二进制位来进行记录，只有0和1两个状态

#### 位存储



#### 使用场景

* 统计用户信息
  * 活跃，不活跃
  * 登录，未登录
* 打卡，365天打卡（像这种两个状态的都可以使用 BitMaps ）
  * 365 天 = 365 bit      1字节 = 8 bit       大概46个字节就够了



使用 bitmap 记录，周一到周日的打卡

#### 案例：打卡

* setbit

周一：1    周二：1 ......  周日：0

```bash
# setbit
127.0.0.1:6379> setbit sign 0 1
(integer) 0
127.0.0.1:6379> setbit sign 1 1
(integer) 0
127.0.0.1:6379> setbit sign 2 1
(integer) 0
127.0.0.1:6379> setbit sign 3 1
(integer) 0
127.0.0.1:6379> setbit sign 4 0
(integer) 0
127.0.0.1:6379> setbit sign 5 1
(integer) 0
127.0.0.1:6379> setbit sign 6 0
```

* getbit

查看某一个是否打卡

```bash
127.0.0.1:6379> getbit sign 4
(integer) 0
127.0.0.1:6379> getbit sign 5
(integer) 1
```

* bitcount     统计总数

```bash
127.0.0.1:6379> bitcount sign  # 统计这一周的打卡记录，可以看到有没有全勤
(integer) 5
```



## 事务

Redis事务本质：一组命令的集合！一个事务中的所有的命令都会被序列化，在事务执行过程中，会按照顺序执行！

一致性、顺序性、排他性

```sql
------- 队列 set set set 执行 --------
```

Redis：

* ==单条==命令是保证==原子性==的，==但是事务是**不保证**原子性的==

* ==Redis事务没有隔离级别的概念==
  * 所有的命令在事务中，并没有直接被执行！只有发起执行命令的时候才会被执行！（Exec）

Redis的事务

* 开启事务  multi
* 命令入队  ...
* 执行事务  exec



### 事务的执行

```bash
127.0.0.1:6379> multi	# 开启事务
OK
## 命令入队
127.0.0.1:6379> set key1 v1 	
QUEUED
127.0.0.1:6379> set key2 v2
QUEUED
127.0.0.1:6379> get key2
QUEUED
127.0.0.1:6379> set key3 v3
QUEUED
127.0.0.1:6379> exec   # 执行事务（一组命令只有在发起执行命令的时候才会被执行）
1) OK
2) OK
3) "v2"
4) OK
```



### 放弃事务

```bash
#	discard  放弃事务
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2 
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> discard		# 放弃事务（事务队列中的命令都不会被执行）
OK
127.0.0.1:6379> get k2
(nil)
```



### 编译时异常

代码有错误！命令出错！（事务中的所有命令都不会被执行）

```bash
127.0.0.1:6379> multi 
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> getset k3   # 错误的命令
(error) ERR wrong number of arguments for 'getset' command
127.0.0.1:6379> set k4 v4
QUEUED
127.0.0.1:6379> exec		# 执行事务保错
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379> get k4   # 所有的命令都不会被执行
(nil)
```



### 运行时异常

1/0 （如果事务队列中存在语法性，那么执行命令的时候，其他命令是可以执行的，错误命令抛出异常）

```bash
127.0.0.1:6379> set k1 v1  # 设置字符串
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> incr k1  # 字符串无法 +1 （运行时异常）
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> get k3
QUEUED
127.0.0.1:6379> exec	# 虽然第一条报错（运行时异常）了  都是后面的命令都执行成功了（所以说在Redis中事务是不保证原子性）
1) (error) ERR value is not an integer or out of range
2) OK
3) OK
4) "v3"
127.0.0.1:6379> get k2
"v2"
127.0.0.1:6379> get k3
"v3"

```



### 监控（Watch）

* 悲观锁： 无论什么时候都会加锁（认为什么时候都可能出现问题）
* 乐观锁：认为什么时候都不会出现问题，所以不会上锁！更新数据的时候去判断一下，再此期间是否有人修改过这个数据
  * MySQL中
    * 获取version
    * 更新的时候比较version



#### Redis监视测试

* 正常执行成功

```bash
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> watch money		# 监视 money 对象
OK
127.0.0.1:6379> multi  # 事务正常结束后，数据保存一致性。这个时候事务就执行成功了
OK
127.0.0.1:6379> decrby money 20
QUEUED
127.0.0.1:6379> incrby out 20
QUEUED
127.0.0.1:6379> exec
1) (integer) 80
2) (integer) 20
```

* 测试多线程修改值，使用 watch 可以当做的 Redis 的乐观锁
  * 在服务器中开启两个会话

```bash
127.0.0.1:6379> watch money  # 监视 money 对象（在执行的时候回比较money还是不是100，如果是的话才开始执行命令队列）
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> decrby money 15
QUEUED
127.0.0.1:6379> incrby money 15
QUEUED
127.0.0.1:6379> exec  # 执行之前，另外一个线程，修改了money的值，这个时候，就会导致事务执行失败
(nil)

```

* 
  * 另一个线程在线程1创建事务并且命令入队（但还未执行命令）期间，抢先执行了命令

```bash
127.0.0.1:6379> get money
"80"
127.0.0.1:6379> set money 500
OK
```

* 如果修改失败，获取最新的值就好

```bash
127.0.0.1:6379> unwatch  # 如果发现事务执行失败，就先解锁
OK
127.0.0.1:6379> watch money  # 获取最新的值，再次监视，select version
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> decrby money 5
QUEUED
127.0.0.1:6379> incrby money 5
QUEUED
127.0.0.1:6379> exec  # 对比监视的值是否发生了变化，如果没有变化，那么可以执行成功。如果变化了就执行失败
1) (integer) 495
2) (integer) 500
```



## Jedis

* java操作Redis



### 什么是Jedis

Jedis 是 Redis 官方推荐的 java连接开发工具，使用java操作Redis的中间件。



### 测试

#### 1.导入依赖

```xml
  <dependencies>
      <!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
      <dependency>
          <groupId>redis.clients</groupId>
          <artifactId>jedis</artifactId>
          <version>3.2.0</version>
      </dependency>

      <!--fastjson-->
      <dependency>
          <groupId>com.alibaba</groupId>
          <artifactId>fastjson</artifactId>
          <version>1.2.62</version>
      </dependency>
  </dependencies>
```

#### 2.编码测试

* 连接数据库
* 操作命令
* 断开连接

```java
public class TestPing {
    public static void main(String[] args) {
        // 1. new Jedis 对象
        Jedis jedis = new Jedis("127.0.0.1",6379);
        // 2. 手动开启Redis服务端，然后再来java中连接 
        // jedis 对象中所有的命令就是我们之前学习的所有命令！
        System.out.println(jedis.ping());
    }
}
```

![image-20200615203218966](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200615203218966.png)

#### 基本

```java
public class TestKey {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("127.0.0.1", 6379);

        System.out.println("清空数据库==>"+jedis.flushDB());
        System.out.println("判断某个值是否存在==>"+jedis.exists("name"));
        System.out.println("新增<'name','goku'>的键值对==>"+jedis.set("name", "goku"));
        System.out.println("新增<'age','22'>的键值对==>"+jedis.set("age", "22"));
        System.out.println("系统的所有键如下==>");
        Set<String> keys = jedis.keys("*");
        System.out.println(keys);

        System.out.println("删除age==》"+jedis.del("age"));
        System.out.println("判断age是否存在==》"+jedis.exists("age"));
        System.out.println("查看键name所存储的值的类型"+jedis.type("name"));
        jedis.set("password", "123456");
        jedis.set("phone", "13046753546");
        System.out.println("随机返回一个值==》"+jedis.randomKey());
        System.out.println("随机返回一个值==》"+jedis.randomKey());
        System.out.println("随机返回一个值==》"+jedis.randomKey());
        System.out.println("重命名key==》"+jedis.rename("name", "newName"));
        System.out.println("取出重命名后name的值==》"+jedis.get("newName"));
        System.out.println("按索引查询，切换数据库==》"+jedis.select(1));
        System.out.println("返回当前数据库中所有的key的数目==》"+jedis.dbSize());
        System.out.println("切换回原来的默认号数据库==》"+jedis.select(0));
        System.out.println("返回当前数据库中所有的key的数目==》"+jedis.dbSize());
        System.out.println("清空所有的数据库"+jedis.flushAll());
        Set<String> keys1 = jedis.keys("*");
        System.out.println(keys1);
    }
}
```



##### String

```java
public class TestString {
    public static void main(String[] args) throws InterruptedException {
        Jedis jedis = new Jedis("127.0.0.1", 6379);

        jedis.flushDB();
        System.out.println(jedis.set("key1", "k1"));
        System.out.println(jedis.keys("*"));
        System.out.println(jedis.exists("key1"));
        System.out.println(jedis.append("key1", "isnice"));
        System.out.println(jedis.get("key1"));
        System.out.println(jedis.strlen("key1"));

        System.out.println("==============   步长  ================");
        jedis.flushDB();
        System.out.println(jedis.set("views", "0"));
        System.out.println(jedis.get("views"));
        System.out.println(jedis.incr("views"));
        System.out.println(jedis.incr("views"));
        System.out.println(jedis.decr("views"));
        System.out.println(jedis.incrBy("views", 10));
        System.out.println(jedis.decrBy("views", 5));


        System.out.println("==============   字符串范围  range  ================");
        jedis.flushDB();
        System.out.println(jedis.set("key1", "hello,redis"));
        System.out.println(jedis.get("key1"));
        System.out.println(jedis.getrange("key1", 0L, 3L));
        System.out.println(jedis.getrange("key1", 0, -1));
        System.out.println(jedis.setrange("key1", 2, "uuu"));
        System.out.println(jedis.get("key1"));

        System.out.println("==============   设置过期时间  ================");
        System.out.println(jedis.setex("key3", 60,"ww60"));
        System.out.println(jedis.ttl("key3"));
        System.out.println(jedis.setnx("key3", "uuuuuuu"));
        System.out.println(jedis.setnx("newkey3", "newnewuuuuu"));
        System.out.println(jedis.get("key3"));


        System.out.println("==============   批量操作  ================");
        jedis.mset("key5","001","key6","006");
        System.out.println(jedis.keys("*"));
        System.out.println(jedis.mget("key1", "key3", "key5"));
        System.out.println(jedis.msetnx("key6", "000006", "key7", "000007"));
        System.out.println(jedis.msetnx("key8", "000008", "key7", "000007"));

    }
}
```



##### List

```java
public class TestList {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("127.0.0.1", 6379);

        jedis.flushDB();
        jedis.lpush("list", "one");
        jedis.lpush("list", "two");
        jedis.lpush("list", "three");
        System.out.println(jedis.lrange("list", 0, -1));
        jedis.rpush("list", "rightone");
        System.out.println(jedis.lrange("list", 0, -1));
        jedis.lpop("list");
        System.out.println(jedis.lrange("list", 0, -1));
        jedis.rpop("list");
        System.out.println(jedis.lrange("list", 0, -1));

        System.out.println("------- Lindex lrem --------");
        System.out.println(jedis.lindex("list", 0));
        System.out.println(jedis.lindex("list", 1));
        System.out.println(jedis.lrem("list", 1, "one"));

        System.out.println("------- Lindex Ltrim --------");
        jedis.flushDB();
        jedis.lpush("list", "one");
        jedis.lpush("list", "two");
        jedis.lpush("list", "three");
        jedis.lpush("list", "four");
        jedis.lpush("list", "five");
        System.out.println(jedis.lrange("list", 0, -1));
        jedis.ltrim("list", 2, 3);
        System.out.println(jedis.lrange("list", 0, -1));

        System.out.println("------- rpoplpush 移除列表的最后一个元素，并把这个元素移动到新（目标）列表中去 --------");
        jedis.flushDB();
        jedis.lpush("list", "one");
        jedis.lpush("list", "two");
        jedis.lpush("list", "three");
        jedis.lpush("list", "four");
        jedis.lpush("list", "five");

        jedis.rpoplpush("list", "newlist");
        System.out.println(jedis.lrange("list", 0, -1));
        System.out.println(jedis.lrange("newlist", 0, -1));

        System.out.println("------- rpoplpush lset 将列表中指定下标的值替换为另一个，相当于更新操作--------");
        jedis.flushDB();
    //    System.out.println(jedis.lset("list", 0, "newitem"));
        jedis.lpush("list", "nnn");
        System.out.println(jedis.lset("list", 0, "newitem"));
        System.out.println(jedis.lrange("list", 0, 0));
        System.out.println(jedis.linsert("list", ListPosition.BEFORE, "newitem", "before"));
        System.out.println(jedis.lrange("list", 0, -1));
        System.out.println(jedis.linsert("list", ListPosition.AFTER, "newitem", "after"));
        System.out.println(jedis.lrange("list", 0, -1));
    }
}
```



##### Set

```java
public class TestSet {
    public static void main(String[] args) {

        Jedis jedis = new Jedis("127.0.0.1", 6379);

        jedis.flushDB();
        jedis.sadd("myset", "hello");
        jedis.sadd("myset", "world");
        jedis.sadd("myset", "redis");
        System.out.println(jedis.smembers("myset"));
        System.out.println(jedis.sismember("myset", "hello"));
        System.out.println(jedis.scard("myset"));
        System.out.println(jedis.srem("myset", "world"));
        System.out.println(jedis.smembers("myset"));

        System.out.println("==============  set是无序不重复集合  =================");
        jedis.flushDB();
        jedis.sadd("myset", "hello");
        jedis.sadd("myset", "world");
        jedis.sadd("myset", "redis");
        jedis.sadd("myset", "world");
        jedis.sadd("myset", "redis");
        System.out.println(jedis.smembers("myset"));

        System.out.println("==============  随机抽取元素  可以指定个数  =================");
        System.out.println(jedis.srandmember("myset"));
        System.out.println(jedis.srandmember("myset"));
        System.out.println(jedis.srandmember("myset"));
        System.out.println(jedis.srandmember("myset"));
        System.out.println(jedis.srandmember("myset"));

        System.out.println("===============================");
        System.out.println(jedis.srandmember("myset",2));
        System.out.println(jedis.srandmember("myset",2));
        System.out.println(jedis.srandmember("myset",2));
        System.out.println(jedis.srandmember("myset",2));

        System.out.println("===========spop  随机删除key==============");
        System.out.println(jedis.spop("myset"));
        System.out.println(jedis.spop("myset"));
        System.out.println(jedis.spop("myset"));

        System.out.println("===========smove  将一个指定的值  移动导另一个set集合中==============");
        jedis.flushDB();
        jedis.sadd("myset", "hello");
        jedis.sadd("myset", "world");
        jedis.sadd("myset", "redis");
        jedis.sadd("myset2", "ms2");
        jedis.smove("myset", "myset2","hello");
        System.out.println(jedis.smembers("myset"));
        System.out.println(jedis.smembers("myset2"));

        System.out.println("===========数学集合类==============");
        /*
            差集   sdiff key1 key2
            交集   sinter key1 key2
            并集   sunion key1 key2
         */
        jedis.sadd("k1", "a");
        jedis.sadd("k1", "b");
        jedis.sadd("k1", "c");

        jedis.sadd("k2", "c");
        jedis.sadd("k2", "d");
        jedis.sadd("k2", "e");

        System.out.println(jedis.sdiff("k1", "k2"));
        System.out.println(jedis.sdiff("k2", "k1"));

        System.out.println(jedis.sinter("k1", "k2"));

        System.out.println(jedis.sunion("k1", "k2"));

    }
}
```



##### hash

```java
public class TestHash {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("127.0.0.1", 6379);

        jedis.flushDB();
        HashMap<String, String> map = new HashMap<String, String>();
        map.put("k1", "v1");
        map.put("k2", "v2");
        map.put("k3", "v3");
        map.put("k4", "v4");

        jedis.hmset("hash", map);
        jedis.hset("hash", "k5","v5");

        System.out.println(jedis.hgetAll("hash"));
        System.out.println(jedis.hkeys("hash"));
        System.out.println(jedis.hvals("hash"));
        jedis.hset("hash", "num","10");
        System.out.println(jedis.hincrBy("hash", "num", 5));
        System.out.println(jedis.hincrBy("hash", "num", -10));
        System.out.println(jedis.hsetnx("hash", "num", "11"));
        System.out.println(jedis.hget("hash", "num"));
        System.out.println(jedis.hsetnx("hash", "newnum", "11"));
        System.out.println(jedis.hget("hash", "newnum"));
        System.out.println(jedis.hlen("hash"));
        System.out.println(jedis.hmget("hash", "k2", "k3"));
        System.out.println(jedis.hexists("hash", "k4"));
        System.out.println(jedis.hdel("hash", "k3","k4"));
        System.out.println(jedis.hgetAll("hash"));
    }
}
```



##### zset

```java
public class TestZset {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("127.0.0.1", 6379);

        jedis.flushDB();
        jedis.zadd("myzset", 1, "one");
        jedis.zadd("myzset", 2, "two");
        System.out.println(jedis.zrange("myzset", 0, -1));


        jedis.flushDB();
        jedis.zadd("salary", 25000, "zhansgan");
        jedis.zadd("salary", 2500, "lisi");
        jedis.zadd("salary", 500, "gokufriday");

        System.out.println(jedis.zrangeByScore("salary", 0, 50000));
        System.out.println(jedis.zrangeByScore("salary", 0, 5000));

        System.out.println(jedis.zrevrangeByScore("salary", 50000, 0));
        System.out.println(jedis.zrevrangeByScore("salary", 5000, 0));

        System.out.println(jedis.zrangeByScoreWithScores("salary", 0, 50000));
    }
}
```



#### 测试事务

```java
public class TestTX {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("127.0.0.1", 6379);

        jedis.flushDB();

        JSONObject jsonObject = new JSONObject();
        jsonObject.put("hello", "world");
        jsonObject.put("name", "goku");
        // 开启事务
        Transaction multi = jedis.multi();
        String result = jsonObject.toJSONString();
//        jedis.watch(result);
        try {
            multi.set("user1", result);
            multi.set("user2", result);
//            int i=1/0;
            multi.exec();  // 执行事务
        } catch (Exception e) {
            multi.discard(); // 放弃事务
            e.printStackTrace();
        } finally {
            System.out.println(jedis.get("user1"));
            System.out.println(jedis.get("user2"));
            // 关闭连接
            jedis.close();
        }

    }
}
```



## SpringBoot整合

SpringBoot操作数据 ； Spring-data jpa jdbc mongodb reids

SpringData 是和 SpringBoot 齐名的项目

说明：在SpringBoot 2.X之后，原来使用Jedis 被替换成了lettuce



Jedis：采用直连的方式。多个线程操作的话，是不安全的，如果想要避免不安全，需要使用 jedis pool连接池 ，更像BIO

lettuce：采用netty，实例可以在多个线程中进行共享，不存在不安全的问题，这样可以减少线程数据了，更像NIO



#### 源码分析：

```java
@Configuration(proxyBeanMethods = false)
@ConditionalOnClass(RedisOperations.class)
@EnableConfigurationProperties(RedisProperties.class)
@Import({ LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class })
public class RedisAutoConfiguration {

   @Bean
   @ConditionalOnMissingBean(name = "redisTemplate")  // 我们自己可以自定义一个 redisTemplate 来替换这个默认的
   public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory)
         throws UnknownHostException {
       // 默认的RedisTemplate 没有过多的设置，redis对象都是需要序列化的
       // 每个泛型都是Object，Object，我们使用需要强制转换<String,Object>
      RedisTemplate<Object, Object> template = new RedisTemplate<>();
      template.setConnectionFactory(redisConnectionFactory);
      return template;
   }

   @Bean	//由于String是 Redis 中最经常使用的类型，所以再单独写一个bean
   @ConditionalOnMissingBean
   public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory)
         throws UnknownHostException {
      StringRedisTemplate template = new StringRedisTemplate();
      template.setConnectionFactory(redisConnectionFactory);
      return template;
   }

}
```

#### 步骤

1.导入依赖

```xml
<!--引入redis-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

2.配置application.properties

```properties
# SpringBoot 所有配置类，都有一个自动配置类 RedisAutoConfiguration
# 自动配置类都会有一个 properties 配置文件 RedisProperties
spring.redis.host=127.0.0.1
spring.redis.port=6379
```

3.测试

```java
@SpringBootTest
class Redis02SpringbootApplicationTests {

    @Autowired
    private RedisTemplate redisTemplate;
    
    @Test
    void contextLoads() {
        // redisTemplate
        // opsForList
        // ...
        // opsForValue()   String
        // 除了基本的操作，常用的方法都可以直接通过redisTemplate操作，比如事务，基本的crud

        // 获取redis连接对象
//        RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
//        connection.flushDb();
//        connection.flushAll();
        
        redisTemplate.opsForValue().set("name", "gokudu");
        redisTemplate.opsForValue().get("name");
       
    }
}
```





![image-20200616010002124](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616010002124.png)



![image-20200616010129832](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616010129832.png)



#### 自定义 RedisTemplate

##### 实体类User

暂时不序列化，以便测试

```java
@Component
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {

    private String name;
    private int age;

}
```

##### 测试

* 使用 jackson 的 ObjectMapper 来给对象转换为 json字符串

![image-20200616121900234](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616121900234.png)

![image-20200616121802553](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616121802553.png)



* 直接 set 没有序列化的对象user （直接报错）

需要知道的是，在真实开发中，所有的实体类都会序列化

![image-20200616122826713](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616122826713.png)



* 对user类序列化测试

序列化之后，注入成功（但是我们还有问题，解析到问题，默认的序列化是jdk序列化）

```java
@Component
@Data
@AllArgsConstructor
@NoArgsConstructor
// 在企业中，所有的 pojo 都会序列化
public class User implements Serializable {
    private String name;
    private int age;
}
```

![image-20200616123502443](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616123502443.png)

![image-20200616123756717](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616123756717.png)

##### 编写自定义 RedisTemplate



![image-20200616124212098](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616124212098.png)



![image-20200616124301674](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616124301674.png)



![image-20200616124133496](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616124133496.png)



* 自定义 RedisTemplate<String, Object>

```java
@Configuration
public class RedisConfig {

    // 这是固定模板，拿来就可以直接使用
    // 自己定义一个RedisTemplate
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory)
            throws UnknownHostException {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(factory);

        // 序列化配置
        // 将任意对象转为JSON
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        
        // String的序列化
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();

        // key采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        // hash的key采用String的序列化方式
        template.setHashKeySerializer(stringRedisSerializer);
        // value采用String的序列化方式
        template.setValueSerializer(jackson2JsonRedisSerializer);
        // hash的value采用String的序列化方式
        template.setHashValueSerializer(jackson2JsonRedisSerializer);

        template.afterPropertiesSet();

        return template;
    }
}
```

* flushdb之后再回来测试
  * 注入我们自己的RedisTemplate

```java
@SpringBootTest
class Redis02SpringbootApplicationTests {

    @Autowired
    @Qualifier("redisTemplate")
    // @Autowire 当有多个相同类型的 bean 却只有一个需要自动装配时，将@Qualifier 注解和 @Autowired 结合使用以消除这种混淆，指定需要装配的确切的 bean
    // @Autowired byType     @Qualifier byName
    private RedisTemplate redisTemplate;

    @Test
    void testUser() throws JsonProcessingException {
        // 真实开发一般都是用Json来传递对象
        User user = new User("gokudu", 66);
//        String jsonUser = new ObjectMapper().writeValueAsString(user);  // ObjectMapper 已经序列化了
        redisTemplate.opsForValue().set("user", user);
        System.out.println(redisTemplate.opsForValue().get("user"));
    }
}
```

![image-20200616170119145](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616170119145.png)

![image-20200616170133677](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616170133677.png)





## Redis.conf详解

> 1.配置文件  单位  大小写不敏感

![image-20200616174624747](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616174624747.png)



>  2.可以包含其他配置文件

![image-20200616174743925](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616174743925.png)



> 3.网络

```bash
bind 127.0.0.1
protected-mode yes
port 6379
```

> 4.通用配置

```bash
daemonize yes  # 以守护进程的方式进行  默认是 no，需要我们自己开启 yes
pidfile /var/run/redis_6379.pid  # 如果以后台的方式运行，我们需要指定一个pid文件

# 日志
# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably)  生产环境
# warning (only very important / critical messages are logged)
loglevel notice # 默认日志级别 
logfile ""		# 日志的文件为准名

databases 16	# 默认数据库数量
always-show-logo yes	# 是否总是显示logo
```

> 5.快照

持久化，在规定的时间内，执行了多少次操作，则会持久化到文件 .rdb   .aof

redis是内存数据库，如果没有持久化，那么数据断电即失

```bash
# 如果 900s 内，如果至少有 1 个key进行修改，那么将及时进行持久化
save 900 1
# 如果 300s 内，如果至少有 10 个key进行修改，那么将及时进行持久化
save 300 10
# 如果 60s 内，如果至少有 10000 个key进行修改，那么将及时进行持久化
save 60 10000
# 之后的学习持久化，可以自己定义

stop-writes-on-bgsave-error yes  # 持久化错误之后是否继续工作
rdbcompression yes		# 是否压缩 rdb 文件，需要消耗一些 cpu 资源
rdbchecksum yes		#保存 rdb 文件的时候，进行检查校验
dir ./			# rdb 文件目录
```

> Replication 复制

关于主从复制的详细内容，在后面 Redis主从复制 这里



> Security  安全

```bash
127.0.0.1:6379> config get requirepass   # 获取redis的密码
1) "requirepass"
2) ""
127.0.0.1:6379> config set requirepass "123456"  # 设置redis的密码
OK

127.0.0.1:6379> exit
[root@gokufriday bin]# ./redis-cli -p 6379		# 重新连接
127.0.0.1:6379> ping						# 输入命令没有权限
(error) NOAUTH Authentication required.
127.0.0.1:6379> auth 123456		# 使用密码进行登录
OK
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> config get requirepass
1) "requirepass"
2) "123456"
```



> 限制

```bash
# maxclients 10000     # 连接上1最大客户端数量
# maxmemory <bytes>    # redis 配置的最大内存容量
# maxmemory-policy noeviction	# 内存到达上限之后的处理策略
    1、volatile-lru：只对设置了过期时间的key进行LRU（默认值） 
    2、allkeys-lru ： 删除lru算法的key   
    3、volatile-random：随机删除即将过期key   
    4、allkeys-random：随机删除   
    5、volatile-ttl ： 删除即将过期的   
    6、noeviction ： 永不过期，返回错误
```

> APPEND ONLY MODE模式  （aof）

具体的配置在后面 Redis持久化 这里

```bash
appendonly no  # 默认不开启。默认使用 rdb 模式持久化，大多数情况下，rdb够用了
appendfilename "appendonly.aof"		# 持久化文件名

# appendfsync always  # 每次修改都会同步    消耗性能，速度最慢
appendfsync everysec  # 每秒都执行同步一次  sync    但是可能会丢失这1s的数据
# appendfsync no	  # 不执行同步	这样的话操作系统自己执行同步1，速度最快
```



## Redis持久化

参考博客  https://www.cnblogs.com/chichung/p/12687101.html

Redis是内存数据库，如果不将内存中的数据库状态保存到磁盘，一旦服务器进程退出，服务器中的数据库状态也会消失。

Redis为此提供了持久化功能

### RDB（Redis DataBase）（快照）

RDB保存的文件是 ==dump.rdb==

![image-20200616192630971](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616192630971.png)

> 触发机制

1.save的规则满足的情况下，会自动触发 rdb 规则 ，产生 rdb 文件

* save可以自己设定
* 你可以对 Redis 进行设置， 让它在“N 秒内数据集至少有 M 个改动”这一条件被满足时，
  自动保存一次数据集。

* 比如说， 以下设置会让 Redis 在满足“100 秒内有至少有 5000 个键被改动”这一条件时，
  自动保存一次数据集：

```shell
save 100 5000
```

2.执行flushall命令，也会触发 rdb 规则

3.退出 redis，也会触发 rdb 规则

备份会自动生成一个dump.rdb（在生产环境，我们很有可能对这个文件进行备份）

![image-20200616185131733](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616185131733.png)

> 恢复rdb文件

1.只需要将 rdb文件 放在 redis 启动目录即可，Redis启动的时候回自动检测 dump.rdb 恢复其中的数据

2.查看启动位置

```bash
127.0.0.1:6379> config get dir
1) "dir"
2) "/usr/local/bin"		# 将 rdb 文件放在这个命令下，启动就会自动恢复其中的数据
```

> 几乎默认的配置就够用了，但是还想深入学习

优点：

​	1.适合大规模的数据恢复！ 

​	2.对数据的完整性要求不高

缺点

​	1.需要一定的时间间隔进程操作！ 如果redis意外宕机了，那么最后一个修改的数据就丢失了

​	2.fork进程的时候，会占用一定的内存空间



### AOF（Append Only File）（日志）

将我们的所有命令都记录下来（history），恢复的时候就将这个文件全部都执行一遍



以日志的形式来记录每一个写操作，将redis执行的所有指令都记录下来（读操作不记录），值追加文件但不可以写文件，redis启动之初会读取文件重新构建数据，也就是说，redis在重启的话就根据日志文件的内容，将写指令从头开始执行一次以恢复数据。

Aof保存的文件是 ==appendonly.aof== 文件

> 

![image-20200616200538840](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616200538840.png)

默认是不开启的，我们需要手动进行配置，只需要将 appenonly 改为 yes，这样就开启了aof

重启，redis的aof持久化就生效了

如果这个 aof 文件有错误（被恶意修改之类..），这时redis启动不起来

如果要修复这个文件，redis-check-aof --fix 

```bash
redis-check-aof --fix appendonly.aof
```



> redis重写规则

aof默认就是文件无限追加，文件会越来越大

如果aof文件大于64m，redis会fork一个新的进程将文件进行重写



![image-20200616200751431](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616200751431.png)



* aof 重写

aof 文件记录的是每一条redis命令，有时候我们会对某一个key进行多次set，中间会产生很多条命令，但是结果只有一个。

```shell
set name 1
...
set name 12
set name 123
...
set name 1234
set name 12345
```

例如我们执行了上述命令，aof 文件会记录着每一条命令。在redis重启时，会逐条执行上述的命令。但是其实可以精简为`set name 12345`，其余的几条命令其实没有意义。AOF重写就是实现了这个功能。

- **使用子进程来进行AOF重写时会遇到的问题**（来自博客 https://www.cnblogs.com/chichung/p/12687101.html）

问题：

子进程在进行AOF重写期间，服务器进程还要继续处理命令请求，而新的命令可能对现有的数据进行修改，这会让当前数据库的数据和重写后的AOF文件中的数据不一致。

解决方案：

要知道redis是怎么处理这个问题的，需要先了解下AOF重写的具体实现：

![img](https://img2020.cnblogs.com/blog/1414690/202004/1414690-20200412193117336-1589482807.png)

AOF文件重写过程与RDB快照bgsave工作过程有点相似，都是通过fork子进程，由子进程完成相应的操作，同样的在fork子进程简短的时间内，redis是阻塞的。

（1）开始`bgrewriteaof`，判断当前有没有`bgsave命令(RDB持久化)/bgrewriteaof`在执行，倘若有，则这些命令执行完成以后在执行。

（2）主进程`fork`出子进程，在这一个短暂的时间内，redis是阻塞的。

（3）主进程`fork`完子进程继续接受客户端请求。此时，客户端的写请求不仅仅写入`aof_buf`缓冲区，还写入`aof_rewrite_buf`重写缓冲区。一方面是写入`aof_buf`缓冲区并根据appendfsync策略同步到磁盘，保证原有AOF文件完整和正确。另一方面写入`aof_rewrite_buf`重写缓冲区，保存fork之后的客户端的写请求，防止新AOF文件生成期间丢失这部分数据。

（4.1）子进程写完新的AOF文件后，向主进程发信号，父进程更新统计信息。

（4.2）主进程把`aof_rewrite_buf`中的数据写入到新的AOF文件。

（5.）使用新的AOF文件覆盖旧的AOF文件，标志AOF重写完成。

> 优缺点

```bash
appendonly no  # 默认不开启。默认使用 rdb 模式持久化，大多数情况下，rdb够用了
appendfilename "appendonly.aof"		# 持久化文件名

appendfsync always。   每次有新命令追加到 AOF 文件时就执行一次 fsync ：非常慢，也非常安全。
appendfsync everysec。 每秒 fsync 一次：足够快（和使用 RDB 持久化差不多），并且在故障时只会丢失 1 秒钟的数据。
appendfsync no。       从不 fsync ：将数据交给操作系统来处理。更快，也更不安全的选择。
```

### RDB和AOF混合持久化

重启 Redis 时，我们很少使用 rdb 来恢复内存状态，因为会丢失大量数据。

如果使用 AOF 日志重放，性能则相对 rdb 来说要慢很多，这样在 Redis 实例很大的情况下，启动的时候需要花费很长的时间。

Redis 4.0 为了解决这个问题，带来了一个新的持久化选项——混合持久化。

混合持久化同样也是通过`bgrewriteaof`完成的，不同的是当开启混合持久化时，fork出的子进程先将共享的内存副本**全量**的以RDB方式写入aof文件，然后在将`aof_rewrite_buf`重写缓冲区的增量命令以AOF方式写入到文件，写入完成后通知主进程更新统计信息，并将新的含有RDB格式和AOF格式的AOF文件替换旧的的AOF文件。简单的说：新的AOF文件前半段是RDB格式的全量数据后半段是AOF格式的增量数据，如下图：

![img](https://img2020.cnblogs.com/blog/1414690/202004/1414690-20200412193142949-1690648191.png)

在redis重启的时候，加载 aof 文件进行恢复数据：先加载 rdb 内容再加载剩余的 aof。

混合持久化配置：

```shell
aof-use-rdb-preamble yes  # yes：开启，no：关闭
```



## Redis发布订阅

### 简介

Redis 发布订阅(pub/sub)是一种消息通信模式：发送者(pub)发送消息，订阅者(sub)接收消息。

Redis 客户端可以订阅任意数量的频道。

下图展示了频道 channel1 ， 以及订阅这个频道的三个客户端 —— client2 、 client5 和 client1 之间的关系：

![img](https://www.runoob.com/wp-content/uploads/2014/11/pubsub1.png)

当有新消息通过 PUBLISH 命令发送给频道 channel1 时， 这个消息就会被发送给订阅它的三个客户端：

![img](https://www.runoob.com/wp-content/uploads/2014/11/pubsub2.png)



### 使用场景：

1.实时消息系统

2.实时聊天室

3.订阅、关注系统

比较复杂的场景我们会使用 消息中间件（MQ）



### Redis 发布订阅命令

下表列出了 redis 发布订阅常用命令：

| 序号 | 命令及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | [PSUBSCRIBE pattern [pattern ...\]]    订阅一个或多个符合给定模式的频道。 |
| 2    | [PUBSUB subcommand [argument [argument ...\]]   查看订阅与发布系统状态。 |
| 3    | [PUBLISH channel message]   将信息发送到指定的频道。         |
| 4    | PUNSUBSCRIBE [pattern [pattern ...\]]    退订所有给定模式的频道。 |
| 5    | [SUBSCRIBE channel [channel ...\]]    订阅给定的一个或多个频道的信息。 |
| 6    | [UNSUBSCRIBE [channel [channel ...\]]]    指退订给定的频道。 |



### 测试

发送端

```bash
127.0.0.1:6379> publish gokufriday "hello,gokufriday's follower"	# 发布者发布消息的频道  并且发布消息
(integer) 1
127.0.0.1:6379> publish gokufriday "let's study redis now"		# 发布者发布消息的频道
(integer) 1
```



接收端

```bash
127.0.0.1:6379> subscribe gokufriday  # 订阅一个频道“gokufriday”
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "gokufriday"
3) (integer) 1
1) "message"		# 消息
2) "gokufriday"		# 那个发送消息的频道
3) "hello,gokufriday's follower"	# 消息的具体内容

1) "message"
2) "gokufriday"
3) "let's study redis now"

```



## Redis主从复制

![image-20200616213436610](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616213436610.png)



### 环境配置

只配置从库，不配置主库

```bash
127.0.0.1:6379> info replication  # 查看当前库的信息
# Replication
role:master		# 角色 master
connected_slaves:0 	# 没有从机
master_replid:5f9ef5e196e73d98942e107bfd34e646689047fc
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```



### 配置3个redis服务

复制3个配置文件，修改对应的信息

1.端口

2.pid 名

3.log文件名

4.dump.rdb 名



查看是否开启3个进程

```bash
ps -ef|grep redis
```

![image-20200616222056314](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616222056314.png)



### 一主二从

 ==默认情况下，每台redis服务器都是主机==： 我们配置一般只配置丛机

可以理解为 **认老大**  ：一主（79）二从（80、81）

![image-20200616222718833](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616222718833.png)



![image-20200616222832802](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616222832802.png)



![image-20200616222851303](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616222851303.png)



#### 配置主从机

￼真实的配置应该在配置文件中配置，这样的话是永久代，这里使用命令，只是暂时的（仅仅是学习,工作中不会使用）

```bash
slaveof host port
```

```bash
################   6380   ####################
#    配置从机
127.0.0.1:6380> SLAVEOF 127.0.0.1 6379   # SLAVEOF host 6379   认 6379 当主机
OK
127.0.0.1:6380> info replication
# Replication
role:slave		# 当前角色是从机
master_host:127.0.0.1   # 看到主机的信息
master_port:6379
master_link_status:up
master_last_io_seconds_ago:7
master_sync_in_progress:0
slave_repl_offset:14
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:c8d8322375b04d7325904023fcff0765f37855d0
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:14
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:14
################   6381   ####################
...
...
################   6379   ####################
#  在主机中查看
127.0.0.1:6379> info replication
# Replication
role:master   
connected_slaves:2    # 从机的信息
slave0:ip=127.0.0.1,port=6380,state=online,offset=238,lag=0
slave1:ip=127.0.0.1,port=6381,state=online,offset=238,lag=0
master_replid:c8d8322375b04d7325904023fcff0765f37855d0
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:238
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:238
```



### 细节

主机可以写，从机不能写只能读！主机中所有的信息和数据，都会被从机自动保存！



主机写

![image-20200616225835416](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616225835416.png)

从机只能读，不能写

![image-20200616225847792](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616225847792.png)



测试：主机断开连接。从机依旧能连接到主机，可以读取到主机之前已经写的信息。主机回来了，依旧能获取到主机新写的信息

如果是使用命令行，来配置的主从机，这时如果重启从机，马上从机就会和主机解绑了，变成原子的主机。只要再和原来的主机绑定，变为从机，就可以获取主机中的信息（在从机关机到重新变成从机这个过程中，主机写的信息，只要从机和主机绑定上了，依旧能获取到这段空窗期的信息）



>  复制原理

Slave 启动成功连接到 master 后会发送一个sync同步命令

Master 接到命令，启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进程执行 完毕之后，==master将传送整个数据文件到slave，并完成一次完全同步。== 

==全量复制==：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。
==增量复制==：Master 继续将新的所有收集到的修改命令依次传给slave，完成同步

但是只要是重新连接master，一次完全同步（全量复制）将被自动执行！ 我们的数据一定可以在从机中看到！



>  层层链路

上一个M链接下一个 S！

![image-20200616232728884](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200616232728884.png)

这时候也可以完成我们的主从复制！



> 如果没有老大了，这个时候能不能选择一个老大出来呢？ 手动！


如果主机断开了连接，我们可以使用 命令  SLAVEOF no one  让自己变成主机！其他的节点就可以手动连 接到新的这个主节点（手动）！如果这个时候老大修复了，那就重新连接！





## 哨兵模式

（自动选举老大的模式）

> 概述

主从切换技术的方法是：当主服务器宕机后，需要手动把一台从服务器切换为主服务器，这就需要人工 干预，费事费力，还会造成一段时间内服务不可用。这不是一种推荐的方式，更多时候，我们优先考虑 哨兵模式。Redis从2.8开始正式提供了Sentinel（哨兵） 架构来解决这个问题。

谋朝篡位的自动版，能够后台监控主机是否故障，==如果故障了根据投票数自动将从库转换为主库==。

哨兵模式是一种特殊的模式，首先Redis提供了哨兵的命令，哨兵是一个独立的进程，作为进程，它会独 立运行。其原理是哨兵通过发送命令，等待Redis服务器响应，从而监控运行的多个Redis实例。

![image-20200617091515144](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200617091515144.png)



 这里的哨兵有两个作用

* 通过发送命令，让Redis服务器返回监控其运行状态，包括主服务器和从服务器。 
* 当哨兵监测到master宕机，会自动将slave切换成master，然后通过发布订阅模式通知其他的从服 务器，修改配置文件，让它们切换主机。

然而一个哨兵进程对Redis服务器进行监控，可能会出现问题，为此，我们可以使用多个哨兵进行监控。 各个哨兵之间还会进行监控，这样就形成了多哨兵模式。

![image-20200617091647923](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200617091647923.png)



>  测试

目前的配置是一主二从

1.配置哨兵配置文件  sentinel.conf

```bash
# sentinel monitor 被监控的名称 host port 1
sentinel monitor myredis 127.0.0.1 6379 1
```

2.启动哨兵模式

```bash
redis-sentinel myconf/sentinel.conf

[root@gokufriday bin]# redis-sentinel myconf/sentinel.conf
16107:X 17 Jun 2020 09:41:31.671 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
16107:X 17 Jun 2020 09:41:31.671 # Redis version=6.0.5, bits=64, commit=00000000, modified=0, pid=16107, just started
16107:X 17 Jun 2020 09:41:31.671 # Configuration loaded
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 6.0.5 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in sentinel mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 26379
 |    `-._   `._    /     _.-'    |     PID: 16107
  `-._    `-._  `-./  _.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |           http://redis.io        
  `-._    `-._`-.__.-'_.-'    _.-'                                   
 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
 |    `-._`-._        _.-'_.-'    |                                  
  `-._    `-._`-.__.-'_.-'    _.-'                                   
      `-._    `-.__.-'    _.-'                                       
          `-._        _.-'                                           
              `-.__.-'                                               

16107:X 17 Jun 2020 09:41:31.672 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
16107:X 17 Jun 2020 09:41:31.675 # Sentinel ID is 535477f1fe5b4818cca6a80108cecca936241109
16107:X 17 Jun 2020 09:41:31.675 # +monitor master myredis 127.0.0.1 6379 quorum 1
16107:X 17 Jun 2020 09:41:31.676 * +slave slave 127.0.0.1:6380 127.0.0.1 6380 @ myredis 127.0.0.1 6379
16107:X 17 Jun 2020 09:41:31.678 * +slave slave 127.0.0.1:6381 127.0.0.1 6381 @ myredis 127.0.0.1 6379
```

如果主（Master）节点断开了，这时就会从从机中随机选择一个主服务器

![image-20200617095247471](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200617095247471.png)



哨兵日志

![image-20200617095348477](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200617095348477.png)



原本的主机连接之后发现自己变成从机了

![image-20200617095702068](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200617095702068.png)





> 哨兵模式

优点：

1.哨兵集群，基于主从复制模式，包含了所有的主从配置的优点

2.主从可以自动切换，故障可以转移，系统可用性更高

3.哨兵模式就是主从模式的升级，手动到自动，更加健壮



缺点：

1.Redis不好在线扩容，集群容量一旦到达上限，在线扩容就十分麻烦

2.实现哨兵模式的配置非常麻烦，有很多选择



> 哨兵模式全部配置  (博客: https://juejin.im/post/5b7d226a6fb9a01a1e01ff64)

```bash
# 哨兵sentinel实例运行的端口，默认26379  
port 26379
# 哨兵sentinel的工作目录
dir ./

# 哨兵sentinel监控的redis主节点的 
## ip：主机ip地址
## port：哨兵端口号
## master-name：可以自己命名的主节点名字（只能由字母A-z、数字0-9 、这三个字符".-_"组成。）
## quorum：当这些quorum个数sentinel哨兵认为master主节点失联 那么这时 客观上认为主节点失联了  
# sentinel monitor <master-name> <ip> <redis-port> <quorum>  
sentinel monitor mymaster 127.0.0.1 6379 2

# 当在Redis实例中开启了requirepass <foobared>，所有连接Redis实例的客户端都要提供密码。
# sentinel auth-pass <master-name> <password>  
sentinel auth-pass mymaster 123456  

# 指定主节点应答哨兵sentinel的最大时间间隔，超过这个时间，哨兵主观上认为主节点下线，默认30秒  
# sentinel down-after-milliseconds <master-name> <milliseconds>
sentinel down-after-milliseconds mymaster 30000  

# 指定了在发生failover主备切换时，最多可以有多少个slave同时对新的master进行同步。这个数字越小，完成failover所需的时间就越长；反之，但是如果这个数字越大，就意味着越多的slave因为replication而不可用。可以通过将这个值设为1，来保证每次只有一个slave，处于不能处理命令请求的状态。
# sentinel parallel-syncs <master-name> <numslaves>
sentinel parallel-syncs mymaster 1  

# 故障转移的超时时间failover-timeout，默认三分钟，可以用在以下这些方面：
## 1. 同一个sentinel对同一个master两次failover之间的间隔时间。  
## 2. 当一个slave从一个错误的master那里同步数据时开始，直到slave被纠正为从正确的master那里同步数据时结束。  
## 3. 当想要取消一个正在进行的failover时所需要的时间。
## 4.当进行failover时，配置所有slaves指向新的master所需的最大时间。不过，即使过了这个超时，slaves依然会被正确配置为指向master，但是就不按parallel-syncs所配置的规则来同步数据了
# sentinel failover-timeout <master-name> <milliseconds>  
sentinel failover-timeout mymaster 180000

# 当sentinel有任何警告级别的事件发生时（比如说redis实例的主观失效和客观失效等等），将会去调用这个脚本。一个脚本的最大执行时间为60s，如果超过这个时间，脚本将会被一个SIGKILL信号终止，之后重新执行。
# 对于脚本的运行结果有以下规则：  
## 1. 若脚本执行后返回1，那么该脚本稍后将会被再次执行，重复次数目前默认为10。
## 2. 若脚本执行后返回2，或者比2更高的一个返回值，脚本将不会重复执行。  
## 3. 如果脚本在执行过程中由于收到系统中断信号被终止了，则同返回值为1时的行为相同。
# sentinel notification-script <master-name> <script-path>  
sentinel notification-script mymaster /var/redis/notify.sh

# 这个脚本应该是通用的，能被多次调用，不是针对性的。
# sentinel client-reconfig-script <master-name> <script-path>
sentinel client-reconfig-script mymaster /var/redis/reconfig.sh
```





## Redis缓存穿透和雪崩

> 服务器的高可用问题

在这里我们不会详细的区分析解决方案的底层！

Redis缓存的使用，极大的提升了应用程序的性能和效率，特别是数据查询方面。但同时，它也带来了一 些问题。其中，要害的问题，就是数据的一致性问题，从严格意义上讲，这个问题无解。如果对数据 的一致性要求很高，那么就不能使用缓存。

另外的一些典型问题就是，缓存穿透、缓存雪崩和缓存击穿。目前，业界也都有比较流行的解决方案



（博客： https://juejin.im/post/5c9a67ac6fb9a070cb24bf34）

### 1 Redis缓存调用流程  

今天我们不牵涉多级缓存的知识，就把系统使用到的缓存方案，不管是一级还是多级的都统称为缓存，主要是为了讲述使用缓存的时候可能会遇到的一些问题以及一些解决办法。

我们使用缓存时，我们的业务系统大概的调用流程如下图：



![img](https://user-gold-cdn.xitu.io/2019/3/27/169bb2607456de97?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



当我们查询一条数据时，先去查询缓存，如果缓存有就直接返回，如果没有就去查询数据库，然后返回。这种情况下就可能会出现一些现象。

### 2 缓存穿透（查不到）

2.1 什么是缓存穿透

正常情况下，我们去查询数据都是存在。

那么==请求去查询一条压根儿数据库中根本就不存在的数据==，也就是缓存和数据库都==查询不到这条数据，但是请求每次都会打到数据库==上面去。

这种查询不存在数据的现象我们称为==**缓存穿透**==。

2.2 穿透带来的问题

试想一下，如果有黑客会对你的系统进行攻击，拿一个不存在的id 去查询数据，会产生大量的请求到数据库去查询。可能会导致你的数据库由于压力过大而宕掉。

2.3 解决办法

2.3.1 缓存空值

之所以会发生穿透，就是因为缓存中没有存储这些空数据的key。从而导致每次查询都到数据库去了。

那么我们就可以为这些key对应的值设置为null 丢到缓存里面去。后面再出现查询这个key 的请求的时候，直接返回null 。

这样，就不用在到数据库中去走一圈了，但是别忘了设置过期时间。

2.3.2 BloomFilter

BloomFilter 类似于一个hbase set 用来判断某个元素（key）是否存在于某个集合中。

这种方式在大数据场景应用比较多，比如 Hbase 中使用它去判断数据是否在磁盘上。还有在爬虫场景判断url 是否已经被爬取过。

这种方案可以加在第一种方案中，==在缓存之前在加一层 BloomFilter ，在查询的时候先去 BloomFilter 去查询 key 是否存在，如果不存在就直接返回==，存在再走查缓存 -> 查 DB。

流程图如下：



![img](https://user-gold-cdn.xitu.io/2019/3/27/169bb2638b91b339?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



2.4 如何选择

针对于一些恶意攻击，攻击带过来的大量key 是不存在的，那么我们采用第一种方案就会缓存大量不存在key的数据。

此时我们采用第一种方案就不合适了，我们完全可以先对使用第二种方案进行过滤掉这些key。

针对这种key异常多、请求重复率比较低的数据，我们就没有必要进行缓存，使用第二种方案直接过滤掉。

而对于空数据的key有限的，重复率比较高的，我们则可以采用第一种方式进行缓存。

### 3 缓存击穿（量太大，缓存过期）

3.1 什么是击穿

缓存击穿是我们可能遇到的第二个使用缓存方案可能遇到的问题。

在平常高并发的系统中，==大量的请求同时查询一个 key 时，此时这个key正好失效了，就会导致大量的请求都打到数据库上面去==。这种现象我们称为**缓存击穿**。

3.2 会带来什么问题

会造成某一时刻数据库请求量过大，压力剧增。

3.3 如何解决（分布式锁）

上面的现象是多个线程同时==去查询数据库的这条数据，那么我们可以在第一个查询数据的请求上使用一个 互斥锁来锁住它==。

其他的线程走到这一步拿不到锁就等着，等第一个线程查询到了数据，然后做缓存。后面的线程进来发现已经有缓存了，就直接走缓存。

### 4、缓存雪崩

4.1 什么是缓存雪崩

缓存雪崩的情况是说，当某一时刻发生==大规模的缓存失效==的情况，比如你==的缓存服务宕机了==，会有大量的请求进来直接打到DB上面。结果就是DB 称不住，挂掉。

4.2 解决办法

4.2.1 事前：

- **使用集群缓存，保证缓存服务的高可用**



这种方案就是在发生雪崩前对缓存集群实现高可用，如果是使用 Redis，可以使用 主从+哨兵 ，Redis Cluster 来避免 Redis 全盘崩溃的情况。

4.2.2 事中：

- **ehcache本地缓存 + Hystrix限流&降级,避免MySQL被打死**



使用 ehcache 本地缓存的目的也是考虑在 Redis Cluster 完全不可用的时候，ehcache 本地缓存还能够支撑一阵。

使用 Hystrix进行限流 & 降级 ，比如一秒来了5000个请求，我们可以设置假设只能有一秒 2000个请求能通过这个组件，那么其他剩余的 3000 请求就会走限流逻辑。

然后去调用我们自己开发的降级组件（降级），比如设置的一些默认值呀之类的。以此来保护最后的 MySQL 不会被大量的请求给打死。

4.2.3 事后：

- **开启Redis持久化机制，尽快恢复缓存集群**



一旦重启，就能从磁盘上自动加载数据恢复内存中的数据。

防止雪崩方案如下图所示：



![img](https://user-gold-cdn.xitu.io/2019/3/27/169bb265aa52e948?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)





### 5 解决热点数据集中失效问题

我们在设置缓存的时候，一般会给缓存设置一个失效时间，过了这个时间，缓存就失效了。

对于一些热点的数据来说，当缓存失效以后会存在大量的请求过来，然后打到数据库去，从而可能导致数据库崩溃的情况。

5.1 解决办法

5.1.1 设置不同的失效时间

为了避免这些热点的数据集中失效，那么我们在设置缓存过期时间的时候，我们让他们失效的时间错开。

比如在一个基础的时间上加上或者减去一个范围内的随机值。

5.1.2 互斥锁

结合上面的击穿的情况，在第一个请求去查询数据库的时候对他加一个互斥锁，其余的查询请求都会被阻塞住，直到锁被释放，从而保护数据库。

但是也是由于它会阻塞其他的线程，此时系统吞吐量会下降。需要结合实际的业务去考虑是否要这么做。



> 解决方案

* redis高可用 

这个思想的含义是，既然redis有可能挂掉，那我多增设几台redis，这样一台挂掉之后其他的还可以继续 工作，其实就是搭建的集群。（异地多活！）

* 限流降级

（在SpringCloud讲解过！）
这个解决方案的思想是，在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量。比如对 某个key只允许一个线程查询数据和写缓存，其他线程等待。

* 数据预热

数据加热的含义就是在正式部署之前，我先把可能的数据先预先访问一遍，这样部分可能大量访问的数 据就会加载到缓存中。在即将发生大并发访问前手动触发加载缓存不同的key，设置不同的过期时间，让 缓存失效的时间点尽量均匀。 