# SQL

## SQL查询语言概览

- 数据定义语言（DDL）
- 数据操纵语言（DML）
- 完整性
- 视图定义
- 事务控制
- 嵌入式SQL和动态SQL
- 授权

## SQL数据定义

### 基本类型

- char(n):固定长度的字符串（会追加空格）
- varchar(n):可变长度的字符串
- int：整数类型
- smallint：小整数类型（和机器相关）
- numeric(p,d):定点数，p位数，d位小数
- real，double，precision：浮点数与双精度浮点数，精度与机器相关
- float(n)：精度至少为n位的浮点数

### 基本模式定义

create table 命令的通用形式

```sql
CREATE TABLE r
(
    A1 D1,
    A2 D2,
    AN DN,
    <完整性约束1>,
    <完整性约束K>
);
```

示例：

```sql
CREATE TABLE department(
    dept_name VARCHAR(20),
    building VARCHAR(15),
    budget NUMERIC(12,2),
    PRIMARY KEY(dept_name)
);
```

#### 完整性约束

- PRIMARY KEY：取值唯一
- FOREIGN KEY:外键约束
- NOT NULL :非空约束

## SQL查询的基本结构

### 单关系查询

示例：

```sql
SELECT name FROM instructor
```

- distnct 去除重复元组
- SELECT子句还可进行加减乘除运算
- WHERE子句选出满足条件的元组

### 多关系查询

示例：

```sql
SELECT name,instructor.dept_name,building
FROM instructor , department
WHERE instructor.dept_name = department.dept_name
```

典型的SQL查询语句形式：

```sql
SELECT A1,A2,A3,...,AN
FROM R1,R2,...,RN
WHERE P
```

笛卡尔积：

表1：

name|age
----|----
小明|15
小红|16

表2：

grade|school
----|----
5|中心小学
6|中心小学

两张表的笛卡尔积是：

name|age|grade|school
----|----|----|----
小明|15|5|中心小学
小红|16|6|中心小学
小明|15|6|中心小学
小红|16|5|中心小学

### 自然连接

```sql
SELECT name,instructor.dept_name,building
FROM instructor , department
WHERE instructor.dept_name = department.dept_name
```

上面那条SQL可以简化成下列形式：

```sql
SELECT name,instructor.dept_name,building
FROM instructor NATURAL JOIN department
```
### 附加的基本运算

### 更名运算

AS 关键字：可以修改列名

### 字符串运算

- upper()
- lower()
- trim()

LIKE 关键字：

- %表示匹配任何字符（包括什么都没有）
- _表示匹配一个字符

示例：

```sql
SELECT name FROM user WHERE name LIKE 'user%' 
# 查找用户名以user开头的用户
```

escape :用来标志逃逸字符

```sql
LIKE 'ab\\cd%' escape '\' #匹配所有以ab\cd开头的字符串
```

SQL1999 中提供了similar to操作，语法类似于正则表达式

### SELECT子句中的属性说明

可以用*代表所有列

### 排列元组的显示次序

ORDER BY 子句

- DESC 降序
- ASC 升序

```sql
SELECT name,age FROM student ORDER BY age DESC
# 根据学生的年龄进行降序排序
```

### WHERE 子句谓词

BETWEEN ... AND ...

```sql
money BETWEEN 9000 AND 10000
```

等价于

```sql
money >= 9000 AND money <= 10000
```

NOT BETWEEN ... AND ...

上面的取反

## 集合运算

### 并运算

UNION关键字

```sql
SELECT name FROM student WHERE age = 15
UNION
SELECT name FROM student WHERE age = 16
```

### 交运算

INTERSECT关键字
用法同上

### 差运算

EXCEPT 关键字
同上

## 空值

- IS NULL 判断是空值
- IS NOT NULL 判断非空

## 聚集函数

- AVG:求平均值
- MIN:求最小值
- MAX:求最大值
- SUM:求和
- COUNT:计数

### 分组聚集

GROUP BY 子句：
根据后面的列进行分组

```sql
select TO_DAYS(create_time),COUNT(1) 
FROM web_log GROUP BY TO_DAYS(create_time)
# 查询每天的访问次数
```

### HAVING子句

满足HAVING后的条件才会被选择

```sql
select TO_DAYS(create_time),COUNT(1) 
FROM web_log GROUP BY TO_DAYS(create_time) HAVING COUNT(1)>1000
# 查询访问次数1000的那些天
```

## 嵌套子查询

```sql
SELECT username FROM user WHERE user_id IN 
(SELECT user FROM state);
# 查询发表过动态的用户
```

### 集合比较

- some:某一些满足即可
- all：全部满足

```sql
SELECT username FROM user
WHERE age > all 
(SELECT age FROM user WHERE sex = '女')
# 查询出年龄大于全部女性年龄的用户
```

### 空关系测试

EXIST 关键字：
当改关键字后面的关系非空时返回true，反之返回false
相关子查询：

```sql
SELECT user_id FROM user 
WHERE EXISTS (SELECT * FROM state WHERE user = user_id);
# 查询发表过动态的用户ID
```

### 重复元组存在性测试

UNIQUE 关键字：
查询是否存在重复的元组

### FROM子句中的子查询

```sql
SELECT * FROM (SELECT username FROM user) AS T;
# 使用FROM子句子查询，有些数据库要求FROM后面的子查询需要指定一个别名
```

### WITH子句

提供定义临时关系的方法

### 标量子查询

如果一个子查询的结果只有一个元组，那么可以放在单个值能出现的任何地方：

```sql
SELECT username,(SELECT COUNT(1) 
FROM state WHERE state.user = user.user_id) FROM user;
# 查询每个用户的用户名及其发表的动态条数
```

## 数据库的修改

### 删除
```sql
DELETE FROM r
WHERE p
```

示例:

```sql
DELETE FROM user
WHERE username = 'root'
# 删除用户名为root的用户
```

### 插入

```sql
INSERT INTO user VALUES(1,'username',15);
# 这种方式需要指定全部列
INSERT INTO user(username,age) VALUES('username',15);
# 这种方式不需要指定全部列
INSERT INTO user SELECT * FROM user;
# 插入查询出来的元组
```

### 更新
```sql
UPDATE r
SET k1=v1,k2=v2,...,kn=vn
WHERE p
```
```sql
UPDATE user
SET username = 'abc'
WHERE username = 'root'
```

## 连接表达式

### 连接条件

JOIN...ON...

```sql
SELECT * FROM 
user JOIN user_info 
ON user.user_info = user_info.user_info_id;
# 连接两个表，查询出用户所有信息
```

上面的查询等价于:
```sql
SELECT * FROM user,user_info
WHERE user.user_info = user_info.user_info_id
```

### 外连接
- 左外连接：只保留出现在左外连接左边的关系中的元组（如果没有符合连接条件的元组，左表的元组还是会被展示出来）
- 右外连接：只保留出现在右外连接运算右边关系中的元组
- 全外连接：保留出现在两个关系中的元组

左外连接：

```sql
select * from user  
left outer join state on user.user_id = state.user;
# 把user和state进行连接，如果用户没有发表state，则仍保留用户，只是state相关列为NULL
```

右外连接如上取反
全外连接：原理同上，不详细解释（mysql不支持）

### 连接类型和条件

natural join等价于natural inner join

## 视图

定义：不是逻辑模型的一部分，但是作为虚关系对用户可见

### 视图定义

```sql
CREATE VIEW v AS <查询表达式>
```

创建一个部分用户视图：

```sql
CREATE VIEW user_part 
AS
SELECT * FROM user LIMIT 10
```

### SQL查询中使用视图

再查询中，视图能出现在关系名可以出现的任何地方

```sql
SELECT * FROM user_part
```

### 物化视图

如果用于定义视图的实际关系改变，视图也跟着修改。这样的视图称为物化视图

### 视图更新

一般来说，满足下列所有条件，则视图是可更新的
- FROM子句中只有一个数据库关系
- SELECT子句只包含关系的属性名，不包含任何表达式聚集或DISTINCT声明
- 没有出现在SELECT子句中的属性可以去空值，也不是主码的一部分
- 查询中没有GROUP BY 和HAVING子句

## 事务

定义：事务内的所有语句要么全部执行，要么全部不执行

- Commit work:提交当前事务
- Rollback work：回滚当前事务

## 完整性约束

完整性约束防止的是对数据的意外破坏。

### 单个关系上的约束

### NOT NULL约束

```sql
 CREATE TABLE `user` (
  `user_id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(20) NOT NULL,
  `password` varchar(32) NOT NULL,
  `user_info` int(11) NOT NULL,
  `permission` int(11) NOT NULL,
  `create_time` datetime NOT NULL,
  `update_time` datetime NOT NULL,
  `last_login` datetime DEFAULT NULL,
  PRIMARY KEY (`user_id`),
  UNIQUE KEY `username` (`username`)
) ENGINE=InnoDB AUTO_INCREMENT=293 DEFAULT CHARSET=utf8
```

表示禁止在该属性上插入NULL值

### UNIQUE 约束

被该约束修饰的属性在单个关系上是唯一的，由于NULL != NULL ，所以一个关系上允许存在多个NULL值

### CHECK 子句

check(p) 指定一个谓词P，只有当该谓词满足时，数据库才允许插入

### 参照完整性

一个关系中给定属性集上的取值在另一关系的特定属性集的取值中出现，这种情况称为参照完整性

```sql
CREATE TABLE test(
user_id INT,
FOREIGN KEY (user_id) REFERENCES user(user_id)
);
```

test表中的user_id参照user表的user_id

### 事务中对完整性约束的违反

如果事务中的某条SQL语句违反了完整性约束，则会马上进行检查。有些DBS支持将initially deferred加入到约束中，这样完整性约束检查就会在事务结束的时候进行。

### 复杂CHECK条件与断言

比如CHECK后面的谓词可以使用子查询：

```sql
CREATE TABLE test(
user_id INT 
CHECK(user_id IN( SELECT user.user_id FROM user))
)
```
这样在插入test表时，只有在user表中出现的user_id才被允许插入，但是大多数数据库还不支持
断言：
```sql
CREATE ASSERTION <name> CHECK <p>
```
任何在断言中涉及到的关系发生变动，都会触发断言。

## SQL中的数据类型与模式

### SQL中的日期和时间类型

- DATE:日历日期，包括年月日
- TIME :一天中的时间
- TIMESTAMP ：DATE+TIME

#### 与时间相关的函数：

- CURRENT_DATE：返回当前日期
- CURRENT_TIME：返回当前时间

### 默认值

如
```sql
CREATE TABLE test(
  user_id INT DEFAULT 0
);
```
当user_id未指定时，默认为0

### 创建索引

```sql
CREATE INDEX index_1 ON test(id)
```

### 大对象类型

- BLOB
- CLOB

### 用户定义的类型

### CREATE TABLE 的扩展

创建两个模式相同的表：

```sql
CREATE TABLE test1 LIKE test
```

从查询中创建表：

```sql
CREATE TABLE test2 AS 
(
SELECT * FROM test
)
WITH DATA;
# mysql不支持
```

### 模式、目录与环境

当代数据库提供了三层结构的关系命名机制，最顶层由**目录**构成，每个目录当中可以包含**模式**，目录 == 数据库。
默认目录和模式是为每个连接建立的SQL环境的一部分。

## 授权

- 授权读取
- 授权插入
- 授权更新
- 授权删除

### 权限的授予与收回

```sql
GRANT <权限列表>
ON <关系或视图>
TO <用户或角色列表>
```

```sql
GRANT SELECT ON department TO user1
# 授予user1查询department表的权限
```

public:代表当前系统的所有用户以及未来用户

```sql
REVOKE <权限列表>
ON <关系名或视图名>
FROM <用户/角色列表>
```

```sql
REVOKE SELECT ON department FROM user1
# 收回user1的查询权限
```

### 角色

创建角色：

```sql
CREATE ROLE <角色名>
```

```sql
GRANT admin to user1;
# 将admin角色授予user1
```

### 视图的授权

同上

### 模式的授权

```sql
GRANT REFERENCES (dept_name) ON department TO user1
# 允许user1创建这样的关系：它能参照department的dept_name
```

### 权限的转移

在授权语句最后加上 WITH GRANT OPTION
即允许用户可将权限授予给其他用户

### 权限的收回

默认情况下，多数DBS都会级联收回用户的权限
如果在收回语句最后加上 RESTRICT关键字，可以防止级联收回

## 存储过程

存储过程可以看成是对一系列 SQL 操作的批处理

- 代码复用
- 比较安全
- 性能较高

## 使用程序设计语言访问数据库

- 动态SQL:运行时构建SQL语句字符串与数据库进行交互
- 嵌入式SQL:SQL语句必须在编译时全部确定，由预处理器来连接宿主语言与数据库

### JDBC

一段经典的JDBC代码：

```java
// 加载驱动
 Class.forName("com.mysql.jdbc.Driver");
 // 获取连接
 Connection connection =
         DriverManager.getConnection("jdbc:mysql:///test","root","Root@@715711877");
 // 执行SQL

ResultSet resultSet = connection.prepareStatement("SELECT * FROM test").executeQuery();

//取回结果集
while (resultSet.next()){
    System.out.println(resultSet.getInt("id")+"|"
            +resultSet.getString("name"));
}
connection.close();
```