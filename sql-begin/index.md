# 每日学习 MySQL 计划


上一个计划“每日学习 vim 计划”在今天晚上（圣诞节）结束了，感觉效果挺好；虽然只存活了 10 天；但是 vim 是曾经接触过，所以计划就提前结束了；

这次开始学习`MySQL`; 为下学期数据库选修课做准备

# Begin at Christmas
## 登录到 mysql

> mysql -h 主机名 -u 用户名 -p

和 shell 一样，输入 exit 退出登录

## 创建数据库
对表操作之前需要先选定 database；如果没有 database 可以先新建
```
create database samp_db character set gbk;
drop database samp_db;   #删除库
use samp_db          #选择数据库
show databases[tables]  #显示数据库列表或者表列表
```

## 数据类型
在创建 table 前先了解下数据类型

*数字类型*

|MySQL 数据类型|	 含义（有符号）|
|-------------|----------------|
|tinyint(m)	  |  1 个字节  范围 (-128~127)|
|smallint(m)  |  2 个字节  范围 (-32768~32767)|
|mediumint(m) |  3 个字节  范围 (-8388608~8388607)|
|int(m)	      |  4 个字节  范围 (-2147483648~2147483647)|
|bigint(m)	  |  8 个字节  范围 (+-9.22*10 的 18 次方）|

| 属性 | 存储空间 | 精度 | 精确性 | 说明 |
| ---- | ----  | ---- | ---- | ---- |
|FLOAT(M, D)|4 bytes|单精度|非精确| 单精度浮点型，m 总个数，d 小数位 |
|DOUBLE(M, D)|8 bytes|双精度|比 Float 精度高| 双精度浮点型，m 总个数，d 小数位 |

*时间类型*

| 类型 | 字节 | 例 | 精确性 |
| ---- | ----  | ---- | ---- |
| DATE | 三字节 | 2015-05-01 | 精确到年月日 |
| TIME | 三字节 | 11:12:00 | 精确到时分秒 |
| DATETIME | 八字节 | 2015-05-01 11::12:00 | 精确到年月日时分秒 |
| TIMESTAMP |  | 2015-05-01 11::12:00 | 精确到年月日时分秒 |
TIMESTAMP 类型可动态变化

*字符串类型*

| 类型 | 单位 | 最大 | 特性 |
| ---- | ----  | ---- | ---- |
| CHAR | 字符 | 最大为 255 字符 | 存储定长，容易造成空间的浪费 |
| VARCHAR | 字符 | 可以超过 255 个字符 | 存储变长，节省存储空间 |
| TEXT | 字节 | 总大小为 65535 字节，约为 64KB | - |


# Day Two
今天终于又空闲下来继续学习 MySQL 了；

不能一口气吃成胖子；

有一个比较基础的问题就是每次使用 sql 时 都要启动 MariaDB 的系统守护进程`systemctl start mariadb.service`当然你可以设置成开机自启；如果你不在意那点开机速度的话

## Primary Key 主键

数据库表中对储存数据对象予以唯一和完整标识的数据列或属性的组合。一个数据表只能有一个主键，且主键的取值不能缺失，即不能为空值（Null）。

主键是唯一且不重复的；比如在学校里学号是不会重复的，就可以设置为主键。

## 创建 Table
下面这段代码是创建 Table 的常见形式。
```
-- 如果数据库中存在 user_accounts 表，就把它从数据库中 drop 掉
DROP TABLE IF EXISTS `user_accounts`;
CREATE TABLE `user_accounts` (
  `id`             int(100) unsigned NOT NULL AUTO_INCREMENT primary key,
  `password`       varchar(32)       NOT NULL DEFAULT '' COMMENT '用户密码',
  `reset_password` tinyint(32)       NOT NULL DEFAULT 0 COMMENT '用户类型：0－不需要重置密码；1- 需要重置密码',
  `mobile`         varchar(20)       NOT NULL DEFAULT '' COMMENT '手机',
  `create_at`      timestamp(6)      NOT NULL DEFAULT CURRENT_TIMESTAMP(6),
  `update_at`      timestamp(6)      NOT NULL DEFAULT CURRENT_TIMESTAMP(6) ON UPDATE CURRENT_TIMESTAMP(6),
  -- 创建唯一索引，不允许重复
  UNIQUE INDEX idx_user_mobile(`mobile`)
)
ENGINE=InnoDB DEFAULT CHARSET=utf8
COMMENT='用户表信息';
```
`NOT NULL`代表数据列不允许包含`NULL`;`NULL`代表可以为`NULL`

# 元旦特辑
第三篇就到元旦特辑了，嘻嘻嘻，在大家都在写电生理报告的时候，我想到自己已经鸽了很久的 MySQL 篇了

好了，学习数据库肯定得有数据啊，自己创建一个固然要学，可是自己编一个数据库出来也不现实啊
## 导入.sql 文件
MySQL 数据文件的后缀是.sql 官方有提供几个 example databases; 所以直接用官方给的吧

> [MySQL example databases](https://dev.mysql.com/doc/index-other.html)

我下载的是`sakila`，下载下来后`unzip`后得到三个文件`sakila-schema.sql`,`sakila.mwb`,`sakila-data.sql`

在 mariadb 中 使用 source 命令导入下载的 database,`source [path]`; 顺序是先导入 sakila-schema.sql, 然后是 sakila-data

```
mariadb root@localhost:sakila> show databases
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sakila             |
+--------------------+
4 rows in set
Time: 0.034s
```
可以看到 sakila 已经被导入进来了；
```
mariadb root@localhost:sakila> use sakila
You are now connected to database "sakila" as user "root"
Time: 0.001s
mariadb root@localhost:sakila> show tables
10 rows in set
Time: 0.033s

+------------------+
| Tables_in_sakila |
+------------------+
| actor            |
| address          |
| category         |
| city             |
| country          |
| customer         |
| film             |
| film_actor       |
| film_category    |
| film_text        |
+------------------+

```
XD; 导入成功了；可以开始继续学习操作了

## SELECT 查询
如果看到 select 后面跟了很长一串；请不要惊慌，select 最核心的语法只是

> SELECT 列名称 1, 列名称 2 FROM 表名称

- 首先要注意两个列名称之间用逗号分隔，不然会被认为是取一个别名（省略 as）
- 其次如果要选取所有列 可以使用 SELECT * FROM 表名称
- 如果有两张表，有相同的字段名，比如两个表 t1 和 t2，那么查询时需要显式指定查询的字段属于哪张表，select t1.id, t1.name, t2.id, t2.name from t1, t2，以避免冲突

如果想查询不重复的值只要使用`DISTINCT`就可以了

> SELECT DISTINCT 列名称 FROM 表名称

# Day Four
## WHERE
`where` 语句用来设定条件，可以和 SELECT 组合，也可以和`DELETE`，`INSERT`等组合

判断条件的操作符有`=`、`！=`、`>`、`<`、`>=`、`<=`

两个或者两个以上条件，可以用`OR`、`AND`连接

> SELECT * FROM Persons WHERE FirstName='Thomas' AND LastName='Carter';

## UPDATE
`UPDATE`用于更新表中的数据

> UPDATE 表名称 SET 列名称 = 新值，列名称 = 新值 WHERE 列名称 = 某值

```
update actor set last_name = 'Han', first_name = 'Taohai' where actor_id = 1;
+----------+------------+-----------+---------------------+
| actor_id | first_name | last_name | last_update         |
+----------+------------+-----------+---------------------+
| 1        | Taohai     | Han       | 2019-01-02 12:09:26 |
+----------+------------+-----------+---------------------+
```
# Day Five
## INSERT INTO
`INSERT INTO`用于向表中插入新行

> INSERT INTO 表名称 VALUES （值 1, 值 2,....)

如果要指定要插入数据的列可以使用

> INSERT INTO table_name （列 1, 列 2,...) VALUES （值 1, 值 2,....)

比如
```
insert into actor (first_name, last_name) values ('Yi', 'Han')
```
将插入一新行，actor_id 是`ACTO_INCREMENT`的，所以不用指定，也会自动生成

## DELETE
`DELETE`用于删除表中一行

> DELETE FROM 表名称 WHERE 列名称 = 值

在不用`WHERE`约束的情况下，将删除表中所有行，`DELETE FROM table_name`

# Day Six
## ORDER BY
在select语法的最后加上`ORDER BY 列名称`就可以对所选的数据进行排序，缺省是升序排列
如果要降序，可以在最后加上`DESC`的描述
```
SELECT Company, OrderNumber FROM Orders ORDER BY Company DESC
```
## IN
在`WHERE`语法中可以使用`IN`来规定多个值
语法为
> SELECT 列名称 FROM Table 名称 WHERE 列名称 IN (`值1`， `值2`，...)

使用`NOT`可以排除某些值，语法为在`IN`之前加`NOT`，

> SELECT 列名称 FROM Table 名称 WHERE 列名称 NOT IN (`值1`， `值2`，...)

## UNION
`UNION`用于 合并两个`SELECT`里，合并是指 第二个查询直接接在第一个查询后面

考试周到了，这大概是本学期最后一更了，鸽了鸽了

