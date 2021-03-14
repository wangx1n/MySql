# mysql

## 1.初识数据库

### 1.3 数据库分类

**关系型数据库**（Sql）

* MySQL, Oracle,  db2, Sql Server, SQLlite
* 通过表与表之间的，行与列之间的关系存储数据。



**非关系型数据库**（noSql）

* Redis, MongDB
* 非关系型数据库，同过对象存储，同过对象自身的属性来决定。



### 1.7 连接数据库

命令行连接

```sql
mysql -uroot -p12345678 --连接数据库
update mysql.user set authentication_string=password('123456') where user='root' and Host = 'localhost'; --修改数据库密码
flush privileges; -- 刷新权限

show databases; --枚举所有数据库

use bess_gather_test --切换数据库

show tables; --查看数据库中所有表

describe sys_set_tag; --显示数据库中所有表信息

create database westods; --创建数据库

exit; --退出

-- 单行注释
/*
多行注释
*/
```



**数据库\*\*\*语言**

DDL 定义

DML 操作

DQL 查询

DCL 控制



## 2. 操作数据库

操作数据库>操作数据库中的表>操作表中的数据



### 2.1 操作数据库

1.创建数据库

```sql
CREATE DATABASE IF NOT EXISTS westors
```

2.移除数据库

```sql
DROP DATABASE westors;
```

3.使用数据库

```sql
USE `school`
```

### 2.2 数据库的列类型

>数值

* tinyint		十分小的数据		1个字节
* smallint      小的数据		2个字节
* **int		标准的数据		4个字节**
* mediumint 	中等的数据		3个字节
* bigint           大的数据		8个字节



* float			浮点数		4个字节
* double        浮点数        8个字节
* **decimal       字符串形式的浮点数**        



> 字符串

* char            字符串       固定大小 0-255
* **varchar       可变字符串    0-65535**  String
* tinytext       微型文本   2^8-1
* **text               文本         2^16-1**



> 时间日期

java.util.date

* date YYYY-MM-DD 日期
* time HH:mm:ss 时间格式
* **datetime YYYY-MM-DD HH:mm:ss 最常用**
* **timestamp 时间戳1970.1.1到现在的毫秒数**



> null

* 不要使用NULL进行运算 



### 2.3 数据库字段的属性（重点）

Unsigned

* 无符号整数
* 声明该列不能为负数



zefofill

* 0填充
* 不足的位数使用0填充



自增

autoIncrement

* 通常理解为自增，在上一条记录基础上+1
* 通常用来设计主键，必须是整数类型



非空

* 假设设置为notull，如果不给他赋值，就会报错
* null，不填值，就为null



默认

* 设置默认的值



int值的长度，指的是显示宽度，与0填充有关



### 2.4 创建数据库表

```sql
--目标 创建student表
--表的名称和字段用``扩起
--使用英文()
--auto_increment 自增
--字符串用''括起来
--所有语句用，分割，最后一个不用加
--一般一个表用一个主键

CREATE TABLE IF NOT EXISTS `student`(
	`id` INT(10) NOT NULL AUTO_INCREMENT comment '学号',
	`name`		VARCHAR(30) NOT NULL DEFAULT '匿名' COMMENT '姓名',
	`pwd`		VARCHAR(20) NOT NULL DEFAULT '123456' COMMENT '密码',
	`sex`		VARCHAR(2) NOT NULL DEFAULT '男'	COMMENT '性别',
	`birthday`	DATETIME DEFAULT NULL COMMENT '出生日期',
	`address`		VARCHAR(100)	DEFAULT NULL COMMENT '家庭住址',
	`email` 		VARCHAR(50)		DEFAULT NULL COMMENT '邮箱',
	PRIMARY KEY(`id`)
)ENGINE = INNODB DEFAULT CHARSE=utf8

-----------------------------------------------------------------------

CREATE TABLE [IF NOT EXIST] `表名`(
    `列名` 列类型 [属性] [索引] [注释],
    `列名` 列类型 [属性] [索引] [注释],
    ...
    `列名` 列类型 [属性] [索引] [注释]
)[引擎][字符集][注释]
```



**查看创建数据库和表的语句**

```sql
SHOW CREATE DATABASE school --查看创建数据库的语句
SHOW CREATE TABLE student --查看student数据表的定义语句
DESC student --显示表结构
```



### 2.5 数据表的类型

InnoDB: 默认

MYISAM:5.5以前

|            | MYISAM         | InnoDB                 |
| ---------- | -------------- | ---------------------- |
| 事务的支持 | 不支持         | 支持                   |
| 数据行锁定 | 不支持（表锁） | 支持                   |
| 外键       | 不支持         | 支持                   |
| 全文索引   | 支持           | 不支持                 |
| 表空间大小 | 较小           | 较大，约为MYISAM的两倍 |

常规使用操作：

* MYISAM:	节约空间，速度较快
* InnoDB:      安全性高，事务的处理，多表多用户操作



> 在物理空间存在的位置

所有的数据库文件都存在data文件夹下

本质还是文件存储



MySQL引擎在物理文件上的区别

* InnoDB 在数据库表中只有一个*.frm文件，以及上级目录下的ibdate文件
* MYISAM 对应文件
  * *.frm  表结构的定义文件
  * *.MYD 数据文件（data）1
  * *.MYI  索引文件（index）



> 设置数据库表的字符集编码

```sql
CHARSET=uft8
```

MySQL默认编码是Latin1，不支持中文

在my.ini中配置默认的编码

```ini
character-set-server=utf8
```



### 修改和删除表

> 修改表

```sql
修改表名
ALTER TABLE `旧表名` RENAME AS `新表名`
--------------------------------------
ALTER TABLE teacher RENAME AS teacher1

增加表字段
ALTER TABLE `旧表名` ADD `字段名` `列属性`
--------------------------------------
ALTER TABLE teacher1 ADD age INT(11)

修改表字段
修改约束
ALTER TABLE `旧表名` MODIFY `字段名` `列属性`
--------------------------------------
ALTER TABLE teacher1 MODIFY age VARCHAR(3)

修改字段名
ALTER TABLE `旧表名` change `旧字段名` `新字段名` `列属性`
--------------------------------------
ALTER TABLE teacher1 change age age1 INT(3)

删除字段
ALTER TABLE `表名` DROP `字段名`
--------------------------------------
ALTER TABLE teacher1 DROP age1

```

> 删除表

```sql
DROP TABLE IF EXISTS teacher1 --如果表存在在删除
```



注意点

* `` 所有的字段名使用这个包裹
* 注释 -- /**/
* sql关键字大小写不敏感



## 3. MySQL数据管理

### 3.1 外键

```sql
CREATE TABLE `grade`(
	`id` INT(10) NOT NULL AUTO_INCREMENT COMMENT 'id',
	`name` VARCHAR(10) NOT NULL COMMENT '年级名',
	PRIMARY KEY(`id`)
)ENGINE=INNODB CHARSET=utf8
```



* 建表时增加外键 

```sql
CREATE TABLE IF NOT EXISTS `student`(
	`id` INT(10) NOT NULL AUTO_INCREMENT comment '学号',
	`name`		VARCHAR(30) NOT NULL DEFAULT '匿名' COMMENT '姓名',
	`pwd`		VARCHAR(20) NOT NULL DEFAULT '123456' COMMENT '密码',
	`sex`		VARCHAR(2) NOT NULL DEFAULT '男'	COMMENT '性别',
	`birthday`	DATETIME DEFAULT NULL COMMENT '出生日期',
	`address`		VARCHAR(100)	DEFAULT NULL COMMENT '家庭住址',
    
    `grade`		INT(10) NOT NULL COMMENT '年级'
    
	`email` 		VARCHAR(50)		DEFAULT NULL COMMENT '邮箱',
	PRIMARY KEY(`id`),	
    KEY `FK_grade`(`grade`),
    CONSTRAINT 	FOREIGN KEY(`grade`) REFERENCES `grade`(`gradeid`)
)ENGINE = INNODB DEFAULT CHARSE=utf8
```

* 建表后修改外键

```sql
ALTER TABLE `表名` ADD [CONSTAINT `外键名字`] FOREIGN KEY(`字段名`) REFERENCES `表名`（`字段名`）
-------------------------------------------------------------------------------------------
ALTER TABLE student add constraint `FK_grade` foreign key (`grade`) references `grade`(`id`);
```







### 3.2 DML语言（全部记住）

**数据库意义**：数据存储，数据管理

DML语言：数据操作语言

* insert
* update
* delete



### 3.3 插入

```sql
-- insert into 表名([字段1，字段2，字段3])values('值1')，('值2'),(...)
insert into `grade`(`name`)values('大一');
--插入语句，字段要和数据一一对应

insert into `grade`(`name`)values('大二'),('大三');
--同一个字段，插入多个值，用括号隔开

INSERT INTO `student`(`name`,`pwd`) VALUES ('李','12345678'),('童潜','1234')
--插入这句话的时候发现主键被修改为不自增了，要想修改成自增要修改为：
ALTER TABLE student modify id1 int(3) auto_increment PRIMARY key ;
```

语法：`insert into 表名([字段1，字段2，字段3])values('值1')，('值2'),(...)`

**注意事项**

* 字段与字段之间使用英文逗号隔开
* 字段可以省略，但是省略后面的值要一一对应
* 可以同时插入多条记录，VALUES后面的值要逗号分开



### 3.4 修改

```s\ql
update `student` set `name`='wangxin' where `name`='王鑫'
--不指定查询条件，会修改表中所有记录
update `student` set `name`='wangxin'
--修改多个属性，逗号隔开
update `student` set `name`='wangdong', email = '925798209@qq.com' where `name`='王鑫'

语法：update 表名 set `字段名` = '字段内容'，`字段名`= `字段内容` where `字段名` = `字段内容`
```

条件：where 子句 运算符 id 等于 某个值，大于某个值，或在区间

| 操作符           | 含义   | 范围 | 结果       |
| ---------------- | ------ | ---- | ---------- |
| =                | 等于   |      | false/true |
| <>或!=           | 不等于 |      | false/true |
| >                |        |      |            |
| <                |        |      |            |
| >=               |        |      |            |
| <=               |        |      |            |
| between...and... | []     |      |            |
| AND              |        |      |            |
| OR               |        |      |            |



**注意：**

* column_name，数据库列，尽量带上``
* 条件，筛选的条件，如果没有指定，则会修改所有的列
* value，是一个值，也可以是一个变量



### 3.5  删除

> delete

```sql
--尽量避免这么写，会清空整张表
DELETE FORM student
--删除指定数据
DELETE FROM student where id = 1
```

> truncate

作用：完全清空数据表，表的结构和索引约束不会变。

> delete 和 truncate区别

相同：

* 都能删除数据，都不会删除表结构

不同：

* TRUNCATE	重新设置自增列，从0开始
* TRUNCATE    不会影响事务



## 4. DQL 查询

```sql
--查询带别名
SELECT `student_id` AS 学号,`student_name` AS 学生姓名 FROM student AS 学生表
--拼接函数Concat(a,b)
SELECT CONCAT('姓名:',student_name) AS 姓名 FROM student AS 学生表
```



> 去重 distinct

```sql
SELECT DISTINCT `STUDENT_NO` FROM result 
```

去除select 中重复的字段



```sql
SELECT @@auto_increment_increment --查询自增的步长
```



> 模糊查询

| 运算符      | 语法              | 描述                                      |
| ----------- | ----------------- | ----------------------------------------- |
| IS NULL     | a is null         | null为真                                  |
| IS NOT NULL | a is not null     | !null为真                                 |
| BETWEEN     | a between b and c | a在b、c之间，则为真                       |
| LIKE        | a like b          | 如果a匹配b，则为真                        |
| IN          | a in (a1,a2,a3)   | 假设a在a1，a2, a3其中的某一个值，其值为真 |



```sql
--like结合%（代表0-任意个字符） _（一个字符）
--in查询的字段在in集合中

--null
--查询地址字段为空的人
SELECT ... FROM ... WHERE address IS NOT NULL OR address=''
```



### 4.3 联表查询

> JOIN对比

| 操作       | 描述                                         |
| ---------- | -------------------------------------------- |
| INNER JOIN | 如果表中至少有一个匹配的值，就返回行         |
| LEFT JOIN  | 即使右表中没有匹配，也会从左表中返回所有的值 |
| RIGHT JOIN | 即使左表中没有匹配，也会从右表中返回所有的值 |



```sql
SELECT DISTINCT d.tag_id,d.`value`,d.gather_time,s.tag_alias
FROM data_gather_int AS d
LEFT JOIN sys_set_tag AS s
ON d.tag_id = s.id
ORDER BY d.gather_time ASC

--join on 连接查询
--where 等值查询

--联三张表，注意where ORDER_BY要放在最后面的位置
SELECT DISTINCT s.tag_alias,d.`value`,d.gather_time,d.tag_id,md.name
FROM data_gather_int AS d
LEFT JOIN sys_set_tag AS s
ON d.tag_id = s.id
INNER JOIN meta_device AS md
ON s.device_id = md.id
WHERE d.gather_time BETWEEN '2020-01-01' AND '2021-12-31'
ORDER BY d.gather_time ASC
```



> 自连接

**一张表拆成两张表，再连接**

父类

| c_id | c_name   |
| ---- | -------- |
| 2    | 信息技术 |
| 3    | 软件开发 |
| 5    | 美术设计 |
|      |          |

子类

| pid  | c_id | c_name   |
| ---- | ---- | -------- |
| 3    | 4    | 数据库   |
| 2    | 8    | 办公信息 |
| 3    | 6    | web开发  |
| 5    | 7    | ps设计   |

操作：查询父类对应的子类关系

```sql
SELECT a.`c_name` AS '父栏目',b.`c_name` AS '子栏目'
FROM `category` AS a ,`catagory` AS b
where a.`c_id` = b.`pid`
```

查询结果：

| 父栏目   | 子栏目   |
| -------- | -------- |
| 软件开发 | 数据库   |
| 软件开发 | web开发  |
| 信息技术 | 办公信息 |
| 美术设计 | ps设计   |



### 4.5 分页和排序

排序：升序ASC，降序DESC



分页：limit 起始位置，结束位置



### 4.6 子查询和嵌套查询

```sql
--查询分数高于80分的学生的学号姓名，且科目为高等数学-2

--使用子查询
SELECT  DISTINCT s.`StudentNo`,`studentName`
FROM student s
INNER JOIN result r
ON r.studentNO = s.studentNO
WHERE `StudentResult` >= 80
AND
`SubJectNo` = (
    SELECT `SubjectNO` FROM `subject`
    WHERE `SubjectName` = `高等数学-2`
)

--使用联表查询
SELECT  DISTINCT s.`StudentNo`,`studentName`
FROM student s
INNER JOIN result r
ON r.studentNO = s.studentNO
INNER JOIN subject sb
ON sb.SubjectNo = r.SubjectNo
WHERE `StudentResult` >= 80 
AND SubjectName = `高等数学-2`

--嵌套查询
SELECT `studentNO`,`studentNO` FROM student WHERE studentNo IN(
    SELECT `studentNO` FROM result WHERE StudentResult >= 80 AND subjectNO IN(
        SELECT `subjectNO` FROM subject WHERE subjectName = '高等数学-2'
 )
)
```



### 4.7 分组

```sql
SELECT subJectName,AVG(studentResult) AS 平均分,MAX(studentResult),Min(studentResult)
FROM result r
INNER JOIN `subject` s
ON r.subjectNO = s.subjectNO
GROUP BY r.subjectNO
HAVING 平均分 > 80
```



## 5 MySql函数

### 5.1 常用函数

```sql
--数字
SELECT ABS(-8) --绝对值
SELECT CEILING(9.4) --向上取整
SELECT FLOOR(9.4) --向下取证
SELECT RAND() --返回一个0-1之间的随机数
SELECT SIGN() --判断一个数的符号 负数返回 -1 正数返回 1 0 返回0

--字符串
SELECT CHAR_LENGTH('') --返回字符串长度
SELECT CONCAT('','') --合并字符串
SELECT INSERT('原字符串','1','2','改后') --从指定位置替换字符串
SELECT LOWER('') --转小写
SELECT UPPER('') --转大写
SELECT INSTR('guangzhou','h',) --返回第一次出现的索引
SELECT REPLACE('准备替换','准备','完成') --替换指定字符串
SELECT SUBSTR('准备截取字符串',1,4) --从指定的位置截取，截4个
SELECT REVERSE('') --反转字符串

--日期
SELECT CURRENT_DATE()
SELECT CURDATE()
SELECT NOW()
SELECT LOCALTIME()
SELECT SYSDATE()
SELECT SECOND(NOW())
```



### 5.2 聚合函数

| 函数名称 | 描述   |
| -------- | ------ |
| COUNT()  | 计数   |
| SUM()    | 求和   |
| AVG()    | 平均值 |
| MAX()    |        |

```sql
SELECT COUNT(student) FROM student --查询字段计数，会忽略所有的null值
SELECT COUNT(*) FROM student --查询表有多少行 不会忽略null值
SELECT COUNT(1) FROM student --查询表有多少行 不会忽略null值，速度更快
```



## 6.事务

== **要么都成功，要么都失败**==

> ACID原则 原子性，一致性，隔离性，持久性

**原子性**：要么都执行，要么都不执行

**一致性**：事务操作前后状态一致

**持久性**：事务结束后数据不会随着外界原因丢失。没有提交，恢复到原状，提交了，就不变。

**隔离性**：事务之间不会相互影响



**脏读**：一个事务读取了另一个事务未提交的数据

**不可重复读**：多次读取表结果不同

**幻读**：一个事务读取到了别人插入的数据，导致前后读取不一致



> 手动开启事务

```sql
--1.关闭自动提交
SET autocommit = 0
--2.开启一个事务
START TRANSECTION
--3.提交
COMMIT
--4.回滚
ROLLBACK
--5，事务结束
SET autocommit = 1
```



```sql
--设置保存点
ROLLBACK TO SAVEPOINT --回滚到保存点
RELEASE SAVEPOINT --删除保存点
```



## 7.索引

### 7.1 索引的分类

* 主键索引 (PRIMARY KEY)
  * 唯一标识，不可重复，只能有一个列作为主键
* 唯一索引 (UNIQUE KEY)
  * 避免重复的列出现，唯一索引可以重复，唯一索引可以重复
* 常规索引（INDEX/KEY）
  * 默认
* 全文索引 （FullText）
  * 特定的数据库引擎可以



```sql
SHOW INDEX FROM student --显示表的索引信息

ALTER TABLE `student` ADD FULLText INDEX `studentname`(`studentname`) --增加一个全文索引

CREATE INDEX id_app_user_name ON app_user(`username`)

EXPLAIN SELECT * FROM student --非全文索引

EXPLAIN SELECT * FROM student WHERE MATCH(studentname) AGAINST ('王')
```



## 7.3 索引原则

* 索引不是越多越好
* 不要对经常变动的数据加索引
* 小数据量的表不需要加索引
* 索引一般加载常用来查询的字段上



> 索引的数据结构

Btree : InnoDB默认



## 9 数据库规范设计

> 第一范式

每一个字段都是不可再分的原子项

> 第二范式

前提：满足第一范式

一张表的每一列都必须与主键相关

> 第三范式

前提：满足第一范式和第二范式

一张表的每一列都必须和主键直接相关



**关联查询的表不得超过三张表**