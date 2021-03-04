# mysql

## 1.初识数据库

### 1.3 数据库分类

**关系型数据库**（Sql）

* MySQL, Oracle,  db2, Sql Server, SQLlite
* 通过表与表之间的，行与列之间的关系存储数据。



**关系型数据库**（noSql）

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
  * *.frm -表结构的定义文件
  * *.MYD 数据文件（data）
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



