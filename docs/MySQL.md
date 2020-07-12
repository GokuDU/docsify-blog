# MySQL

## MySQl简介

* 由瑞典MySQL AB 公司开发，属于 Oracle 旗下产品
* MySQL 是最流行的关系型数据库管理系统
* 关系数据库将数据保存在不同的表中
* 所使用的 SQL 语言是用于访问数据库的最常用标准化语言
* 体积小、速度快、成本低

官网文档： https://dev.mysql.com/doc/refman/5.7/en/

### 学习思路

对比：SQLyog的可视化操作，查看历史记录

必须记住基本的固定语法和关键字

## 1.初始MySQL

javaEE；企业级java开发 web

前端  （页面【展示，数据】）

后台 （连接点【连接数据库jdbc】，连接前端【控制：控制视图跳转，给前端传递数据】）

数据库（存数据）

### 1.1.什么是数据库

数据库（DataBase，DB）

概念：数据仓库，安转在操作系统之上，存储大量数据

作用：存储数据，管理数据

### 1.2.数据库分类

关系型数据库（SQL）

* MySQL，Oracle...
* 通过表与表之间，行和列之间的关系来存储数据

非关系型数据库（NOSQL）

* Redis，MongDB
* 非关系型数据库，对象存储，通过对象自身的属性来存储

### 1.3.DBMS（数据库管理软件）

* 数据库的管理软件，科学有效的管理数据。维护和获取数据

### 1.4.连接数据库

命令行连接

```sql
mysql -uroot -p
mysql -uroot -p123456    --连接数据库
```

```sql
-- 所有的语句都用分号结尾
flush privileges; 		--刷新权限
show databases;		--查看所有数据库

mysql> use school	--切换数据库
Database changed

show tables;		--查看当前数据库中的所有表
describe student;	--显示当前数据库中具体表的信息

create database first_study;	--创建数据库

exit;				--退出连接
```

**数据库 xx 语言**（CRUD）

DDL	定义

DML	操作

DQL	查询

DCL	控制

## 2.操作数据库

操作数据库>操作数据库中的表>操作表中的数据

### 2.1.操作数据库

#### 1.创建数据库

```sql
CREATE DATABASE first_study; 
CREATE DATABASE IF NOT EXISTS first_study;	--创建数据库
```

#### 2.删除数据库

```sql
DROP DATABASE hello;
DROP DATABASE IF EXISTS hello;
```

#### 3.使用数据库

```sql
USE first_study;
USE `school`;	--使用反引号，如果表名或字段名为特殊字符，需要加``
```

#### 4.查看数据库

```sql
shwo DATABASES;
```

### 2.2.数据库的列类型

#### 数值

* ==int			标准的整数               4字节==  【常用】
* bigint      较大的数据                8字节
* float         浮点数                     4字节
* double      浮点数                   8字节
* decimal    字符串形式的浮点数      【金融计算使用】

#### 字符串

* char           字符串固定大小      0~255
* varchar          可变字符串         0~65535      常用   String、
* tinytext           微型文本           2^8-1
* text                  大文本               2^16-1

#### 时间日期

* date   YYYY-MM-DD  日期格式
* time    HH:mm:ss     时间格式
* ==datetime     YYYY-MM-DD HH:mm:ss  最常用的时间格式==
* ==timestamp  时间戳    1970.1.1 到现在的毫秒数==
* year    年

#### null

* 没有值，未知
* 避免使用null进行运算

### 2.3.数据库的字段属性【重点】

#### Unsigned：

* 无符号的整数
* 声明该列不能定义为负数

#### Zerofill：

* 不足的位数，使用0来填充   
  *  int（M）     
  * int 比较特殊    如果是varchar这个M就代表存储大小
  * M指的是最大显示宽度，对打有效显示宽度为225，显示宽度与存储大小无关

#### 自增

* 自动在上一条记录的基础上 +1（默认）
* 通常用来设计唯一的主键  index  ，必须是整数类型
* 可以自定义设计主键自增的起始值和步长

#### 非空  not null

* 设置为 not null ，如果不赋值，会报错
* null，如果不填写值，默认为null

#### 默认

* 设置默认的值

#### 【拓展】

```
真实开发项目，每一个表都必须存在以下五个字段，表示一个记录的存在用意

id     主键
`version`    乐观锁
is_delete    伪删除
gmt_create   创建时间
gmt_update   修改时间
```

### 2.4.创建数据库表

```sql
--表的名称和字段 尽量使用反引号 ``
--AUTO_INCREMENT  自增	
--字符串使用单引号 ''
--所有的语句最后加英文逗号  ，  最后一个不用
--primary key  主键 ， 一般一个表只有一个唯一的主键	
create table if not exists `student`(
    `id` int(4) not null auto_increment comment '学号',
    `name` varchar(20) not null default '匿名' comment '姓名',
    `pwd` varchar(20) not null default '123456' comment '密码',
    `sex` varchar(2) not null default '女' comment '性别',
    `birthday` datetime default NULL COMMENT '出生日期',
    `address` varchar(100) default null comment '家庭住址', 
    `email` varchar(50) default null comment '邮箱',
    primary key(`id`)		
)engine=innodb default charset=utf8
```

```sql
SHOW CREATE DATABASE school   -- 查看创建数据库的语句
```

CREATE DATABASE `school` /*!40100 DEFAULT CHARACTER SET utf8 */

```sql
SHOW CREATE TABLE student     -- 查看student数据表的定义语句
```

CREATE TABLE `student` (
  `id` int(4) NOT NULL AUTO_INCREMENT COMMENT '学号',
  `name` varchar(20) NOT NULL DEFAULT '匿名' COMMENT '姓名',
  `pwd` varchar(20) NOT NULL DEFAULT '123456' COMMENT '密码',
  `sex` varchar(2) NOT NULL DEFAULT '女' COMMENT '性别',
  `birthday` datetime DEFAULT NULL COMMENT '出生日期',
  `address` varchar(100) DEFAULT NULL COMMENT '家庭住址',
  `email` varchar(50) DEFAULT NULL COMMENT '邮箱',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8

```sql
DESC student   -- 显示表的结构
```

![image-20200606181422911](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200606181422911.png)

### 2.5.数据库引擎

```sql
-- 数据库引擎
/*
  INNODB  默认使用
  MYISAM  早些年使用 
*/
```

|            |        MYISAM        |        INNODB        |
| :--------: | :------------------: | :------------------: |
|  事务支持  | 不支持（最新版支持） |         支持         |
| 数据行锁定 |        不支持        |         支持         |
|  外键约束  |        不支持        |         支持         |
|  全文索引  |         支持         | 不支持（最新版支持） |
| 表空间大小 |         较小         |    较大，约为2倍     |

* MYISAM   节约空间，速度较快
* INNODB   安全性高，事务的处理，多表多用户操作

#### 1.物理空间储存位置

所有的数据库文件都存在data目录下，一个文件夹对应一个数据库

本质还是文件从存储

MySQL引擎在物理文件上的区别

* InnoDB在数据库表中只有一个 ***.frm** 文件，以及其上级目录data目录下的 **ibdata1**  文件
* MYISAM 对应的文件
  * *.frm  表结构的定义文件
  * *.MYD   数据文件（data）
  * *.MYI    索引文件（index）

#### 2.设置数据库中表的字符集编码

```sql
CHARSET=utf8
```

必须设置，不设置的话，会使用MySQL默认的字符集编码（不支持中文）

我们可以在 **my.ini** 中配置默认的编码

```ini
character-set-server=utf8
```

### 2.6.修改和删除表

#### 1.修改表

```sql
-- 修改表名   ALTER TABLE 旧表名 RENAME AS 新表名
ALTER TABLE teacher RENAME AS teacher1
-- 增加表的字段	  ALTER TABLE 表名 ADD 字段名 属性
ALTER TABLE teacher1 ADD age INT(11)
-- 修改表的字段（修改约束，重命名）
-- ALTER TABLE 表名 MODIFY 字段名 列属性()
ALTER TABLE teacher1 MODIFY age VARCHAR(11)   -- 修改约束
-- ALTER TABLE 表名 CHANGE 旧字段名 新字段名 列属性()
ALTER TABLE teacher1 CHANGE age age1 INT(3)   -- 重命名	

-- 删除表的字段
-- ALTER TABLE 表名 DROP 字段名
ALTER TABLE teacher1 DROP age1
```

#### 2.删除

```sql
-- 删除表 （如果表存在再删除）
-- DROP TABLE IF EXISTS 表名
DROP TABLE IF EXISTS teacher1
```

==所有的创建和删除尽量加上判断==

## 3.数据库的数据管理

### 3.1.外键【了解】

#### 1.方式一

* grade表

```sql
CREATE TABLE `grade`(
    `gradeid` INT(10) NOT NULL AUTO_INCREMENT COMMENT '年级id',
    `gradename` VARCHAR(50) NOT NULL COMMENT '年级名称',
    PRIMARY KEY(`gradeid`)
)ENGINE=INNODB DEFAULT CHARSET=utf8 
```

* student表

```sql
-- primary key  主键 ， 一般一个表只有一个唯一的主键	
-- CONSTRAINT 约束名 FOREIGN KEY (作为外键的列) REFERENCES 被引用表(哪个字段)
CREATE TABLE IF NOT EXISTS `student`(
    `id` INT(4) NOT NULL AUTO_INCREMENT COMMENT '学号',
    `name` VARCHAR(20) NOT NULL DEFAULT '匿名' COMMENT '姓名',
    `pwd` VARCHAR(20) NOT NULL DEFAULT '123456' COMMENT '密码',
    `sex` VARCHAR(2) NOT NULL DEFAULT '女' COMMENT '性别',
    `birthday` DATETIME DEFAULT NULL COMMENT '出生日期',
    `gradeid` INT(10) NOT NULL COMMENT '学生的年级',
    `address` VARCHAR(100) DEFAULT NULL COMMENT '家庭住址', 
    `email` VARCHAR(50) DEFAULT NULL COMMENT '邮箱',
    PRIMARY KEY(`id`),	
    KEY `FK_gradeid` (`gradeid`),
    CONSTRAINT `FK_gradeid` FOREIGN KEY (`gradeid`) REFERENCES `grade`(`gradeid`)
)ENGINE=INNODB DEFAULT CHARSET=utf8   
```



![image-20200606192936642](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200606192936642.png)

* 删除有外键关系的表的时候，先删除引用别人的表，再删除被引用的表

#### 2.方式二

创建表成功后，添加外键约束

* 创建表的时候没有外键关系:

```sql
-- 创建表的时候没有外键关系
-- ALTER TABLE 表 ADD CONSTRAINT 约束名 FOREIGN KEY (作为外键的列) REFERENCES 被引用表(哪个字段)
ALTER TABLE `student`
ADD CONSTRAINT `FK_gradeid` FOREIGN KEY (`gradeid`) REFERENCES `grade`(`gradeid`)
```

**以上的操作都是物理外键，数据库级别的外键，不建议使用（避免数据库过多造成困扰）**

==最佳实践==

* 数据库就是单纯的表，只用来存数据，只有行（数据）和列（字段）
* 想使用多张表的数据，想使用外键（程序去实现）

### 3.2.DML语言【掌握】

**数据库意义**：数据存储，数据管理

DML语言：数据操作语言

* insert
* update
* delete

### 3.3.添加

语法：==insert into 表名（字段一,字段二,字段三,...）VALUES（'值一','值二','值三',...）,(...),(...)== 

```sql
-- insert into 表名（字段一,字段二,字段三,...）VALUES（'值一','值二','值三',...）,(...),(...) 
INSERT INTO `grade` (`gradename`) VALUES('大一');
-- 由于主键自增，可以省略主键
-- 写插入语句，一定要一一对应
INSERT INTO `grade` (`gradename`) 
VALUES('大二'),('大三'),('大四')

-- 插入多条数据
INSERT INTO `student` 
(`name`,`pwd`,`sex`,`birthday`,`gradeid`,`address`,`email`)
VALUES 
('李四','111111','男','1988-02-15',4,'广州白云','lisi@si4,com'),
('王五','111111','男','1985-01-20',4,'潮州潮安','wangwu@wu5,com')
```

注意事项

* 字段之间用 英文逗号 隔开
* 字段可以省略（添加部分），但是 values 后面的值一定要一一对应
* 可以同时插入多条数据，values 每条数据之间用括号隔开  values (...),(...),(...)

### 3.4.修改

update 修改谁  set 字段=新值（修改的条件）

```sql
-- 修改学生名字
-- UPDATE 表名 SET 字段名='新值' WHERE [条件]
update `student` set name='伯格曼' where id=5
-- 在不指定条件的情况下 会删除所有的表

-- UPDATE 表名 SET 字段一='新值'，字段二='新值'，字段三='新值' WHERE [条件]
UPDATE `student` SET NAME='洪尚秀',pwd='123456',address='韩国' WHERE id=4
```

条件： where子句  运算符  id =某个值，>某个值 ，在某个区间

| 操作符         | 含义       | 范围        | 结果  |
| -------------- | ---------- | ----------- | ----- |
| =              | 等于       | 1=2         | false |
| <>或！=        | 不等于     | 1<>2        | true  |
| >,<,>=,<=      | …          | …           | …     |
| between… and … | 在某个区间 | [1,3]       |       |
| and            | &&         | 1=2 and 1<2 | false |
| or             | \|\|       | 1=2 and 1<2 | true  |

```sql
-- 通过多个条件
UPDATE `student` SET `name`='abcd' WHERE NAME='aaaaa' AND sex='男' 
-- 修改多个属性  用英文逗号 ,  隔开
UPDATE `student` SET `birthday`=CURRENT_DATE,gradeid=3 WHERE NAME='cc'
-- 通过变量赋值
UPDATE `student` SET SET `birthday`=CURRENT_DATE WHERE NAME='cc'
```

 注意：

* 写SQL语句时数据库的字段（列），尽量加``
* 修改的时候如果没有条件，会修改所有的值
* value（新赋的值），可以是一个具体的之，也可以是一个变量  
  *  例如：  SET `birthday`=CURRENT_DATE

### 3.5.删除

```sql
-- 删除数据 （避免这样写）
DELETE FROM `student` WHERE id=11   -- 不会影响自增

-- 清空某张表
TRUNCATE `student`   -- 自增会归零
```

delete 和 TRUNCATE 区别

* 相同点：都能删除数据，都不会删除表结构（如：不会影响自增）
* 不同：
  * TRUNCATE  重新设置 自增会归零
  * TRUNCATE  不会影响事务

delete后重启数据库，现象

* InnoDB 自增会从1开始（存储在内存中，断电即失）
* MyISAM 继续从上一个自增量开始（存储在文件中，不会丢失）

## 4.DQL查询数据【最重点】



### 4.1DQL

（Data Query Language：数据查询语言）

* 所有的查询操作都用它  select
* 简单的查询，复杂的查询都能做
* ==数据库中最核心的语言==
* 使用频率最高的语句

### 4.2.指定查询字段

==语法：SELECT 字段 FROM 表==

```sql
-- 查询所有学生
SELECT * FROM student
-- 指定字段查询
SELECT `studentno`,`studentname` FROM student
-- 给结果其别名  AS 可以给字段其别名 也可以给表起别名
SELECT `studentno` AS 学号,`studentname` AS 学生姓名 FROM student AS 学生表
-- 函数 CONCAT(a,b) 
SELECT 
CONCAT('姓名：',`studentname`) 
AS 新的名字
FROM student
```

==去重: distinct==

作用：去掉select查询出来中重复的数据，值保留一条

```sql
-- 查询哪一些同学参加了考试
select * from result  -- 查询全部的考试成绩
-- 查询哪些同学参加了考试
select `studentno` from result
-- distinct 去重
select distinct `studentno` from result
```

其他一些查询（表达式）

```sql
select version()		-- 查询系统版本		(函数)
select 59+55*5 as 运算结果	-- 计算			(表达式)
select @@auto_increment_increment -- 查询自增的步长	(变量)

-- 学生考试成绩 +1分
SELECT `studentno`,`studentresult` FROM `result`
-- 加一分后
SELECT `studentno`,`studentresult`+1 AS '加一分后' FROM `result`
```

数据库中的表达式：文本值，列，Null，函数。计算表达式，系统变量，

select ==表达式== from 表

### 4.3.where条件子句

作用：检索数据中符合条件的值

搜索的条件有一个或者多个表达式组成！ 结果是 布尔值

> 逻辑运算符

```sql
--  where  逻辑运算符
-- 查询考试成绩在  90-100 之间
select `studentno`,`studentresult` from `result`  -- 查询全部成绩
where `studentresult`>=90 and `studentresult`<=100
-- and &&
SELECT `studentno`,`studentresult` FROM `result`  
WHERE `studentresult`>=90 and `studentresult`<=100
--  between (区间)
SELECT `studentno`,`studentresult` FROM `result`  
WHERE `studentresult` between 90 AND 100
-- 除了学号为 1000 之外的学生成绩
SELECT `studentno`,`studentresult` FROM `result`  
where `studentno` != 1000 
-- 除了学号为 1000 之外的学生成绩   not
SELECT `studentno`,`studentresult` FROM `result`  
where not `studentno` = 1000 and 1001
```

> ==模糊查询：比较运算符==

| 运算符      | 语法               | 描述                                        |
| ----------- | ------------------ | ------------------------------------------- |
| is null     | a is null          | 如果操作符为null，结果为真                  |
| is not null | a is not null      | 如果操作符不为null，结果为真                |
| between     | a between b and c  | 若a在b和c之间，结果为真                     |
| **like**    | a like b           | SQL匹配，如果a匹配b，结果为真               |
| **in**      | a in (a1,a2,a3...) | 假设a在a1,或者a2...其中某一个值中，结果为真 |

```sql
-- ======== 模糊查询 ========
-- ============= like =============
-- 查询姓 郭 的同学
-- like %(代表0到任意个字符)  _(只指代一个字符)
SELECT `studentno`,`studentname` FROM student 
WHERE studentname LIKE '郭%';
-- 查询姓 郭 的同学 并且 名字只有两个字的
SELECT `studentno`,`studentname` FROM student 
WHERE studentname LIKE '郭_';
-- 查询姓 李 的同学 并且 名字只有三个字的
SELECT `studentno`,`studentname` FROM student 
WHERE studentname LIKE '李__';
-- 查询名字中有 志 的同学 
SELECT `studentno`,`studentname` FROM student 
WHERE studentname LIKE '%志%';

-- ============= in(具体的一个或多个值) =============
-- 查询 1001,1002，1003号学生
SELECT `studentno`,`studentname` FROM student 
WHERE `studentno` IN (1001,1002,1003)
-- 查询在 北京西城、广东潮州 的学生
SELECT `studentno`,`studentname` FROM student 
WHERE `address` IN ('北京西城','广东潮州')

-- ========= null 、 not null ==============
-- 查询地址为空的同学
SELECT `studentno`,`studentname` FROM student 
WHERE `address` ='' OR `address` IS NULL
-- 查询有出生日期的同学  不为空
SELECT `studentno`,`studentname` FROM student 
WHERE `borndate` IS NOT NULL
-- 查询没有出生日期的同学  为空
SELECT `studentno`,`studentname` FROM student 
WHERE `borndate` IS NULL
```

### 4.4.联表查询

#### 1.inner/left/right join  

>  join

-- join (连接的表) on (判断的条件) 连接查询
-- where 等值查询

**往哪个表差就是以这个表为基准：**

* a left join b on (以a表为基准)  

* a rightjoin b on (以b表为基准)  

多表连接思路：

* 要查哪些数据，在哪些表中
* 从这个表中找交叉条件（连接点）
* 先从查询两个表开始

| 操作       | 描述                                                       |
| ---------- | ---------------------------------------------------------- |
| inner join | 如果表中至少有一个匹配，就返回值                           |
| left join  | 会从 左表 中返回所有的值，即使 左表的值 在 右表 中没有匹配 |
| right join | 会从 右表 中返回所有的值，即使 左表 中没有匹配             |

```sql
-- ============= 联表查询 ======================

-- 查询参加考试的同学 （学号，姓名[在另外一个表中]，科目编号，成绩）
/*  思路
     1.分析需求，分析查询的字段来自那些表 （连接查询）
     2.确定使用哪种连接方式查询？ 总共7中
	   确定交叉点：两表之间哪些数据是相同的
	   判断的条件： 学生表 `studentno` = 成绩表 `studentno`
*/
-- inner
select s.`studentno`,`studentname`,`subjectno`,`studentresult`
from student as s
inner join result as r
where s.`studentno` = r.`studentno`

-- join (连接的表) on (判断的条件) 连接查询
-- where 等值查询

-- left join ... on 
SELECT s.`studentno`,`studentname`,`subjectno`,`studentresult`
FROM student AS s
left JOIN result AS r
on s.`studentno` = r.`studentno`

-- right join ... on 
SELECT s.`studentno`,`studentname`,`subjectno`,`studentresult`
FROM student AS s
RIGHT JOIN result AS r
ON s.`studentno` = r.`studentno`
```

> 案例一       了解联表查询

* left join

```sql
-- left join ... on 
SELECT s.`studentno`,`studentname`,`subjectno`,`studentresult`
FROM student AS s
left JOIN result AS r
on s.`studentno` = r.`studentno`
```

![image-20200607031509743](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200607031509743.png)

左表中的 studentname 为 gokudu 的学生在 右表 中并没有值（没有该学生对应的的 studentno），但仍然能查询出来。

应验了 ==left join 会从 左表 中返回所有的值，即使 左表的值 在 右表 中没有匹配==

* right join

```sql
-- right join ... on 
SELECT s.`studentno`,`studentname`,`subjectno`,`studentresult`
FROM student AS s
RIGHT JOIN result AS r
ON s.`studentno` = r.`studentno`
```

![image-20200607032313667](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200607032313667.png)

查询不出来 gokudu ，因为右表中查询出来的  studentno 中，并没有和 gokudu 的 studentno 匹配的。

而查询出来的没有为null的，是因为右表中其他所有的值都能够在左表中找到匹配的。

> 案例二    利用左表查询，找出缺考的同学

```sql
-- 查询缺考的同学
SELECT s.`studentno`,`studentname`,`subjectno`,`studentresult`
FROM student AS s
LEFT JOIN result AS r
ON s.`studentno` = r.`studentno`
where `studentresult` is null
```

> 案例三    思考题（查询参加考试的同学信息：学号，学生姓名，科目名，分数）

```sql
-- 思考题（查询参加考试的同学信息：学号，学生姓名，科目名，分数）
/* 思路
     1.分析需求，分析查询的字段来自哪些表，student，subject，result
     2.确定使用哪种连接方式查询？ 总共7中
     确定交叉点：两表之间哪些数据是相同的
	   判断的条件： 学生表 `studentno` = 成绩表 `studentno`
			成绩表 `subjectno` = 课程表 `subjectno`
	
*/
select s.`studentno`,`studentname`,su.`subjectname`,`studentresult`
from `result` r
left JOIN `student` s
ON s.`studentno` = r.`studentno`
inner join `subject` su
where r.`subjectno` = su.`subjectno`
```

可以先查询其中的两个表，然后再来与第三个表找连接点

1. 先把 result表 和 student表 连接起来， result表 左连接 student表 ，连接点是 studentno ，那么没有参加考试的 studentno  对应的学生，因为在result表中不会记录，所以在 student表 中匹配过来的都是参加考试的学生

```sql
select s.`studentno`,`studentname`,`subjectno`,`studentresult`
from `result` r
left JOIN `student` s
ON s.`studentno` = r.`studentno`
```

2. 通过 subjectno 将上面查询出来的结果 和 subject 表 连接起来

```sql
select s.`studentno`,`studentname`,su.`subjectname`,`studentresult`
from `result` r
left JOIN `student` s
ON s.`studentno` = r.`studentno`
inner join `subject` su
where r.`subjectno` = su.`subjectno`
```

#### 2.自连接【了解】

自己的表和自己的表连接，核心：**一张表拆成两张表**

> 案例

* 建表

```sql
--  ======= 自连接 ===========
CREATE TABLE category (
  categoryid INT(10) UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '主题ID',
  pid INT(10) NOT NULL COMMENT '父ID',
  categoryName VARCHAR(50) NOT NULL COMMENT '主题名字',
  PRIMARY KEY(categoryid)
) ENGINE=INNODB AUTO_INCREMENT=9 DEFAULT CHARSET = utf8;

INSERT INTO category(categoryid,pid,categoryName)
VALUES
('2','1','信息技术'),
('3','1','软件开发'),
('4','3','数据库'),
('5','1','美术设计'),
('6','3','web开发'),
('7','5','PS技术'),
('8','2','办公信息');
```

* 分析

父类

| categoryid | categoryName |
| :--------: | :----------: |
|     2      |   信息技术   |
|     3      |   软件开发   |
|     5      |   美术设计   |

子类

| pid  | categoryid | categoryName |
| :--: | :--------: | :----------: |
|  3   |     4      |    数据库    |
|  3   |     6      |   web开发    |
|  5   |     7      |    PS技术    |
|  2   |     8      |   办公信息   |

操作：查询父类对应的子类关系

|   父类   |   子类   |
| :------: | :------: |
| 信息技术 | 办公信息 |
| 软件开发 |  数据库  |
| 软件开发 | web开发  |
| 美术设计 |  PS技术  |

* 测试

```sql
-- 查询父子信息
SELECT a.`categoryName` AS '父栏目',b.`categoryName` AS '子栏目'
FROM `category` AS a,`category` AS b
WHERE a.`categoryid`=b.`pid`
```

> 案例二    查询参加 数据库结构 考试的同学信息

```sql
-- 思考题（查询参加 数据库结构 考试的同学信息：学号，学生姓名，科目名，分数）
/* 思路
     1.分析需求，分析查询的字段来自哪些表，student，subject，result
     2.确定使用哪种连接方式查询？ 总共7中
     确定交叉点：两表之间哪些数据是相同的
	   判断的条件： 学生表 `studentno` = 成绩表 `studentno`
			成绩表 `subjectno` = 课程表 `subjectno`
	
*/
SELECT s.`studentno`,s.`studentname`,sub.`subjectname`,r.`studentresult`
FROM `student` s
RIGHT JOIN `result` r
ON s.`studentno` = r.`studentno`
INNER JOIN `subject` sub
ON r.`subjectno` = sub.`subjectno`
WHERE sub.`subjectname` LIKE '%数据库结构%'
```

### 4.5.分页和排序

#### 1.排序

order by 某字段 desc/asc

```sql
-- 思考题（查询参加 数据库结构 考试的同学信息：学号，学生姓名，科目名，分数）
--  根据成绩排序
SELECT s.`studentno`,s.`studentname`,sub.`subjectname`,r.`studentresult`
FROM `student` s
RIGHT JOIN `result` r
ON s.`studentno` = r.`studentno`
INNER JOIN `subject` sub
ON r.`subjectno` = sub.`subjectno`
WHERE sub.`subjectname` LIKE '%数据库结构%'
ORDER BY `studentresult` DESC
```

#### 2.分页

语法：limit 查询起始下标，页面大小       limit (n-1) * pageSize，pageSize

```sql
-- 语法  limit 当前页面起始值，页面大小
-- 每页显示 5 条数据
-- limit 0,5   表示 1~5
-- limit 1,5   表示 2~6

SELECT s.`studentno`,s.`studentname`,sub.`subjectname`,r.`studentresult`
FROM `student` s
RIGHT JOIN `result` r
ON s.`studentno` = r.`studentno`
INNER JOIN `subject` sub
ON r.`subjectno` = sub.`subjectno`
WHERE sub.`subjectname` LIKE '%数据库结构%'
ORDER BY `studentresult` DESC
LIMIT 0,5

-- 第一页 limit 0,5    (1-1) * 5
-- 第二页 limit 5,5    (2-1) * 5
-- 第三页 limit 10,5   (3-1) * 5
    ...                  ...
-- 第n页 limit 15,5    (n-1) * pageSize
-- 【 pageSize 页面大小 】
-- 【 (n-1) * pageSize 当前页起始值 】
-- 【 n  当前页 】
-- 【 总数据/页面大小+1 总页数 】
```

查询 C语言-3 课程成绩排名前十  并且分数要大于80 的学生信息（学号，姓名，课程名称，分数）

```sql
-- 查询 C语言-3 课程成绩排名前十  并且分数要>=80 的学生信息（学号，姓名，课程名称，分数）
SELECT s.`studentno`,s.`studentname`,sub.`subjectname`,r.`studentresult`
FROM `student` s
inner JOIN `result` r
ON s.`studentno` = r.`studentno`
INNER JOIN `subject` sub
ON r.`subjectno` = sub.`subjectno`
WHERE sub.`subjectname` LIKE 'C语言-3' 
and r.`studentresult` >= 80
ORDER BY `studentresult` DESC
LIMIT 0,10
```

### 4.6.子查询

where（这个值是计算出来的）

本质：==在where语句中在嵌套一个子查询语句==

```sql
-- ============== where 子查询 =============
-- 查询 数据库结构-1 的所有开始接触 学号，科目号，成绩   降序排序
-- 方式一 使用连接查询
SELECT s.`studentno`,sub.`subjectno`,`studentresult`
FROM `student` s
INNER JOIN `result` r
ON s.`studentno` = r.`studentno`
INNER JOIN `subject` sub
ON r.`subjectno` = sub.`subjectno`
WHERE sub.`subjectname` LIKE '数据库结构-1' 
ORDER BY `studentresult` DESC

-- 方式二 使用子查询 (有里及外)
SELECT `studentno`,`subjectno`,`studentresult`
FROM `result`
WHERE `subjectno` = (
	SELECT `subjectno` FROM SUBJECT
	WHERE `subjectname` LIKE '数据库结构-1' 
)
ORDER BY `studentresult` DESC
```

子查询部分

```sql
WHERE `subjectno` = (
	SELECT `subjectno` FROM SUBJECT
	WHERE `subjectname` LIKE '数据库结构-1' 
)
```

> 案例     高等数学-1   分数不少于80分的学号和姓名

使用子查询

```sql
-- 使用子查询
SELECT DISTINCT s.`studentno`,`studentname`
FROM `student` s
INNER JOIN `result` r
ON s.`studentno`=r.`studentno`
WHERE r.`studentresult`>=80
AND `subjectno` =(
	SELECT `subjectno` 
	FROM`subject`
	WHERE `subjectname` LIKE '高等数学-1'
)
```

对比联表查询

```sql
-- 分数不少于80分的学号和姓名
SELECT DISTINCT s.`studentno`,`studentname`
FROM `student` s
INNER JOIN `result` r
ON s.`studentno`=r.`studentno`
WHERE r.`studentresult`>=80
-- 在这个基础上 在限制一个科目  高等数学-1
SELECT DISTINCT s.`studentno`,`studentname`
FROM `student` s
INNER JOIN `result` r
ON s.`studentno`=r.`studentno`
INNER JOIN `subject` sub
ON sub.`subjectno` = r.`subjectno`
WHERE r.`studentresult`>=80
AND sub.`subjectname` LIKE '高等数学-1'

-- 使用子查询
SELECT DISTINCT s.`studentno`,`studentname`
FROM `student` s
INNER JOIN `result` r
ON s.`studentno`=r.`studentno`
WHERE r.`studentresult`>=80
AND `subjectno` =(
	SELECT `subjectno` 
	FROM`subject`
	WHERE `subjectname` LIKE '高等数学-1'
)
```

> 案例     查询课程为 高等数学-3 的分数不小于75 的同学的学号和姓名

分别使用 联表查询、子查询 实现

```sql
-- 查询课程为 高等数学-3 的分数不小于75 的同学的学号和姓名
SELECT DISTINCT s.`studentno`,`studentname`
FROM `student` s
INNER JOIN `result` r
ON s.`studentno` = r.`studentno`
INNER JOIN `subject` sub
ON r.`subjectno` = sub.`subjectno`
WHERE `studentresult`>=75 
AND `subjectname` LIKE '高等数学-3'

-- 子查询
SELECT DISTINCT s.`studentno`,`studentname`
FROM `student` s
INNER JOIN `result` r
ON s.`studentno` = r.`studentno`
WHERE `studentresult`>=75 
AND `subjectno` = (
	SELECT `subjectno`
	FROM `subject`
	WHERE `subjectname` LIKE '高等数学-3'
)
```

对两个条件都使用子查询

```sql
-- 两个条件都使用子查询
SELECT DISTINCT `studentno`,`studentname`
FROM `student` 
WHERE `studentno` IN (
	SELECT `studentno`
	FROM `result`
	WHERE `studentresult`>=75 
	AND `subjectno` = (
		SELECT `subjectno`
		FROM `subject`
		WHERE `subjectname` LIKE '高等数学-3'
	)
)
```

> 练习    使用子查询 ：   查询 C语言-1 前五名同学的成绩   学号 姓名 分数

```sql
-- 练习 查询 C语言-1 前五名同学的成绩   学号 姓名 分数
SELECT	s.`studentno`,`studentname`,`studentresult`
FROM `student` s
INNER JOIN `result` r
ON `studentresult` IN (
	SELECT `studentresult`
	FROM `result`
	WHERE `subjectno` = (
		SELECT `subjectno`
		FROM `subject`
		WHERE `subjectname` LIKE 'C语言-3'
	)
) 
ORDER BY r.`studentresult` DESC
LIMIT 0,5

```

### 4.7.分组和过滤

语法 ： GROUP BY 字段

where 在分组之前使用

having 在分组之后使用

```sql
-- 查询不同课程的平均分，最高分，最低分
SELECT 	`subjectname`,
	AVG(`studentresult`) AS '平均数',
	MAX(`studentresult`) AS '最高分',
	MIN(`studentresult`) AS '最低分'
FROM `result` r
INNER JOIN `subject` sub
ON r.`subjectno`= sub.`subjectno`
GROUP BY r.`subjectno`   -- 通过哪个字段来分组
-- 再此基础上 要求平均分大于80
HAVING 平均数 > 80	-- 分组之后使用 having 来过滤条件
```

### 4.select小结

![image-20200607204429763](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200607204429763.png)



## 5.MySQL函数

官网： https://dev.mysql.com/doc/refman/5.7/en/

### 5.1.常用函数

```sql
-- ========= 常用函数 ========= 

-- 数学运算
SELECT ABS(-8)   -- 绝对值
SELECT CEILING(9.5)  -- 向上取整
SELECT FLOOR(9.5)  -- 向下取整
SELECT RAND()   -- 返回一个0到1的随机数
SELECT SIGN(10)   -- 返回一个数值的符号   输入0 返回0   负数返回 -1  整数返回 1 

-- 字符串函数
SELECT CHAR_LENGTH('伯格曼的假面')  -- 字符串长度
SELECT CONCAT('张三','打','李四')   -- 拼接字符串
SELECT INSERT('张三想打李四',2,2,'三丰不想')   -- 替换字符串  从某个位置开始，替换 n 个字符串
					       -- 2,2 代表从 第二个字符开始，替换两个字符
								-- "三想"  ==>  "三丰不想"

SELECT UPPER('Gokudu')	-- 大写
SELECT LOWER('Gokudu')	-- 小写
SELECT INSTR('gokudu','u')  -- 返回第一次出现的 子串 的索引
SELECT SUBSTR('伯格曼和塔可夫斯基',5,5)   -- 返回指定字符串  第五位开始，截取五个字符
SELECT REPLACE('伯格曼和塔可夫斯基走到一起，伯格曼说','伯格曼','沟口健二')
SELECT REVERSE('abcdefg')  -- 反转

-- 查询姓周的同学  改为  邹
SELECT REPLACE(studentname,'周','邹')
FROM student 
WHERE studentname LIKE '%周%'

-- 时间和日期函数 (重点)
SELECT CURRENT_DATE()	-- 获取当前时间
SELECT CURDATE()	-- 获取当前时间
SELECT NOW()	  -- 获取当前时间(毫秒)
SELECT LOCALTIME() -- 本地时间
SELECT SYSDATE()    -- 系统时间

SELECT YEAR(CURRENT_DATE())
SELECT MONTH(CURRENT_DATE())
SELECT DAY(CURRENT_DATE())
SELECT HOUR(NOW())
SELECT MINUTE(NOW())
SELECT SECOND(NOW())

-- 系统
SELECT SYSTEM_USER()
SELECT USER()
SELECT VERSION()
```



### 5.2.聚合函数(常用)

| 函数名称  | 描述   |
| --------- | ------ |
| count（） | 计数   |
| sum()     | 求和   |
| avg()     | 平均值 |
| max()     | 最大值 |
| min()     | 最小值 |
| ...       | ...    |

```sql
-- ============ 聚合函数 =============
-- 查询一个表中有多少条记录
select count(studentname) from student;  -- count(指定列/字段)  会忽略所有null  
select count(*) from student;  -- count(*) -- 不会忽略null	
SELECT COUNT(1) FROM student;  -- count(1) -- 不会忽略null

select sum(`studentresult`) as '总和' from `result`
select avg(`studentresult`) as '平均数' from `result`
select max(`studentresult`) as '最高分' from `result`
select min(`studentresult`) as '最低分' from `result`
```

```txt
	select(*)与select(1) 在InnoDB中性能没有任何区别，处理方式相同。
	官方文档描述如下：InnoDB handles SELECT COUNT(*) and SELECT COUNT(1) operations in the same way. There is no performance difference.
```

博客：MySQL count(*),count(1),count(field)区别、性能差异及优化建议

https://baijiahao.baidu.com/s?id=1660139166311547332&wfr=spider&for=pc

### 5.3.数据库级别的MD5加密（扩展）

MD5相比其前身，主要增强了算法复杂度和==**不可逆性**==

MD5不可逆，具体的值的md5不变

所以一些常用的数据转为md5不安全，有人会把这些常见做成一个数据字典（md5加密后的值：加密前的值）

```sql
-- ============ 测试MD5加密 ===============
CREATE TABLE `testmd5`(
   `id` INT(4) NOT NULL,
   `name` VARCHAR(20) NOT NULL,
   `pwd` VARCHAR(50) NOT NULL,
   PRIMARY KEY(`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8

-- 明文密码
INSERT INTO testmd5 VALUES(1,'zhangsan','123456'),(2,'lisi','123456'),(3,'wangwu','123456')

-- md5加密,插入的时候加密 
INSERT INTO testmd5 VALUES(4,'zhangsan',MD5('123456'))
-- 校验，将用户传递过来的密码，进行MD5加密，然后和数据库中插入时加密过的密码进行对比
SELECT * FROM testmd5 WHERE NAME='zhangsan' AND pwd=MD5('123456')
```



## 6.事务

### 6.1.什么是事务

==要么都成功，要么都失败==

1.SQL执行   A给B转账       A 1000  --> 200           B  300

2.SQL执行  B收到A的钱     A  800                          B  500

> 事务原则：ACID原则  ：　原子性，一致性，隔离性，持久性　　

参考博客：https://blog.csdn.net/dengjili/article/details/82468576

**原子性（Atomicity）**

要么都成功，要么都失败

**一致性（Consistency）**
事务前后数据的完整性必须保持一致。（别人转账中，双方的账户总金额不变）   

**隔离性（Isolation）**
事务的隔离性是多个用户并发访问数据库时，数据库为每一个用户开启的事务，不能被其他事务的操作数据所干扰，多个并发事务之间要相互隔离。

**持久性（Durability）**
持久性是指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来即使数据库发生故障也不应该对其有任何影响

>隔离所导致的一些问题

**脏读：**

指一个事务读取了另一个事务未提交的数据。

 ==例子：==

A  500  B 200   C 200

A --> B  200       

C--->B  100     



 C在 A转给B 200 未提交时 ，自己转给B 100 ，那么他读到的是 B 300  ，C 100

然后 A转给B 事务提交之后 ， A 300    B 400         （那么 总值会有 100 丢失）   



**不可重复读：**

在一个事务内读取表中的某一行数据，多次读取结果不同。（不一定是错误的，只是该值已经被修改并且提交事务了）



**虚读、幻读：**

指在一个事务内读取到别人的事务插入的数据，导致前后不一致。（有别的事务插入新数据）

> 执行事务

执行流程：

<img src="https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200607221025093.png" alt="image-20200607221025093" style="zoom:80%;" />

```sql
-- ============= 事务 ==============
-- mysql默认开启事务自动提交
SET autocommit = 0  -- 关闭自动提交
SET autocommit = 1  -- 开启 (默认)

-- 手动开启事务
SET autocommit = 0  -- 关闭自动提交
-- 事务开启
START TRANSACTION -- 标记一个事务的开始，从这个之后的sql都在同一个事务内

INSERT ...
INSERT ...

-- 提交: 持久化（成功）
COMMIT
-- 回滚：回到原来的样子（失败）
ROLLBACK

-- 事务结束
 SET autocommit = 1  -- 开启自动提交 (回到默认)

-- 了解
SAVEPOINT -- 设置事务的保存点
ROLLBACK TO SAVEPOINT --  回滚到保存点
RELEASE SAVEPOINT  -- 撤销保存点
```

> 模拟转账	

```sql
-- 模拟转账  事务
SET autocommit = 0 -- 关闭自动提交事务 
START TRANSACTION  -- 开启一个事务 (一组事务)

UPDATE `account` SET `money` = `money`-500 WHERE `name`='A'  -- A减500元
UPDATE `account` SET `money` = `money`+500 WHERE `name`='B'  -- B加500元

COMMIT       -- 提交事务，就被持久化，回滚也没用
ROLLBACK     -- 回滚事务

SET autocommit = 1    -- 恢复默认自动提交事务
```

## 7.索引

> MySQL官方对索引的定义为：索引（Index）是帮助**MySQL高效获取数据**的**数据结构**。
>
> 提取句子主干，就可以得到索引的本质：索引是数据结构。

### 7.1.索引的分类

* 主键索引   (PRIMARY KEY)
  * 唯一的标识。**主键** 不可用重复
* 唯一索引   (UNIQUE KEY)
  * 、避免重复的列出现。**唯一索引** 可以重复，多个列都可以标识为 **唯一索引**
* 常规索引   (KEY/INDEX)
  * 默认的。KEY/INDEX 关键字设置
* 全文索引   (FULLTEXT)
  * 在特定的数据库引擎下才有（MyISAM）
  * 快速定位数据

> 基础语法

```sql
 -- 索引的使用
-- 1.在创建表的时候，给字段增加索引
-- 2.在已经创建的表中，增加索引

-- 显示所有索引信息
SHOW INDEX FROM student

-- 增加一个全文索引（其他索引同理）     索引名（列名）
ALTER TABLE student ADD FULLTEXT INDEX `studentname`(`studentname`);

-- 创建一个索引  id_表明_字段名
-- CREATE INDEX 索引名 ON 表(字段);
CREATE INDEX id_app_user_name ON app_user(`name`);

EXPLAIN SELECT * FROM student;  --  非全文索引
EXPLAIN SELECT * FROM student WHERE MATCH(studentname) AGAINST('李');
```

![image-20200607225056611](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200607225056611.png)

### 7.2.测试索引

* 新建数据库，添加100万条数据进行测试

```sql
CREATE TABLE `app_user` (
	`id` BIGINT(20) UNSIGNED NOT NULL AUTO_INCREMENT,
	`name` VARCHAR(50) DEFAULT '' COMMENT '用户昵称',
	`email` VARCHAR(50) NOT NULL COMMENT '用户邮箱',
	`phone` VARCHAR(20) DEFAULT '' COMMENT '手机号',
	`gender` TINYINT(4) UNSIGNED DEFAULT '0' COMMENT '性别（0：男，1：女）',
	`password` VARCHAR(100) NOT NULL DEFAULT '' COMMENT '密码',
	`age` TINYINT(4) DEFAULT NULL COMMENT '年龄',
	`create_time` DATETIME DEFAULT CURRENT_TIMESTAMP,
	`update_time` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
	PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8 COMMENT='app用户表'


-- 插入100万条数据
DELIMITER $$  -- 写函数之前必写，标志
CREATE FUNCTION mockdatatest()
RETURNS INT
BEGIN
   DECLARE num INT DEFAULT 1000000;
   DECLARE i INT DEFAULT 0;
   WHILE i<num DO	
	INSERT INTO `app_user`(`name`,`email`,`phone`,`gender`,`password`,`age`)VALUES (CONCAT('用户',i),'15613114@qq.com','13076451252',0,UUID(),FLOOR(RAND()*110));	
	SET i=i+1;
   END WHILE; 
   RETURN i;
END;

SELECT mockdatatest();
```

* 执行sql

```sql
select * from `app_user` where `name` like '用户9999'
```

![image-20200608003159920](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200608003159920.png)

* expain 

```sql
EXPLAIN SELECT * FROM `app_user` WHERE `name` LIKE '用户9999'; 
```

![image-20200608003422666](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200608003422666.png)

* 创建一个索引

```sql
-- 创建一个索引  id_表明_字段名
-- CREATE INDEX 索引名 ON 表(字段);
CREATE INDEX id_app_user_name ON app_user(`name`);
```

* 测试

```sql
SELECT * FROM `app_user` WHERE `name` LIKE '用户9999';
```

![image-20200608004121096](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200608004121096.png)

* expain 

```sql
EXPLAIN SELECT * FROM `app_user` WHERE `name` LIKE '用户9999'; 
```

![image-20200608004152612](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200608004152612.png)

==索引在数据量较小的时候，感觉不到差别==

==但是在数据量很大的时候，区别十分明显==

### 7.3.为什么索引能提高查询速度

博客： [https://github.com/GokuDU/JavaGuide/blob/master/docs/database/MySQL%20Index.md](https://github.com/GokuDU/JavaGuide/blob/master/docs/database/MySQL Index.md)

![MySQL的基本存储结构是页](https://camo.githubusercontent.com/57a746bf254e100c3fd0d2691d172df5c29592eb/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d322f32383535393432312e6a7067)

![img](https://camo.githubusercontent.com/a0e0c5b1377f6ab52365479c52313f4238550d31/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d322f38323035333133342e6a7067)

- **各个数据页可以组成一个双向链表**
- 每个数据页中的记录又可以组成一个单向链表
  - 每个数据页都会为存储在它里边儿的记录生成一个页目录，在通过主键查找某条记录的时候可以在页目录中使用二分法快速定位到对应的槽，然后再遍历该槽对应分组中的记录即可快速找到指定的记录
  - 以其他列(非主键)作为搜索条件：只能从最小记录开始依次遍历单链表中的每条记录。

所以说，如果我们写select * from user where indexname = 'xxx'这样没有进行任何优化的sql语句，默认会这样做：

1. **定位到记录所在的页：需要遍历双向链表，找到所在的页**
2. **从所在的页内中查找相应的记录：由于不是根据主键查询，只能遍历所在页的单链表了**

很明显，在数据量很大的情况下这样查找会很慢！这样的时间复杂度为O（n）。

### 使用索引之后

索引做了些什么可以让我们查询加快速度呢？其实就是**将无序的数据变成有序(相对)**：

![img](https://camo.githubusercontent.com/83e4b2a638e8352a21feafeafe97cbad0fc2a335/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d322f353337333038322e6a7067)

要找到id为8的记录简要步骤：

![img](https://camo.githubusercontent.com/c63688b141c3562bbf4fb4b719ab027c6dea91e9/687474703a2f2f6d792d626c6f672d746f2d7573652e6f73732d636e2d6265696a696e672e616c6979756e63732e636f6d2f31382d31302d322f38393333383034372e6a7067)

很明显的是：没有用索引我们是需要遍历双向链表来定位对应的页，现在通过 **“目录”** 就可以很快地定位到对应的页上了！（二分查找，时间复杂度近似为O(logn)）

其实底层结构就是B+树，B+树作为树的一种实现，能够让我们很快地查找出对应的记录。

### 7.4.索引原则

* 索引不是越多越好
* 不对进程变动数据加索引
* 小数据的表不需要加索引
* 索引一般用来加在查询的字段

> 索引的数据结构

Btree  ： InnoDB 的默认类型

B+Tree

博客： https://blog.codinglabs.org/articles/theory-of-mysql-index.html

​			 [https://github.com/GokuDU/JavaGuide/blob/master/docs/database/MySQL%20Index.md](https://github.com/GokuDU/JavaGuide/blob/master/docs/database/MySQL Index.md)

## 8.数据库备份

### 8.1.用户管理

> SQLyog 可视化管理

![image-20200608020931902](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200608020931902.png)

> 命令操作

用户表:  mysql.user

本质：对这种表进行增删改查

```sql
-- 创建用户 create user 用户名 identified by '密码'
CREATE USER gokudu IDENTIFIED BY '123456'

-- 修改当前用户密码
SET PASSWORD=PASSWORD('123456')

-- 修改指定用户密码
SET PASSWORD FOR gokudu = PASSWORD('123456')

-- 重命名 rename user 旧用户名 to 新的用户名
RENAME USER gokudu TO gokufriday

-- 用户授权  授予全部权限   grant 全部的权限 on 全部库.全部表 to gokufriday
-- all privileges 除了给其他人授权不能没有权限  其他都能干 
GRANT ALL PRIVILEGES ON *.* TO gokufriday

-- 查询权限
SHOW GRANTS FOR gokufriday  -- 查看指定用户的权限
SHOW GRANTS FOR root@localhost	
-- ROOT用户： GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' WITH GRANT OPTION

-- 撤销权限  revoke 全部的权限 on 全部库.全部表 from gokufriday
REVOKE ALL PRIVILEGES ON *.* FROM gokufriday

-- 删除用户
DROP USER gokufriday
```

### 8.2.MySQL 备份

需要备份的原因

* 保证数据不丢失
* 数据转移

MySQL备份的方式

* 使用可视化工具导出
* 使用命令行导出   mysqldump

导出：

```bash
# mysqldump -h主机 -u用户 -p密码 数据库 表名 >物理磁盘位置/文件名
mysqldump -hlocalhost -uroot -p123456 school student >d:/student.sql
mysqldump: [Warning] Using a password on the command line interface can be insecure.

# 导出多张表
# mysqldump -h主机 -u用户 -p密码 数据库 表1 表2 表三 ... >物理磁盘位置/文件名
>mysqldump -hlocalhost -uroot -p123456 school student result grade >d:/b.sql

# 导出某个数据库
mysqldump -hlocalhost -uroot -p123456 school >d:/c.sql
```

导入：

```bash
# 在登录的情况下，使用source
# 物理磁盘位置/文件名
source d:/c.sql
```

## 9.规范数据库设计

### 9.1为什么需要设计

当数据库比较复杂的时候，更需要规范设计

**糟糕的数据库设计：**

* 数据冗余，浪费空间
* 数据库插入和删除比较麻烦’异常【使用物理外键】
* 程序的性能差

**良好的数据库设计：**

* 节省内存空间
* 保证数据库的完整性
* 方便开发

**软件开发中，关于数据库的设计**

* 分析需求：分析业务和需要处理的数据库的需求
* 概要设计：设计关系图 E-R图



**设计数据库的步骤：（个人博客）**





### 9.2.三大范式

为什么要数据规范化

* 信息重复
* 更新异常
* 插入异常
  * 无法正常显示信息
* 删除异常
  * 丢失有效信息

> 三大范式

**第一范式（1NF）**

==原子性==：保证数据表的每一列都是不可分割的==原子性==数据项



举例说明：

==在一个字段中，不能存放多个属性的信息==

比如，在一个家庭信息列中，既有家庭人数信息，又有家庭住址信息



**第二范式（2NF）**

前提：满足第一范式

每张表值描述一件事情



**第三范式（3NF）**

前提：满足第一范式和第二范式

确保数据表中的每一列数据都==和主键直接相关==，而不能间接相关



举例说明：

![img](https://images2018.cnblogs.com/blog/1218459/201809/1218459-20180909211311408-1364899740.png)

上表中，所有属性都完全依赖于学号，所以满足第二范式，但是“班主任性别”和“班主任年龄”直接依赖的是“班主任姓名”，

而不是主键“学号”，所以需做如下调整：

![img](https://images2018.cnblogs.com/blog/1218459/201809/1218459-20180909211539242-1391100354.png) ![img](https://images2018.cnblogs.com/blog/1218459/201809/1218459-20180909211602202-1069383439.png)

这样以来，就满足了第三范式的要求。

ps:如果把上表中的班主任姓名改成班主任教工号可能更确切，更符合实际情况，不过只要能理解就行。



**规范性和性能的问题**

关联查询的表不能超过三张表

* 考虑商业化的需求和 目标（成本，用户体验），考虑数据库的性能
* 在规范性能问题的时候，设当考虑一下数据库的规范性
* 故意给某些表增加一些冗余的字段（从多表查询变为单表查询）
* 故意增加一些计算列（从大数据降为小数据量的查询：==索引==）

## 10.JDBC（重点）

### 10.1.数据库驱动

驱动：显卡、声卡、数据库都需要驱动

![image-20200608125023672](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200608125023672.png)

编写的程序通过 数据库 驱动，和数据库打交道

### 10.2.JDBC

SUN公司为了简化开发人员的操作（对数据库的统一），提供一个规范（Java操作数据库规范），也就是JDBC（Java Database Connectivity）

这些规范的实现由具体的厂商去做

而对于开发人员，只需要掌握JDBC接口的操作

![image-20200608130149447](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200608130149447.png)

需要的包：

java.sql

javax.sql

导入数据库驱动包： mysql-connector-java-5.1.47.jar

### 10.3.第一个JDBC程序

* 新建一个数据库

```sql
create database jdbcstudy character set utf8 collate utf8_general_ci;

use jdbcstudy;

create table users(
    `id` int primary key,
    `name` varchar(40),
    `password` varchar(40),
    `email` varchar(60),
    `birthday` date
);

insert into users(`id`,`name`,`password`,`email`,`birthday`)
values(1,'zhangsan','123456','15615165@qq.com',now()),
      (2,'lisi','123456','dsdadsd@144.com',now()),
      (3,'wangwu','123456','swdfffsa@q3.com',now())
```

* java

```java
// 第一个JDBC程序
public class JdbcFirstDemo {

    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 1.加载驱动
        Class.forName("com.mysql.jdbc.Driver");

        // 2.用户信息和url
        String url="jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true";
        String username="root";
        String password="123456";

        // 3.连接成功，返回一个数据库对象
        Connection connection = DriverManager.getConnection(url, username, password);

        // 4.执行SQL的对象
        Statement statement = connection.createStatement();

        // 5.执行SQL的对象 来 执行SQL
        String sql="select * from users";
        ResultSet resultSet = statement.executeQuery(sql);

        while(resultSet.next()){
            System.out.println("id:"+resultSet.getObject("id"));
            System.out.println("name:"+resultSet.getObject("name"));
            System.out.println("pwd:"+resultSet.getObject("password"));
            System.out.println("email:"+resultSet.getObject("email"));
            System.out.println("birth:"+resultSet.getObject("birthday"));
            System.out.println("=========++++++++==========++++++++++===========");
        }

        // 6.释放资源
        resultSet.close();
        statement.close();
        connection.close();
    }
}
```

步骤：

1.加载驱动

2.连接数据库  （输入用户信息）

```java
DriverManager.getConnection(url, username, password); 
```

3.获取执行sql的对象  Statement

4.通过 Statement 对象 来 执行sql ，获得返回的结果集

5.释放资源

> DriverManager

```java
// 1.加载驱动
// DriverManager.registerDriver(new com.mysql.jdbc.Driver());   
// 没必要注册，源码在静态代码块已经有这一句了，这样写就注册两次了
Class.forName("com.mysql.jdbc.Driver");
 Connection connection = DriverManager.getConnection(url, username, password);
//   事务提交  事务回滚   数据库设置自动提交

connection.commit();
connection.rollback();
connection.setAutoCommit(true);
```

> url

```java
 String url="jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true";

// mysql -- 3306
// 协议://主机名：端口号/数据库名?参数1&参数2&参数3
// oralce -- 1521
// jdbc:oralce:thin:@localhost:1521:sid
```

> Statement 执行sql的对象

```java
String sql="select * from users";	//	编写SQL，尽量先测试成功，在使用

statement.executeQuery(sql);    // 查询  返回resultSet
statement.execute(sql);     // 执行任何SQL
statement.executeUpdate(sql);  // 插入、更新、删除  返回一个受影响的行数
```

> resultSet

```java
// 如果不知道列的类型的情况下
resultSet.getObject("column_01");
// 如果知道列的类型，可以直接指定类型
resultSet.getInt("column_01");
resultSet.getString("column_01");
resultSet.getFloat("column_01");
resultSet.getDate("column_01");  
```

* 遍历，指针

```java
// 遍历，指针
resultSet.beforeFirst();      // 移动到最前面
resultSet.afterLast();        // 移动到最后面
resultSet.next();             // 移动到下一个数据
resultSet.previous();         // 移动到前一行
resultSet.absolute(row); // 移动到指定行
```

* 第一个JDBC程序中的对应代码

```java
// 5.执行SQL的对象 来 执行SQL
String sql="select * from users";
ResultSet resultSet = statement.executeQuery(sql);

while(resultSet.next()){
    System.out.println("id:"+resultSet.getObject("id"));
    System.out.println("name:"+resultSet.getObject("name"));
    System.out.println("pwd:"+resultSet.getObject("password"));
    System.out.println("email:"+resultSet.getObject("email"));
    System.out.println("birth:"+resultSet.getObject("birthday"));
    System.out.println("=========++++++++==========++++++++++===========");
}
```

> 释放资源

```java
// 释放资源
resultSet.close();
statement.close();
connection.close();
```



### 10.4.Statement对象

编写增删改的方法，调用 ==executeUpdate（）==

> insert

```java
st = conn.createStatement();
String sql="INSERT INTO users(`id`,`name`,`password`,`email`,`birthday`)\n" +
        "VALUES(5,'laoli','123456','lao5454@q2q.com','1977-06-13')";

int i = st.executeUpdate(sql);
if(i>0){
    System.err.println("[DEBUG]insert success");
}
```

> update

```java
st = conn.createStatement();
String sql="update users set `name`='gokudu',`password`='112211' where id=5";

int i = st.executeUpdate(sql);
if(i>0){
    System.err.println("[DEBUG]update success");
}
```

> delete

```java
st = conn.createStatement();
String sql="DELETE FROM users WHERE id=5";

int i = st.executeUpdate(sql);
if(i>0){
    System.err.println("[DEBUG]delete success");
}
```

> Select

```java
st = conn.createStatement();
String sql="select * from users where id=4";

ResultSet resultSet = st.executeQuery(sql);
while(resultSet.next()){
    System.out.println("id:"+resultSet.getObject("id"));
    System.out.println("name:"+resultSet.getObject("name"));
    System.out.println("pwd:"+resultSet.getObject("password"));
    System.out.println("email:"+resultSet.getObject("email"));
    System.out.println("birth:"+resultSet.getObject("birthday"));
    System.out.println("=========++++++++==========++++++++++===========");
}
```

#### 1.提取工具类

```java
public class JdbcUtils {

    private static String driver =null;
    private static String url =null;
    private static String username =null;
    private static String password =null;

    static{
        try {
            InputStream resourceAsStream = 	JdbcUtils.class.getClassLoader().getResourceAsStream("db.properties");
            Properties properties = new Properties();
            properties.load(resourceAsStream);

            driver = properties.getProperty("driver");
            url = properties.getProperty("url");
            username = properties.getProperty("username");
            password = properties.getProperty("password");

            // 驱动只加载一次
            Class.forName(driver);

        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    // 获取连接
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(url, username, password);
    }


    // 释放资源
    public static void releaseResources(Connection conn, Statement st, ResultSet res) throws SQLException {
        if(res != null){
            res.close();
        }
        if(st != null){
            st.close();
        }
        if(conn != null){
            conn.close();
        }
    }
    
}
```

#### 2.编写增删改的方法

调用 ==executeUpdate（）==

```java
package com.guo.jdbc02;

import com.guo.jdbc02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestInsert {

    public static void main(String[] args)  {
        Connection conn=null;
        Statement st=null;
        ResultSet res=null;
        try {
            conn = JdbcUtils.getConnection();
            st = conn.createStatement();
            String sql="INSERT INTO users(`id`,`name`,`password`,`email`,`birthday`)\n" +
                    "VALUES(5,'laoli','123456','lao5454@q2q.com','1977-06-13')";

            int i = st.executeUpdate(sql);
            if(i>0){
                System.err.println("[DEBUG]insert success");
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            try {
                JdbcUtils.releaseResources(conn, st, res);
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

update 和  delete 只需要 在上面这个方法的基础上，更改一下sql语句

> update

```java
st = conn.createStatement();
String sql="update users set `name`='gokudu',`password`='112211' where id=5";

int i = st.executeUpdate(sql);
if(i>0){
    System.err.println("[DEBUG]update success");
}
```

> delete

```java
st = conn.createStatement();
String sql="DELETE FROM users WHERE id=5";

int i = st.executeUpdate(sql);
if(i>0){
    System.err.println("[DEBUG]delete success");
}
```

#### 3.编写增删改的方法

调用 ==executeQuery（）==

```java
st = conn.createStatement();
String sql="select * from users where id=4";

ResultSet resultSet = st.executeQuery(sql);
while(resultSet.next()){
    System.out.println("id:"+resultSet.getObject("id"));
    System.out.println("name:"+resultSet.getObject("name"));
    System.out.println("pwd:"+resultSet.getObject("password"));
    System.out.println("email:"+resultSet.getObject("email"));
    System.out.println("birth:"+resultSet.getObject("birthday"));
    System.out.println("=========++++++++==========++++++++++===========");
}
```

### 10.5. SQL注入问题

SQL存在漏洞，会被攻击导致数据泄露。使用 PreparedStatement 可以防止注入，并且效率更高

```java
login(" 'or '1=1"," 'or '1=1");
```

通过SQL注入，查到全部用户登录数据

![image-20200608182918876](https://raw.githubusercontent.com/GokuDU/docsify-blog/master/images/image-20200608182918876.png)

```java
package com.guo.jdbc02;

import com.guo.jdbc02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class TestSQLInjection {

    public static void main(String[] args)  {
//        login("yyyy","112211");
        login(" 'or '1=1"," 'or '1=1");
    }

    public static void login(String username,String password){
        Connection conn=null;
        Statement st=null;
        ResultSet res=null;
        try {
            conn = JdbcUtils.getConnection();
            st = conn.createStatement();
            String sql="select * from users where `name`='" + username + "'and `password`='" + password + "'";

            ResultSet resultSet = st.executeQuery(sql);
            while(resultSet.next()){
                System.out.println(resultSet.getObject("name"));
                System.out.println(resultSet.getObject("password"));
                System.out.println("===========================");
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            try {
                JdbcUtils.releaseResources(conn, st, res);
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 10.6.PreparedStatement 

使用 PreparedStatement 可以防止注入，并且效率更高

> 增删改      insert、update、delete

使用预编译插入数据，update和delete同理，修改一下sql，给相应的占位符设置参数类型、赋值即可

```java
// 区别
// 使用 ?  占位符   代替参数
String sql="INSERT INTO users(`id`,`name`,`password`,`email`,`birthday`) VALUES(?,?,?,?,?)";
pst = conn.prepareStatement(sql);// 预编译SQL，然后不执行

// 手动给参数设置类型
pst.setInt(1, 6);
pst.setString(2, "laoli");
pst.setString(3, "laoli");
pst.setString(4, "lao5454@q2q.co");
// 注意   sql.Date    数据库  java.sql.Date
//       util.Date    Java   new Date().getTime() 获得时间戳
pst.setDate(5, new java.sql.Date(new Date().getTime()));

//执行
int i = pst.executeUpdate();
if(i>0){
    System.out.println("[DEBUG] prepareStatement INSERT SUCCESS");
}
```

完整代码

```java
package com.guo.Jdbc03;

import com.guo.Jdbc02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.Date;

public class PreparedUpdate {

    public static void main(String[] args)  {
        Connection conn=null;
        PreparedStatement pst=null;
        ResultSet res=null;
        try {
            conn = JdbcUtils.getConnection();

            // 区别
            // 使用 ?  占位符   代替参数
            String sql="UPDATE users SET `name`=?,`password`=? WHERE id=?";
            pst = conn.prepareStatement(sql);// 预编译SQL，然后不执行

            // 手动给参数设置类型，并且赋值
            pst.setString(1, "updatename");
            pst.setString(2, "100000");
            pst.setInt(3, 5);

            //执行
            int i = pst.executeUpdate();
            if(i>0){
                System.out.println("[DEBUG] prepareStatement Update SUCCESS");
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            try {
                JdbcUtils.releaseResources(conn, pst, res);
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

> 查询     select

```java
conn = JdbcUtils.getConnection();

String sql="SELECT * FROM users WHERE `name`=? AND `password`=?";

pst = conn.prepareStatement(sql);

pst.setString(1, "lisi");
pst.setString(2, "123456");

ResultSet resultSet = pst.executeQuery();

while(resultSet.next()){
    System.out.println(resultSet.getObject("id"));
    System.out.println(resultSet.getObject("name"));
    System.out.println(resultSet.getObject("password"));
    System.out.println(resultSet.getObject("email"));
    System.out.println(resultSet.getObject("birthday"));
}
```

完整代码：

```java
package com.guo.Jdbc03;

import com.guo.Jdbc02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class PreparedSelect {


    public static void main(String[] args)  {
        Connection conn=null;
        PreparedStatement pst=null;
        ResultSet res=null;

        try {
            conn = JdbcUtils.getConnection();

            String sql="SELECT * FROM users WHERE `name`=? AND `password`=?";

            pst = conn.prepareStatement(sql);

            pst.setString(1, "lisi");
            pst.setString(2, "123456");

            ResultSet resultSet = pst.executeQuery();

            while(resultSet.next()){
                System.out.println(resultSet.getObject("id"));
                System.out.println(resultSet.getObject("name"));
                System.out.println(resultSet.getObject("password"));
                System.out.println(resultSet.getObject("email"));
                System.out.println(resultSet.getObject("birthday"));
            }


        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            try {
                JdbcUtils.releaseResources(conn, pst, res);
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

> 防止SQL注入

```java
package com.guo.Jdbc03;

import com.guo.Jdbc02.utils.JdbcUtils;

import java.sql.*;

public class PreparedTestSQLInjection {

    public static void main(String[] args)  {
//        login("yyyy","112211");
        login(" 'or '1=1"," 'or '1=1");
    }

    public static void login(String username,String password){
        Connection conn=null;
        PreparedStatement pst=null;
        ResultSet res=null;
        try {
            conn = JdbcUtils.getConnection();

            // PreparedStatement 防止 SQL 注入的本质，把传递进来的参数当做字符
            // 假设其中存在转义字符， 比如说 '  就会被转义 
            String sql="select * from users where `name`=? and `password`= ? ";

            pst = conn.prepareStatement(sql);

            pst.setString(1, username);
            pst.setString(2, password);

            ResultSet resultSet = pst.executeQuery();

            while(resultSet.next()){
                System.out.println(resultSet.getObject("name"));
                System.out.println(resultSet.getObject("password"));
                System.out.println(resultSet.getObject("email"));
                System.out.println(resultSet.getObject("birthday"));
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            try {
                JdbcUtils.releaseResources(conn, pst, res);
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 10.7. 事务

> 代码实现

1.开启事务

2.一组事务执行完毕，提交事务

3.可以在catch语句中显性地定义 回滚 语句，但是默认失败其实也会回滚

```java
package com.guo.Jdbc04;

import com.guo.Jdbc02.utils.JdbcUtils;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class TestTransation {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement pst = null;
        ResultSet res = null;

        try {
            conn = JdbcUtils.getConnection();
            //关闭数据库自动提交,会自动开启事务，它相比直接在数据库写，少了一步操作（START TRANSACTION）
            conn.setAutoCommit(false);      // 即开启事务

            String sql1="update JDBC.account set money = money - 1000 where name = 'AAA'";
            pst  = conn.prepareStatement(sql1);
            pst.executeUpdate();

            String sql2="update JDBC.account set money = money + 1000 where name = 'BBB'";
            pst  = conn.prepareStatement(sql2);
            pst.executeUpdate();

            //业务完成，提交事务
            conn.commit();
            System.out.println("[DEBUG] UPDATE SUCCESS!");

        } catch (SQLException e) {
            // 如果这里不写  它也会回滚
            // 程序以及帮我们做好了
            // 这里只是显示的定义回滚语句，但是默认失败它就会回滚
            try {
                conn.rollback();
            } catch (SQLException e1) {
                e1.printStackTrace();
            }
            e.printStackTrace();
        }finally {
            try {
                JdbcUtils.releaseResources(conn, pst, res);
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

    }
}
```

### 10.8.数据库连接池

数据库--连接--释放

频繁连接--释放十分浪费资源

==池化技术：准备一些预先的资源，过来就能连接到预先准备好的==



编写连接池，实现一个接口 DataSource

> 开源数据源实现

DBCP

C3P0

Druid

使用了数据库连接池之后，在项目中就不需要编写连接数据库的代码了

> DBCP

需要的jar包

