# ElasticSearch（初级）



版本 ElasticSearch 7.6.1

6.x 和 7.x 区别很大 ， 6.x的api （原生api、RestFul）



## 前言

SQL: like %某某某%，如果是大数据，搜索起来就非常慢，这时可以利用索引（作用不大）

ElasticSearch：搜索 （百度，github，淘宝...）

1.聊一个人

2.货币三家

3.安装

4.生态圈

5.分词器

6.RestFul操作  ElasticSearch

7.CRUD

8.SpringBoot集成 ElasticSearch（从原理分析开始）

9.爬虫爬取数据

10.实战：模拟全文搜索



大数据量的情况下，只要用到搜索的地方，考虑ElasticSearch

大数据两个问题： 存储 + 计算



## Lucene

lucene是一套信息检索工具包（jar包）

不包含： 搜索引擎系统

包含： 索引结构、读写索引的根据、常用的排序、搜索规则...



### lucene 和 ElasticSearch 的关系

**ElasticSearch**  是 基于 **lucene**  工具包做了一些封装和增强（上手简单）



## 1.ElasticSearch概述

ElasticSearch，简称es，es是一个开源的 **高扩展** 的 **分布式全文搜索引擎** ，它可以近乎 **实时的搜索、检索数据**。它的**扩展性**很好，可以扩展上百台服务器，处理PB级别（大数据）的数据。

es也使用java开发并使用 Lucene 作为核心来实现所有索引和搜索的功能，但是他的目的是通过简单的 ==RESTful==来隐藏 lunece 的复杂性，从而让全文搜索变得简单

据统计，在2016年1月，ElasticSearch 已经超过了 Solr 等，==成为排名第一的搜索引擎类应用==



### 谁在使用：

1、维基百科，类似百度百科，全文检索，高亮，搜索推荐（权重）

2、The Guardian（国外新闻网站），类似搜狐新闻，用户行为日志（点击，浏览，收藏，评论）+社交
网络数据（对某某新闻的相关看法），数据分析，给到每篇新闻文章的作者，让他知道他的文章的公众
反馈（好，坏，热门，垃圾，鄙视，崇拜）

3、Stack Overflow（国外的程序异常讨论论坛），IT问题，程序的报错，提交上去，有人会跟你讨论和
回答，全文检索，搜索相关问题和答案，程序报错了，就会将报错信息粘贴到里面去，搜索有没有对应
的答案

4、GitHub（开源代码管理），搜索上千亿行代码

5、电商网站，检索商品

6、日志数据分析，logstash采集日志，ES进行复杂的数据分析，**ELK**技术，**elasticsearch**+**logstash**+**kibana**

7、商品价格监控网站，用户设定某商品的价格阈值，当低于该阈值的时候，发送通知消息给用户，比如
说订阅牙膏的监控，如果高露洁牙膏的家庭套装低于50块钱，就通知我，我就去买

8、BI系统，商业智能，Business Intelligence。比如说有个大型商场集团，BI，分析一下某某区域最近
3年的用户消费金额的趋势以及用户群体的组成构成，产出相关的数张报表，**区，最近3年，每年消费
金额呈现100%的增长，而且用户群体85%是高级白领，开一个新商场。ES执行数据分析和挖掘，
Kibana进行数据可视化

9、国内：站内搜索（电商，招聘，门户，等等），IT系统搜索（OA，CRM，ERP，等等），数据分析
（ES热门
的一个使用场景）



## 2.ElasticSearch 和 Solr 的区别

### 2.1.ElasticSearch简介

Elasticsearch是一个实时分布式搜索和分析引擎。它让你以前所未有的速度处理大数据成为可能

它用于**全文搜索、结构化搜索、分析**以及将这三者混合使用

维基百科使用Elasticsearch提供全文搜索并高亮关键字，以及输入实时搜索(search-asyou-type)和搜索纠错(did-you-mean)等搜索建议功能

英国卫报使用Elasticsearch结合用户日志和社交网络数据提供给他们的编辑以实时的反馈，以便及时了解公众对新发表的文章的回应

StackOverflow结合全文搜索与地理位置查询，以及more-like-this功能来找到相关的问题和答案

Github使用Elasticsearch检索1300亿行的代码

Elasticsearch不仅用于大型企业，它还让像DataDog以及Klout这样的创业公司将最初的想法变成可扩展的解决方案。Elasticsearch可以在你的笔记本上运行，也可以在数以百计的服务器上处理PB级别的数据 

Elasticsearch是一个基于Apache Lucene(TM)的开源搜索引擎。无论在开源还是专有领域，Lucene可以被认为是迄今为止最先进、性能最好的、功能最全的搜索引擎库

但是，Lucene只是一个库。想要使用它，你必须使用Java来作为开发语言并将其直接集成到你的应用中，更糟糕的是，Lucene非常复杂，你需要深入了解检索的相关知识来理解它是如何工作的

Elasticsearch也使用Java开发并使用Lucene作为其核心来实现所有索引和搜索的功能，但是它的目的是通过简单的 RESTful API 来隐藏Lucene的复杂性，从而让全文搜索变得简单。



### 2.2.Solr简介

Solr 是Apache下的一个顶级开源项目，采用Java开发，它是基于Lucene的全文搜索服务器。Solr提供了比Lucene更为丰富的查询语言，同时实现了可配置、可扩展，并对索引、搜索性能进行了优化

Solr可以独立运行，运行在Jetty、Tomcat等这些Servlet容器中，Solr 索引的实现方法很简单，**用 POST方法向 Solr 服务器发送一个描述 Field 及其内容的 XML 文档，Solr根据xml文档添加、删除、更新索引**。Solr 搜索只需要发送 HTTP GET 请求，然后对 Solr 返回Xml、==json==等格式的查询结果进行解析，组织页面布局。Solr不提供构建UI的功能，Solr提供了一个管理界面，通过管理界面可以查询Solr的配置和运行情况

solr是基于lucene开发企业级搜索服务器，实际上就是封装了lucene

Solr是一个独立的企业级搜索应用服务器，它对外提供类似于==**Web-service**==的API接口。用户可以通过http请求，向搜索引擎服务器提交一定格式的文件，生成索引；也可以通过提出查找请求，并得到返回结果



### 2.3.Lucene简介

Lucene是apache软件基金会4 jakarta项目组的一个子项目，是一个开放源代码的全文检索引擎工具包，但它不是一个完整的全文检索引擎，而是一个全文检索引擎的架构，提供了完整的查询引擎和索引引擎，部分文本分析引擎（英文与德文两种西方语言）。**Lucene的目的是为软件开发人员提供一个简单易用的工具包，以方便的在目标系统中实现全文检索的功能，或者是以此为基础建立起完整的全文检索**
**引擎**。Lucene是一套用于全文检索和搜寻的开源程式库，由Apache软件基金会支持和提供。Lucene提供了一个简单却强大的应用程式接口，能够做全文索引和搜寻。==在Java开发环境里Lucene是一个成熟的免费开源工具。就其本身而言，Lucene是当前以及最近几年最受欢迎的免费Java信息检索程序库。==人们经常提到信息检索程序库，虽然与搜索引擎有关，但不应该将信息检索程序库与搜索引擎相混淆。

Lucene是一个全文检索引擎的架构。那什么是全文搜索引擎？

全文搜索引擎是名副其实的搜索引擎，国外具代表性的有Google、Fast/AllTheWeb、AltaVista、Inktomi、Teoma、WiseNut等，国内著名的有百度（Baidu）。它们都是通过从互联网上提取的各个网站的信息（以网页文字为主）而建立的数据库中，检索与用户查询条件匹配的相关记录，然后按一定的排列顺序将结果返回给用户，因此他们是真正的搜索引擎。

从搜索结果来源的角度，全文搜索引擎又可细分为两种，一种是拥有自己的检索程序（Indexer），俗称“蜘蛛”（Spider）程序或“机器人”（Robot）程序，并自建网页数据库，搜索结果直接从自身的数据库中调用，如上面提到的7家引擎；另一种则是租用其他引擎的数据库，并按自定的格式排列搜索结果，如Lycos引擎



### 2.4.ElasticSearch 和 Solr 比较



<img src="https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626195610650.png" alt="image-20200626195610650"  />



![image-20200626195904870](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626195904870.png)



![image-20200626195926481](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626195926481.png)



![image-20200626195954924](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626195954924.png)



#### ElasticSearch vs Solr 总结

1. ElasticSearch 开箱即用。 Solr 安装比较麻烦

2. Solr 利用 Zookeeper 进行分布式管理，而 ElasticSearch 自身带有分布式协调管理功能

3. Solr 支持更多格式的数据，比如JSON、XML、CSV，而 Elasticsearch 仅支持**json**文件格式

4. Solr 官方提供的功能更多，而 Elasticsearch 本身更注重于**核心功能**，高级功能多由第三方插件提供，例如图形化界面需要kibana友好支撑

5. Solr 查询快，但更新索引时慢（即插入删除慢），用于电商等查询多的应用

* ES建立索引快（即查询慢），**即实时性查询快**，用于facebook新浪等搜索

* Solr 是传统搜索应用的有力解决方案，但 Elasticsearch 更适用于新兴的实时搜索应用

6. Solr比较成熟，有一个更大，更成熟的用户、开发和贡献者社区，而 Elasticsearch相对开发维护者较少，更新太快，学习使用成本较高



## 3.ElasticSearch 安装

* JDK1.8 （最低要求）

* ElasticSearch 客户端，界面工具

ElasticSearch  基于 java 开发 ， ==ElasticSearch==  的版本和我们之后使用的 ==java的核心jar包== 要对应【版本对应】



官网：    https://www.elastic.co

下载地址·：  https://www.elastic.co/cn/downloads/elasticsearch





### 3.1.window下安装

#### 1.解压zip文件即可

![image-20200626202236043](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626202236043.png)



#### 2.目录

```shell
bin  		# 启动文件

config 		# 配置文件
	log4j2.properties		# 日志配置文件
	jvm.options				# 虚拟机相关配置文件
	elasticsearch.yml		# elasticsearch的配置文件   默认 9200 端口 ==》 跨域问题
lib			# 相关jar包
log			# 日志
modules		# 功能模块
plugins		# 插件   如：ik分词器...
```



#### 3.启动

* 启动  elasticsearch.bat

![image-20200626204549977](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626204549977.png)



* 出现闪退

修改  elasticsearch-env.bat 文件

```bat
if defined JAVA_HOME (
  set JAVA="%JAVA_HOME%\bin\java.exe"
  set JAVA_TYPE=JAVA_HOME
) else (
  set JAVA="%ES_HOME%\jdk\bin\java.exe"
  set JAVA_HOME="%ES_HOME%\jdk"
  set JAVA_TYPE=bundled jdk
)

============================
=====    修改为	==========

set JAVA="%ES_HOME%\jdk\bin\java.exe" 
set JAVA_HOME="%ES_HOME%\jdk" 
```



* 启动成功

![image-20200626205613588](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626205613588.png)



#### 4.访问 127.0.0.1:9200 测试

![image-20200626205754742](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626205754742.png)



### 3.2.安装可视化界面 es head 插件

需要 Node.js 环境

#### 1.下载地址

https://github.com/mobz/elasticsearch-head

![image-20200626210446316](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626210446316.png)

#### 2.在es head根目录下安装依赖

```
npm install
```

![image-20200626211526095](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626211526095.png)

![image-20200626211555220](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626211555220.png)



![image-20200626211702462](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626211702462.png)



#### 3.启动

```
npm run start
```

![image-20200626211842632](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626211842632.png)



#### 4.访问 9100

* 未能连接，需要解决跨域问题

![image-20200626212033183](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626212033183.png)

* 修改 elasticSearch/config 下的配置文件  elasticSearch.yml

![image-20200626212234645](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626212234645.png)

```yml
http.cors.enabled: true			# 开启跨域
http.cors.allow-origin: "*"		# 允许所有端口访问
```



* 重启 elasticSearch 服务器（elasticsearch.bat），访问 9100 端口
  * 初学者，可以把 es 当成一个数据库（可以建立索引【库】，文档【库中的数据】）

![image-20200626213347308](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626213347308.png)

![image-20200626213742707](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626213742707.png)

#### 5.注意点

这个 head 我们把它当做数据展示工具，之后所有的查询，选择 **Kibana** 来做



### 3.3.Kibana安装

#### 前言：了解 ELK


ELK是Elasticsearch、Logstash、Kibana三大开源框架首字母大写简称。市面上也被成为ElasticStack。其中**Elasticsearch**是一个基于Lucene、分布式、通过Restful方式进行交互的近**实时搜索平台框架**。像类似百度、谷歌这种大数据全文搜索引擎的场景都可以使用Elasticsearch作为底层支持框架，可见Elasticsearch提供的搜索能力确实强大,市面上很多时候我们简称Elasticsearch为es。**Logstash**是ELK的**中央数据流引擎**，用于从不同目标（文件/数据存储/MQ）收集的不同格式数据，经过过滤后支持输出到不同目的地（文件/MQ/redis/elasticsearch/kafka等）。**Kibana**可以将elasticsearch的**数据通过友好的页面展示出来**，提供实时分析的功能。

市面上很多开发只要提到ELK能够一致说出它是一个日志分析架构技术栈总称，但实际上ELK不仅仅适用于日志分析，它还可以支持其它任何数据分析和收集的场景，日志分析和收集只是更具有代表性。并非唯一性

![image-20200626215250896](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626215250896.png)



#### 1.下载Kibana

Kibana是一个针对Elasticsearch的开源分析及可视化平台，用来搜索、查看交互存储在Elasticsearch索引中的数据。使用Kibana，可以通过各种图表进行高级数据分析及展示。Kibana让海量数据更容易理解。它操作简单，基于浏览器的用户界面可以快速创建仪表板（dashboard）实时显示Elasticsearch查询动态。设置Kibana非常简单。无需编码或者额外的基础架构，几分钟内就可以完成Kibana安装并启动Elasticsearch索引监测



官网：https://www.elastic.co/cn/kibana

下载Kibana https://www.elastic.co/cn/downloads/kibana （注意版本要和 es 版本一致）

![image-20200626215520022](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626215520022.png)



#### 2.解压zip文件

* 解压Kibana需要较长时间，这是一个标准的工程（内容很多，还可以用来做一些通信、版本管理）

* 好处：ELK 基本上都是开箱即用



* 解压后的目录

![image-20200626221703932](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626221703932.png)



#### 3.启动

* 启动 Kibana.bat

![image-20200626221924390](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626221924390.png)

![image-20200626222005071](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626222005071.png)



#### 4.访问 5601 端口



![image-20200626222157911](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626222157911.png)



#### 5.开发工具

* 之后的所有操作都在这里编写

![image-20200626222500393](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626222500393.png)



#### 6.汉化

* 中文国际化配置文件 zh-CN.json

![image-20200626222749631](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626222749631.png)



* 修改 kibana.yml 配置文件
  * 国际化设置为中文

```yml
i18n.locale: "zh-CN"
```

![image-20200626222916803](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626222916803.png)

* 重启 Kibana 项目

![image-20200626223309740](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626223309740.png)



## 4.ES核心概念

### 概述

在前面的学习中，我们已经掌握了es是什么，同时也把es的服务已经安装启动，那么es是如何去存储数据，数据结构是什么，又是如何实现搜索的呢？我们先来聊聊ElasticSearch的相关概念吧！

==集群，节点，索引，类型，文档，分片，映射是什么？==



### 1.elasticsearch面向文档

* elasticsearch是**面向文档** 【一切都是 json 】

* **关系数据库 和 ES 的对比**

  elasticsearch(集群)中可以包含多个索引(数据库)，每个索引中可以包含多个类型(表)，每个类型下又包含多 个文档(行)，每个文档中又包含多个字段(列)

| Relational DB    | Elasticsearch              |
| ---------------- | -------------------------- |
| 数据库(database) | 索引(indices)              |
| 表(tables)       | types【types慢慢会被弃用】 |
| 行(rows)         | documents                  |
| 字段(columns)    | fields                     |



#### 物理设计

elasticsearch 在后台把**每个索引划分成多个分片**，每分分片可以在集群中的不同服务器间迁移

一个服务就是一个集群！默认的集群名称是 elasticsearch

![image-20200626225240309](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626225240309.png)



#### 逻辑设计

一个索引类型中，包含多个文档，比如说文档1，文档2。 当我们索引一篇文档时，可以通过这样的一各顺序找到 它: 索引 ▷ 类型 ▷ 文档ID ，通过这个组合我们就能索引到某个具体的文档。 注意:ID不必是整数，实际上它是个字 符串



### 2.文档

也就是一天天数据

```
user
1	zhangsan	22
2	lisi		15
```

elasticsearch是面向文档的，那么就意味着索引和搜索数据的最小单位是文档，elasticsearch中，文档有几个 重要属性 :

* 自我包含，一篇文档同时包含字段和对应的值，也就是同时包含 **key:value**！

* 可以是层次型的，一个文档中包含自文档，复杂的逻辑实体【一个json对象 使用fastjson进行自动转换】

* 灵活的结构，文档不依赖预先定义的模式，我们知道关系型数据库中，要提前定义字段才能使用，在elasticsearch中，对于字段是非常灵活的，有时候，我们可以忽略该字段，或者动态的添加一个新的字段。

尽管我们可以随意的新增或者忽略某个字段，但是，每个字段的类型非常重要，比如一个年龄字段类型，可以是字符 串也可以是整形。因为elasticsearch会保存字段和类型之间的映射及其他的设置。这种映射具体到每个映射的每种类型，这也是为什么在elasticsearch中，类型有时候也称为映射类型



### 3.类型

类型是文档的逻辑容器，就像关系型数据库一样，表格是行的容器。 类型中对于字段的定义称为映射，比如 name 映 射为字符串类型。 我们说文档是无模式的，它们不需要拥有映射中所定义的所有字段，比如新增一个字段，那么elasticsearch是怎么做的呢?elasticsearch会自动的将新字段加入映射，但是这个字段的不确定它是什么类型，elasticsearch就开始猜，如果这个值是18，那么elasticsearch会认为它是整形。 但是elasticsearch也可能猜不对， 所以最安全的方式就是提前定义好所需要的映射，这点跟关系型数据库殊途同归了，先定义好字段，然后再使用



### 4.索引

索引类似于关系型数据库中的数据库

索引是映射类型的容器，elasticsearch中的索引是一个非常大的文档集合。索引存储了映射类型的字段和其他设置。 然后它们被存储到了各个分片上了。 我们来研究下分片是如何工作的。

#### 物理设计 

* 物理设计：节点和分片 

* 如何工作

一个集群至少有一个节点，而一个节点就是一个elasricsearch进程，节点可以有多个索引默认的，如果你创建索引，那么索引将会有个5个分片 ( primary shard ,又称主分片 ) 构成的，每一个主分片会有一个副本 ( replica shard ,又称复制分片 )

![image-20200626231922464](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626231922464.png)

![image-20200626231659021](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626231659021.png)

上图是一个有3个节点的集群，可以看到主分片和对应的复制分片都不会在同一个节点内，这样有利于某个节点挂掉 了，数据也不至于丢失。 实际上，一个分片是一个Lucene索引，一个包含==倒排索引==的文件目录，倒排索引的结构使 得elasticsearch在不扫描全部文档的情况下，就能告诉你哪些文档包含特定的关键字



### 5.倒排索引

* elasticsearch使用的是一种称为倒排索引的结构，采用Lucene倒排索作为底层。这种结构 **适用于快速的全文搜索** ， 一个索引由文档中所有**不重复**的列表构成，对于每一个词，都有一个包含它的文档列表。 例如，现在有两个文档， 每个文档包含如下内容

```shell
Study every day, good good up to forever # 文档1包含的内容
To forever, study every day, good good up # 文档2包含的内容
```



* 为了创建倒排索引，我们首先要将每个文档拆分成独立的词(或称为词条或者tokens)，然后创建一个包含所有不重复的词条的排序列表，然后列出每个词条出现在哪个文档 

| Term    | doc_1 | doc_2 |
| ------- | ----- | ----- |
| Study   | √     | x     |
| To      | x     | √     |
| every   | √     | √     |
| forever | √     | √     |
| day     | √     | √     |
| study   | x     | √     |
| good    | √     | √     |
| every   | √     | √     |
| up      | √     | √     |
| to      | √     | x     |



* 试图搜索 to forever，只需要查看包含每个词条的文档  【score (权重) 】

| Term    | doc_1 | doc_2 |
| ------- | ----- | ----- |
| to      | √     | x     |
| forever | √     | √     |
| total   | 2     | 1     |

两个文档都匹配，但是第一个文档比第二个匹配程度更高。如果没有别的条件，现在，这两个包含关键字的文档都将返回



#### 示例

* 通过博客标签来搜索博客文章。那么倒排索引列表就是这样的一个结构



![image-20200626233418220](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200626233418220.png)



如果要搜索含有 python 标签的文章，那相对于查找所有原始数据而言，查找倒排索引后的数据将会快的多。只需要 查看标签这一栏，然后获取相关的文章ID即可。

过滤掉无关的所有数据，提高效率



### 6.ES 和 Lucene 的索引对比

在elasticsearch中， 索引 这个词被频繁使用，这就是术语的使用

在elasticsearch中，索引被分为多个分片，**每份 分片是一个Lucene的索引**。所以一==个elasticsearch索引是由多个Lucene索引组成的==。别
问为什么，谁让elasticsearch使用Lucene作为底层呢! 如无特指，说起索引都是指elasticsearch的索引。



接下来的一切操作都在kibana中Dev Tools下的Console里完成。基础操作



## 5.ES基础操作

### 5.1. IK分词器插件

#### 1.什么是IK分词器？

* 分词： 即把一段中文或者别的划分成一个个的关键字，我们在搜索时候会把自己的信息进行分词，会把数据库中或者索引库中的数据进行分词，然后进行一个匹配操作，默认的中文分词是将每个字看成一个词，比如 “我爱伯格曼” 会被分为"我","爱","伯","格"，"曼"，这显然是不符合要求的，所以我们需要**安装中文分词器ik**来解决这个问题 【使用中文，一般建议使用IK分词器】

* IK提供了两个分词算法**：ik_smart** 和 **ik_max_word**
  *  ik_smart 为最少切分
  * ik_max_word为最细粒度划分



#### 2.安装步骤

##### 1.下载地址

https://github.com/medcl/elasticsearch-analysis-ik/releases/tag/v7.6.1



##### 2.将下载的文件放入到插件中

点击进入到ES目录下的 plugins 中，新建 ik 目录 ，将下载的文件放入到 ik 目录中

![image-20200627002059243](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627002059243.png)



##### 3.重启ES

可以看到 ik 分词器被加载了

![image-20200627002808290](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627002808290.png)



##### 4.查看插件

可以通过命令 elasticsearch-plugin list 来查看插件，首先需要进入bin目录

![image-20200627003019735](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627003019735.png)



##### 5.使用Kibana测试

* ik_smart 【最小切分】

![image-20200627004454875](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627004454875.png)



* ik_max_word 【最细粒度划分，穷尽词库的可能】

![image-20200627004556023](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627004556023.png)



##### 6.自定义词库

* 输入“我认识伯格曼” ，发现"伯格曼"被拆分了
  * 这个时候需要自己将这个词加入到我们的分词器的字典中

![image-20200627005106440](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627005106440.png)



* 想让系统识别“伯格曼”是一个词，需要编辑自定义词库

  * 进入elasticsearch-7.6.1/plugins/ik/config目录
  * 新建一个myword.dic文件，编辑内容

  ![image-20200627005531946](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627005531946.png)

  

  * 修改IKAnalyzer.cfg.xml（在ik/config目录下）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
	<comment>IK Analyzer 扩展配置</comment>
	<!--用户可以在这里配置自己的扩展字典 -->
	<entry key="ext_dict">myword.dic</entry>
	 <!--用户可以在这里配置自己的扩展停止词字典-->
	<entry key="ext_stopwords"></entry>
	<!--用户可以在这里配置远程扩展字典 -->
	<!-- <entry key="remote_ext_dict">words_location</entry> -->
	<!--用户可以在这里配置远程扩展停止词字典-->
	<!-- <entry key="remote_ext_stopwords">words_location</entry> -->
</properties>
```



* 修改完配置重新启动elasticsearch，再次测试
  * 可以识别了

![image-20200627010201592](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627010201592.png)





## 6.关于索引的基本操作[附：Rest风格说明]

### 6.1.Rest风格说明

一种软件架构风格，而不是标准，只是提供了一组设计原则和约束条件。它主要用于客户端和服务器交互类的软件。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制



基本Rest命令说明：

| method | url地址                                         | 描述                   |
| ------ | ----------------------------------------------- | ---------------------- |
| PUT    | localhost:9200/索引名称/类型名称/文档id         | 创建文档（指定文档id） |
| POST   | localhost:9200/索引名称/类型名称                | 创建文档（随机文档id） |
| POST   | localhost:9200/索引名称/类型名称/文档id/_update | 修改文档               |
| DELETE | localhost:9200/索引名称/类型名称/文档id         | 删除文档               |
| GET    | localhost:9200/索引名称/类型名称/文档id         | 查询文档通过文档id     |
| POST   | localhost:9200/索引名称/类型名称/_search        | 查询所有数据           |



### 6.2.关于索引的基本操作

首先在浏览器 http://localhost:5601/ 进入 kibana里的Console


#### 1.创建索引

```
PUT /test01/type01/1

PUT /索引名称/类型名称/文档id
{请求体}
```

![image-20200627023358288](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627023358288.png)



这样就完成了自动增加索引，数据也成功添加了【看起来很像数据库，在学习的初期可以把它当做数据库来看】



![image-20200627023527132](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627023527132.png)



* 对比关系型数据库 ：
  PUT test1/type1/1 ： 索引test1相当于关系型数据库的库，类型type1就相当于表 ，1 代表数据中的主键 id
  * 这里需要补充的是 ，在elastisearch5版本前，一个索引下可以创建多个类型，但是在elastisearch5后，一个索引只能对应一个类型，而id相当于关系型数据库的主键id若果不指定就会默认生成一个20位的uuid，属性相当关系型数据库的column(列)。
  * 结果中的 result 则是操作类型，现在是 created ，表示第一次创建。如果再次点击执行该命令那么result 则会是 updated ，我们细心则会发现 _version 开始是1，现在你每点击一次就会增加一次。表示第几次更改



#### 2.类型【_type】

 name 这个字段用不用指定类型呢。毕竟我们关系型数据库 是需要指定类型的啊 !

* 字符串类型

  text 、 keyword

* 数值类型

  long, integer, short, byte, double, float, half_float, scaled_float

* 日期类型

  date

* 布尔值类型

  boolean

* 二进制类型

  binary

* 其他类型等等......



#### 3.指定字段类型

![image-20200627025004222](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627025004222.png)



#### 4.查看索引默认信息

![image-20200627025114456](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627025114456.png)



#### 5.不定义类型的情况

没有给字段指定类型，那么es就会默认给我配置字段类型

![image-20200627030121766](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627030121766.png)

![image-20200627030224794](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627030224794.png)



#### 6.查看ES中的索引的情况

```
GET /_cat/indices?v
```

![image-20200627031108609](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627031108609.png)



#### 7.修改索引

* 以前的方式

修改之后版本号 _version 增加

这种方式如果修改的时候漏了字段，数据会丢失

![image-20200627031502573](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627031502573.png)

* 现在的方式
  * 查看索引信息

![image-20200627031928663](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627031928663.png)

![image-20200627032015927](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627032015927.png)



#### 8.删除索引

![image-20200627032547067](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627032547067.png)



## 7.关于文档的基本操作

### 1.添加数据 PUT

![image-20200627033537072](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627033537072.png)

![image-20200627033615797](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627033615797.png)





* 再创建两个用户

![image-20200627034032566](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627034032566.png)

### 2.查询数据 GET

![image-20200627034147085](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627034147085.png)



### 3.更新数据 PUT

![image-20200627034539422](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627034539422.png)



### 4.更新数据 POST  _update【推荐使用这种更新方式】

![image-20200627035309689](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627035309689.png)

![image-20200627035108506](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627035108506.png)



### 5.简单的条件查询  _search?q

* 简单查询

```
GET /gokufriday/user/3
```



* 条件

```
GET /gokufriday/user/_search?q=name:张三
```



![image-20200627035935673](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627035935673.png)



### 6.复杂搜索  select

#### 1.构建查询【匹配查询】

![image-20200627043010659](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627043010659.png)



#### 2.查询字段过滤

我们仅是需要查看 name 和 age

![image-20200627043202428](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627043202428.png)



#### 3.排序

![image-20200627043730449](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627043730449.png)



#### 4.分页查询

![image-20200627044022090](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627044022090.png)



#### 5.bool查询【多条件查询】

##### 1. must （and）

**must命令（and），所有条件都要符合**  相当于 where name = xx and age = 3

![image-20200627044429147](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627044429147.png)





##### 2. should（or）

**should（or），只要有条件符合即可  相当于 where name = xx orage = 3**

![image-20200627044829162](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627044829162.png)



##### 3.must_not (not)



![image-20200627045042664](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627045042664.png)



##### 4.filter 【区间范围】

* gt       大于
* gte    大于等于
* lt        小于
* lte      小于等于



![image-20200627045516693](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627045516693.png)

![image-20200627045632169](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627045632169.png)

![image-20200627045932200](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627045932200.png)



#### 6.匹配多个条件查询

![image-20200627132118261](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627132118261.png)



#### 7.精确查询

**term查询是直接通过==倒排索引==指定的词条进行精确查找**



* **关于分词**
  * term：直接精确查询
  * match：使用分词器解析（先分析文档，然后通过分析的文档进行查询）



**两个类型   text   keyword**



##### 1.创建索引 testdb

![image-20200627135714463](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627135714463.png)



##### 2.存放两条数据

![image-20200627135652042](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627135652042.png)





##### 3.keyword 和 standard

* "analyzer": "keyword"

![image-20200627135838145](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627135838145.png)



* "analyzer": "standard"

![image-20200627135958676](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627135958676.png)



##### 4.term查询

* "name" 是 text类型

![image-20200627140857444](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627140857444.png)



* "desc" 是 keyword类型

![image-20200627141047256](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627141047256.png)



#### 8.多个值精确匹配

![image-20200627144026289](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627144026289.png)



#### 9.高亮查询

* 默认的高亮查询

![image-20200627145124140](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627145124140.png)



* 自定义格式高亮查询

![image-20200627145505670](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627145505670.png)



## 8.集成SpringBoot

### 8.1.官方文档

https://www.elastic.co/guide/index.html



![image-20200627150025326](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627150025326.png)

![image-20200627150153954](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627150153954.png)

![image-20200627150350004](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627150350004.png)

### 8.2.步骤【分析文档】

#### 1.原生依赖

```xml
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>7.6.1</version>
</dependency>
```



#### 2.构建对象

![image-20200627150813160](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627150813160.png)



#### 3.分析这个类中方法

##### 1.配置基本的项目

创建一个空项目，然后在这个项目里添加子模块

* 创建空项目

* 创建子模块（集成Springboot）



![image-20200627151716272](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627151716272.png)



* 配置jdk环境

![image-20200627151934304](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627151934304.png)

![image-20200627152003881](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627152003881.png)



![image-20200627152132464](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627152132464.png)



* JavaScript版本

![image-20200627152235226](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627152235226.png)



##### 2.版本不一致问题

==**问题： 一定要保证 我们导入的依赖和我们本地的es 版本一致**==

![image-20200627153038083](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627153038083.png)



* 修改依赖

![image-20200627153449112](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627153449112.png)



##### 3.源码中提供的对象

![image-20200627160253150](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627160253150.png)



* 虽然这里导入了三个类，但是他们都是 **RestClientConfigurations** 的静态内部类，核心类只有 **RestClientConfigurations**

```java
/*
 * Copyright 2012-2019 the original author or authors.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      https://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

package org.springframework.boot.autoconfigure.elasticsearch.rest;

import java.time.Duration;

import org.apache.http.HttpHost;
import org.apache.http.auth.AuthScope;
import org.apache.http.auth.Credentials;
import org.apache.http.auth.UsernamePasswordCredentials;
import org.apache.http.client.CredentialsProvider;
import org.apache.http.impl.client.BasicCredentialsProvider;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestClientBuilder;
import org.elasticsearch.client.RestHighLevelClient;

import org.springframework.beans.factory.ObjectProvider;
import org.springframework.boot.autoconfigure.condition.ConditionalOnClass;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.context.properties.PropertyMapper;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * Elasticsearch rest client infrastructure configurations.
 *
 * @author Brian Clozel
 * @author Stephane Nicoll
 */
class RestClientConfigurations {

   @Configuration(proxyBeanMethods = false)
   static class RestClientBuilderConfiguration {
	//	RestClientBuilder
      @Bean
      @ConditionalOnMissingBean
      RestClientBuilder elasticsearchRestClientBuilder(RestClientProperties properties,
            ObjectProvider<RestClientBuilderCustomizer> builderCustomizers) {
         HttpHost[] hosts = properties.getUris().stream().map(HttpHost::create).toArray(HttpHost[]::new);
         RestClientBuilder builder = RestClient.builder(hosts);
         PropertyMapper map = PropertyMapper.get();
         map.from(properties::getUsername).whenHasText().to((username) -> {
            CredentialsProvider credentialsProvider = new BasicCredentialsProvider();
            Credentials credentials = new UsernamePasswordCredentials(properties.getUsername(),
                  properties.getPassword());
            credentialsProvider.setCredentials(AuthScope.ANY, credentials);
            builder.setHttpClientConfigCallback(
                  (httpClientBuilder) -> httpClientBuilder.setDefaultCredentialsProvider(credentialsProvider));
         });
         builder.setRequestConfigCallback((requestConfigBuilder) -> {
            map.from(properties::getConnectionTimeout).whenNonNull().asInt(Duration::toMillis)
                  .to(requestConfigBuilder::setConnectTimeout);
            map.from(properties::getReadTimeout).whenNonNull().asInt(Duration::toMillis)
                  .to(requestConfigBuilder::setSocketTimeout);
            return requestConfigBuilder;
         });
         builderCustomizers.orderedStream().forEach((customizer) -> customizer.customize(builder));
         return builder;
      }

   }

   @Configuration(proxyBeanMethods = false)
   @ConditionalOnClass(RestHighLevelClient.class)
   static class RestHighLevelClientConfiguration {
	// RestHighLevelClient 高级客户端，也就是我们后面项目会用到的客户端
      @Bean
      @ConditionalOnMissingBean
      RestHighLevelClient elasticsearchRestHighLevelClient(RestClientBuilder restClientBuilder) {
         return new RestHighLevelClient(restClientBuilder);
      }

      @Bean
      @ConditionalOnMissingBean
      RestClient elasticsearchRestClient(RestClientBuilder builder,
            ObjectProvider<RestHighLevelClient> restHighLevelClient) {
         RestHighLevelClient client = restHighLevelClient.getIfUnique();
         if (client != null) {
            return client.getLowLevelClient();
         }
         return builder.build();
      }

   }

   @Configuration(proxyBeanMethods = false)
   static class RestClientFallbackConfiguration {
	// RestClient 普通的客户端
      @Bean
      @ConditionalOnMissingBean
      RestClient elasticsearchRestClient(RestClientBuilder builder) {
         return builder.build();
      }

   }

}
```





##### 4.配置类 ElasticSearchClientConfig

```java
// Spring几个步骤
// 1. 找对象
// 2. 放到Spring中待用
// 3. 如果是SpringBoot ，需要先分析源码
//      xxxAutoConfiguration   xxxPeoperties
@Configuration
public class ElasticSearchClientConfig {

    @Bean
    public RestHighLevelClient restHighLevelClient(){
        RestHighLevelClient client = new RestHighLevelClient(
                RestClient.builder(
                        new HttpHost("127.0.0.1", 9200, "http")
                        ));
        return client;
    }
}
```



### 8.3.具体的api测试

#### 1.SpringBoot默认的测试类

* @Autowried正确使用

![image-20200627171724291](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627171724291.png)



* @Autowried错误使用

![image-20200627171859288](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627171859288.png)



* 使用  @Qualifier

![image-20200627171948315](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627171948315.png)







#### 2.测试索引

##### 1.创建索引

```java
// 测试索引的创建  Request
@Test
void testCreateIndex() throws IOException {
    // 1. 创建索引请求
    CreateIndexRequest request = new CreateIndexRequest("gokufriday_index");
    // 2. 客户端执行请求  client.indices() ==》 IndicesClient   请求后获得响应
    CreateIndexResponse response = client.indices().create(request, RequestOptions.DEFAULT);
    System.out.println(response);
}
```



##### 2.判断索引是否存在

```java
// 测试获取索引，判断其是否存在
@Test
void testExistIndex() throws IOException {
    GetIndexRequest request = new GetIndexRequest("gokufriday_index");
    boolean exists = client.indices().exists(request, RequestOptions.DEFAULT);
    System.out.println("exists==>"+exists);
}
```



##### 3.删除索引

```java
// 测试删除索引
@Test
void testDeleteIndex() throws IOException {
    DeleteIndexRequest request = new DeleteIndexRequest("gokufriday_index");
    AcknowledgedResponse delete = client.indices().delete(request, RequestOptions.DEFAULT);
    System.out.println("AcknowledgedResponse==>"+delete.isAcknowledged());
}
```



#### 3.测试文档

##### 1.创建实体类 User

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Component
public class User {
    private String name;
    private int age;
}
```



##### 2.添加文档

* 先导入fastjson

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.62</version>
</dependency>
```

* 添加文档

```java
// 测试添加文档
@Test
void testAddDocument() throws IOException {
    //  创建对象
    User user = new User("gokufriday005", 23);
    // 创建请求
    IndexRequest request = new IndexRequest("gokufriday_index");

    // 规则 put /gokufriday_index/_doc/1
    request.id("1");
    request.timeout(TimeValue.timeValueSeconds(1));

    // 放入我们的请求
    request.source(JSON.toJSONString(user), XContentType.JSON);

    // 客户端发送请求，获取响应结果
    IndexResponse indexResponse = client.index(request, RequestOptions.DEFAULT);

    System.out.println(indexResponse.toString());	// 打印 json 字符串
    System.out.println(indexResponse.status());     // 打印命令返回状态
}
```



![image-20200627181227989](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627181227989.png)

![image-20200627181149134](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627181149134.png)





##### 3.获取文档

* 判断是否存在

```java
// 获取文档，判断是否存在  get /gokufriday_index/_doc/1
@Test
void testIsExits() throws IOException {
    GetRequest getRequest = new GetRequest("gokufriday_index", "1");
    // 不获取返回的 _source 的上下文 [非必要,默认有设置]
    getRequest.fetchSourceContext(new FetchSourceContext(false));
    getRequest.storedFields("_none_");

    boolean exists = client.exists(getRequest, RequestOptions.DEFAULT);
    System.out.println("isExists==>"+exists);
}
```



* 获取文档信息

```java
// 获取文档信息
@Test
void testGetDocument() throws IOException {
    GetRequest getRequest = new GetRequest("gokufriday_index", "1");
    GetResponse getResponse = client.get(getRequest, RequestOptions.DEFAULT);
    System.out.println("_source==>"+getResponse.getSourceAsString());    // 打印文档信息
    System.out.println(getResponse);    // 返回的内容和命令是一样的
}
```



##### 4.更新文档信息

```java
// 更新文档信息
@Test
void testUpdateDocument() throws IOException {
    UpdateRequest updateRequest = new UpdateRequest("gokufriday_index", "1");
    updateRequest.timeout("1s");

    User user = new User("updateGoku", 16);
    updateRequest.doc(JSON.toJSONString(user),XContentType.JSON);

    UpdateResponse updateResponse = client.update(updateRequest, RequestOptions.DEFAULT);
    System.out.println(updateResponse.toString()); // 打印 json 字符串
    System.out.println(updateResponse.status());     // 打印命令返回状态

}
```

![image-20200627183822943](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627183822943.png)



##### 5.删除文档信息

```java
// 删除文档信息
@Test
void testDeleteDocument() throws IOException {
    DeleteRequest deleteRequest = new DeleteRequest("gokufriday_index", "1");
    deleteRequest.timeout("1s");

    DeleteResponse deleteResponse = client.delete(deleteRequest, RequestOptions.DEFAULT);
    System.out.println(deleteResponse.status());

}
```

![image-20200627191944792](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627191944792.png)

![image-20200627192003524](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627192003524.png)



##### 6.批量插入数据

```java
// 批量插入数据
@Test
void testBulkRequest() throws IOException {
    BulkRequest bulkRequest = new BulkRequest();
    bulkRequest.timeout("10s");

    ArrayList<User> userList = new ArrayList<>();
    userList.add(new User("gokudu1",23));
    userList.add(new User("gokudu2",23));
    userList.add(new User("gokudu3",23));
    userList.add(new User("gokufriday1",19));
    userList.add(new User("gokufriday2",19));
    userList.add(new User("gokufriday3",19));

    for (int i = 0; i < userList.size() ; i++) {
        bulkRequest.add(
                new IndexRequest("gokufriday_index")
                .id(""+(i+1))
                .source(JSON.toJSONString(userList.get(i)), XContentType.JSON));
    }

    BulkResponse bulkResponse = client.bulk(bulkRequest, RequestOptions.DEFAULT);

    System.out.println(bulkResponse.hasFailures());  // 是否执行有失败的
}
```



* 返回 false   没有失败

![image-20200627195029312](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627195029312.png)

![image-20200627195109437](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627195109437.png)





* 不填充id数据，会默认生成随机id

```java
// 批量插入数据
    @Test
    void testBulkRequest() throws IOException {
        BulkRequest bulkRequest = new BulkRequest();
        bulkRequest.timeout("10s");

        ArrayList<User> userList = new ArrayList<>();
        userList.add(new User("gokudu1",23));
        userList.add(new User("gokudu2",23));
        userList.add(new User("gokudu3",23));
        userList.add(new User("gokufriday1",19));
        userList.add(new User("gokufriday2",19));
        userList.add(new User("gokufriday3",19));

        for (int i = 0; i < userList.size() ; i++) {
            // 批量更新和批量删除数据，在这里修改对应的请求即可
            // 这里不填充id数据，会默认生成随机id
            bulkRequest.add(
                    new IndexRequest("gokufriday_index")
//                    .id(""+(i+1))
                    .source(JSON.toJSONString(userList.get(i)), XContentType.JSON));
        }

        BulkResponse bulkResponse = client.bulk(bulkRequest, RequestOptions.DEFAULT);

        System.out.println(bulkResponse.hasFailures());  // 是否执行有失败的
    }
```

![image-20200627195515517](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627195515517.png)



##### 7.查询

```java
// 查询
    // searchRequest  搜索请求
    //      new SearchSourceBuilder   构建搜索条件
    //          QueryBuilders.termQuery       精确匹配
    //          QueryBuilders.matchAllQuery()    匹配所有
    //      new HighlightBuilder()    构建高亮
	//		
	//	xxxBuilder  对应ElasticSearch的所有命令  
    @Test
    void testSearch() throws IOException {
        SearchRequest searchRequest = new SearchRequest(EsConst.ES_INDEX01);
        // 构建搜索条件
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
//        new HighlightBuilder();
        // 查询条件    可以通过使用 QueryBuilders 工具类来实现
        //  QueryBuilders.termQuery       精确匹配
        //  QueryBuilders.matchAllQuery()    匹配所有
        TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("name", "gokufriday1");
//        MatchAllQueryBuilder matchAllQueryBuilder = QueryBuilders.matchAllQuery();
        // termQueryBuilder [精确匹配构建器] 放入 sourceBuilder [搜索条件构建器]
        sourceBuilder.query(termQueryBuilder);
        sourceBuilder.timeout(new TimeValue(60, TimeUnit.SECONDS));

        // 将 sourceBuilder 放入请求
        searchRequest.source(sourceBuilder);

        SearchResponse searchResponse = client.search(searchRequest, RequestOptions.DEFAULT);

        System.out.println(JSON.toJSONString(searchResponse.getHits()));
        System.out.println("===================================");
        for (SearchHit documentFields : searchResponse.getHits().getHits()) {
            System.out.println(documentFields.getSourceAsMap());
        }
    }
```

![image-20200627202721040](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627202721040.png)



## 9.实战

### 9.1.创建项目 es-jingdon

![image-20200627203426106](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627203426106.png)



#### 1.pom

* 修改springboot版本为 2.2.7
* 修改 elasticsearch 版本为 7.6.1

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.7.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.guo</groupId>
    <artifactId>es-jingdon</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>es-jingdon</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
        <!-- es依赖 保持和本地的一致 -->
        <elasticsearch.version>7.6.1</elasticsearch.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```



#### 2.application.properties

```properties
server.port=9099
# 关闭 thymeleaf 的缓存
spring.thymeleaf.cache=false
```



#### 3.导入静态资源

![image-20200627221929806](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200627221929806.png)





### 9.2.爬虫

>  数据问题？ （数据库获取，消息队列中获取，爬虫爬取数据）这些都可以成为数据源

爬取数据： 获取请求返回的页面信息，筛选出需要的数据

在java中使用 **jsoup** 包



#### 1.pom

```xml
<!-- 解析网页 -->
<dependency>
    <groupId>org.jsoup</groupId>
    <artifactId>jsoup</artifactId>
    <version>1.10.2</version>
</dependency>
```



#### 2.application.yml

```yml
server.port=9099
# 关闭 thymeleaf 的缓存
spring.thymeleaf.cache=false
```



#### 3.实体类Content

封装对象

```java
package com.guo.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@AllArgsConstructor
@NoArgsConstructor
@Data
public class Content {
    private String img;
    private String price;
    private String title;
}
```



#### 4.工具类HtmlParseUtil

psvm只是测试一下，需要删掉

```java
@Component
public class HtmlParseUtil {
    public static void main(String[] args) throws IOException {
        new HtmlParseUtil().parseJD("死亡搁浅").forEach(System.out::println);
    }

    public List<Content> parseJD(String keywords) throws IOException  {
        // 获取请求 https://search.jd.com/Search?keyword=java
        // 加上 "&enc=utf-8"   可以搜索中文
        String url = "https://search.jd.com/Search?keyword=" + keywords + "&enc=utf-8";
        // 解析网页  Jsoup返回的 Document 就是浏览器Document对象
        Document document = Jsoup.parse(new URL(url), 30000);
        // 所有在js的方法在这里都能用
        Element element = document.getElementById("J_goodsList");
        // 获取所有的 li 元素
        Elements liElement = element.getElementsByTag("li");

        List<Content> contentList = new ArrayList<>();

        // 获取 li 元素中的内容
        for (Element el : liElement) {
            // 关于这种图片的网站，所有的图片都是延迟加载的
            // source-data-lazy-img
            String img = el.getElementsByTag("img").eq(0).attr("src");
            String price = el.getElementsByClass("p-price").eq(0).text();
            String title = el.getElementsByClass("p-name").eq(0).text();

            Content content = new Content();
            content.setImg(img);
            content.setPrice(price);
            content.setTitle(title);

            contentList.add(content);
        }
        return contentList;
    }
}
```

![image-20200628183241899](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200628183241899.png)



#### 5.业务类ContentService

解析数据放入 es 索引中

```java
@Service
public class ContentService {

    @Autowired
    private RestHighLevelClient restHighLevelClient;

    // 1. 解析数据放入 es 索引中
    public Boolean parseContent(String keywords) throws IOException {
        List<Content> contents = new HtmlParseUtil().parseJD(keywords);
        // 把查询的数据放入 es 中
        BulkRequest bulkRequest = new BulkRequest();
        bulkRequest.timeout("1m");

        for (int i = 0; i < contents.size() ; i++) {
           bulkRequest.add(
                   new IndexRequest("jd_goods")
                   .source(JSON.toJSONString(contents.get(i)), XContentType.JSON)
           );
        }

        BulkResponse bulkResponse = restHighLevelClient.bulk(bulkRequest, RequestOptions.DEFAULT);

        return !bulkResponse.hasFailures();
    }
}
```



#### 6.ContentController

```java
@RestController
public class ContentController {

    @Autowired
    private ContentService contentService;

    @GetMapping("/parse/{keywords}")
    public Boolean parseContent(@PathVariable("keywords") String keywords) throws IOException {
        return contentService.parseContent(keywords);
    }
}
```



#### 7.访问  localhost:9099/parse/java 测试

![image-20200628201142194](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200628201142194.png)

![image-20200628201221839](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200628201221839.png)





### 9.3.实现搜索

#### 1.ContentService

```java
// 2. 获取这些数据，实现搜索功能
public List<Map<String,Object>> searchPage (String keywords,int pageNo,int pageSize) throws IOException {
    if (pageNo<=1) {
        pageNo = 1;
    }

    // 条件搜索
    SearchRequest searchRequest = new SearchRequest("jd_goods");
    SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();

    // 分页
    sourceBuilder.from(pageNo);
    sourceBuilder.size(pageSize);

    // 精准匹配关键字
    TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("title", keywords);
    sourceBuilder.query(termQueryBuilder);
    sourceBuilder.timeout(new TimeValue(60, TimeUnit.SECONDS));

    // 执行搜索
    // 将 sourceBuilder 放入请求
    searchRequest.source(sourceBuilder);
    SearchResponse searchResponse = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);

    // 解析结果
    List<Map<String,Object>> list = new ArrayList<>();
    for (SearchHit documentFields : searchResponse.getHits().getHits()) {
        list.add(documentFields.getSourceAsMap());
    }

    return list;
}
```





#### 2.contentService

```java
@GetMapping("/search/{keyword}/{pageNo}/{pageSize}")
public List<Map<String,Object>> search(@PathVariable("keyword") String keyword,
                                       @PathVariable("pageNo") int pageNo,
                                       @PathVariable("pageSize") int pageSize) throws IOException {

    return contentService.searchPage(keyword, pageNo, pageSize);
}
```



#### 3.导入vue，axios

![image-20200628211706220](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200628211706220.png)

![image-20200628211752197](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200628211752197.png)



#### 4.index.html

##### 1.测试搜索

确定能拿到搜索框输入 keyword 后，在对接后端的接口

```html
<script>    

    new Vue({
        el: '#app',
        data: {
            keyword: '',    // 搜索的关键字
            results: []     // 搜索的结果
        },
        methods: {
            searchKey() {
                let ketword = this.keyword;
                console.log(ketword);
            }
        }
    })

</script>
```



* 绑定监听@click:（以监听click为例，其他如keyup，用法类似）
  *  v-on:click="fun"
    * @click="fun"
    *  @click="fun(参数)
* @click.stop与@click.prevent
  * @click.stop 阻止事件冒泡
  * @click.prevent 阻止事件的默认行为



![image-20200628212954991](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200628212954991.png)



![image-20200628213431351](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200628213431351.png)



##### 2.对接后端接口

```html
new Vue({
    el: '#app',
    data: {
        keyword: '',    // 搜索的关键字
        results: []     // 搜索的结果
    },
    methods: {
        searchKey() {
            let ketword = this.keyword;
            console.log(ketword);
            // 对接后端接口
            axios.get('search/'+ketword+"/1/20").then(response=>{
                console.log(response);
            })
        }
    }
})
```

![image-20200628213821281](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200628213821281.png)



##### 3.渲染数据

response.data 从响应中拿到数据，赋给results

在 div 中遍历results

```html
<script>

    new Vue({
        el: '#app',
        data: {
            keyword: '',    // 搜索的关键字
            results: []     // 搜索的结果
        },
        methods: {
            searchKey() {
                let ketword = this.keyword;
                console.log(ketword);
                // 对接后端接口
                axios.get('search/'+ketword+"/1/20").then(response=>{
                    console.log(response);
                    this.results = response.data;    // 绑定数据
                })
            }
        }
    })

</script>
```



![image-20200628215052136](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200628215052136.png)



![image-20200628215138270](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200628215138270.png)



### 9.4.搜索标题高亮

* 在9.3实现搜索的基础上，修改
* 修改的部分

```java
...
// 高亮
HighlightBuilder highlightBuilder = new HighlightBuilder();
highlightBuilder.field("title");
highlightBuilder.requireFieldMatch(true);  //  false : 多个高亮显示
highlightBuilder.preTags("<span style='color:red'>");
highlightBuilder.postTags("</span>");

sourceBuilder.highlighter(highlightBuilder);java
...
// 解析结果
    List<Map<String,Object>> list = new ArrayList<>();
    for (SearchHit hit : searchResponse.getHits().getHits()) {

        // 解析高亮字段
        Map<String, HighlightField> highlightFields = hit.getHighlightFields();
        HighlightField titleField = highlightFields.get("title");

        Map<String, Object> sourceAsMap = hit.getSourceAsMap(); // 原来的结果

        // 将原来的字段换为现在高亮的字段
        if (titleField != null){
            Text[] fragments = titleField.fragments();
            String highLightTitle = "";
            for (Text text : fragments) {
                highLightTitle += text;
            }
            sourceAsMap.put("title", highLightTitle);   // 高亮字段替换原来的内容
        }

        list.add(sourceAsMap);
    }
```


* 完整代码

```java
// 3. 获取这些数据，实现搜索功能  (搜索高亮)
public List<Map<String,Object>> searchPageHighLight (String keywords,int pageNo,int pageSize) throws IOException {
    if (pageNo<=1) {
        pageNo = 1;
    }

    // 条件搜索
    SearchRequest searchRequest = new SearchRequest("jd_goods");
    SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();

    // 分页
    sourceBuilder.from(pageNo);
    sourceBuilder.size(pageSize);

    // 精准匹配关键字
    TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("title", keywords);
    sourceBuilder.query(termQueryBuilder);
    sourceBuilder.timeout(new TimeValue(60, TimeUnit.SECONDS));

    // 高亮
    HighlightBuilder highlightBuilder = new HighlightBuilder();
    highlightBuilder.field("title");
    highlightBuilder.requireFieldMatch(true);  //  false : 多个高亮显示
    highlightBuilder.preTags("<span style='color:red'>");
    highlightBuilder.postTags("</span>");

    sourceBuilder.highlighter(highlightBuilder);

    // 执行搜索
    // 将 sourceBuilder 放入请求
    searchRequest.source(sourceBuilder);
    SearchResponse searchResponse = restHighLevelClient.search(searchRequest, RequestOptions.DEFAULT);

    // 解析结果
    List<Map<String,Object>> list = new ArrayList<>();
    for (SearchHit hit : searchResponse.getHits().getHits()) {

        // 解析高亮字段
        Map<String, HighlightField> highlightFields = hit.getHighlightFields();
        HighlightField titleField = highlightFields.get("title");

        Map<String, Object> sourceAsMap = hit.getSourceAsMap(); // 原来的结果

        // 将原来的字段换为现在高亮的字段
        if (titleField != null){
            Text[] fragments = titleField.fragments();
            String highLightTitle = "";
            for (Text text : fragments) {
                highLightTitle += text;
            }
            sourceAsMap.put("title", highLightTitle);   // 高亮字段替换原来的内容
        }

        list.add(sourceAsMap);
    }

    return list;
}
```



* 解析

![image-20200628224144401](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200628224144401.png)