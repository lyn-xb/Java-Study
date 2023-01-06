# 数据库基础

## 1、相关概念:

* DataBase,DB:
* DataBase Management System,DBMS:
  *   关系型数据库:建立在关系模型上的数据库

      > 由多张能互相连接的二维表组成的数据库，格式一致易于维护，SQL操作使用方便，数据储存在磁盘中。
  * 层次型数据库
  * 网络型数据库
* Structures Query Language,SQL:操作关系型数据库的同归标准

## 2、MySQL

*   初始化mysql：

    > mysqld --1 initialize-insecure
*   注册服务：

    > mysqld -install
*   启动、关闭、移除服务：

    > net start mysql // 启动mysql服务\
    > net stop mysql // 停止mysql服务 mysqld -remove mysql
*   修改默认账户密码

    > mysqladmin -u root password 123456
*   登录

    > mysql -u用户名 -p密码 -h要连接的mysql服务器的ip地址(默认127.0.0.1) -P端口号(默认3306)
*   退出

    > quit\exit

## 3、SQL

### 通用语法：

* SQL 语句可以单行或多行书写，以分号结尾。
* MySQL 数据库的 SQL 语句不区分大小写，关键字建议使用大写。
* 注释
  *   单行注释:

      > \-- 注释内容 或 #注释内容(MySQL 特有)。\
      > (注意：使用-- 添加单行注释时，--后面一定要加空格，而#没有要求)
  *   多行注释:

      > /\* 注释 \*/

### 分类：

#### (1) DDL(Data Definition Language)：数据定义语言，用来定义数据库对象：数据库，表，列等

* 操作数据库
  *   查询数据库

      > SHOW DATABASES;
  *   创建数据库

      > CREATE 1 DATABASE 数据库名称; //创建数据库\
      > CREATE DATABASE IF NOT EXISTS 数据库名称; //创建数据库(判断，如果不存在则创建)
  *   删除数据库

      > DROP DATABASE 数据库名称; //删除数据库\
      > DROP DATABASE IF EXISTS 数据库名称; //删除数据库(判断，如果存在则删除)
  *   使用数据库

      > USE 数据库名称; //使用数据库 SELECT DATABASE(); //查看当前使用的数据库
* 操作表
  *   查询表当前数据库下所有表名称

      > SHOW TABLES; //查询表当前数据库下所有表名称\
      > DESC 表名称; //查询表结构
  * 创建表
    * 数据类型
      *   数值

          > tinyint : 小整数型，占一个字节\
          > int ：大整数类型，占四个字节\
          > double ：浮点类型\
          > 使用格式：字段名 double(总长度,小数点后保留的位数)
      *   日期

          > date ： 日期值。只包含年月日\
          > datetime ： 混合日期和时间值。包含年月日时分秒
      *   字符串

          > char ： 定长字符串\
          > 优点：存储性能高\
          > 缺点：浪费空间\
          > eg ： name char(10) 如果存储的数据字符个数不足10个，也会占10个的空间\
          > varchar ： 变长字符串\
          > 优点：节约空间\
          > 缺点：存储性能底\
          > eg ： name varchar(10) 如果存储的数据字符个数不足10个，那就数据字符个数是几就占几个的空间
    *   语句

        > CREATE TABLE 表名 (\
        > 字段名1 数据类型1,\
        > 字段名2 数据类型2,\
        > …\
        > 字段名n 数据类型n\
        > );
  *   删除表

      > DROP TABLE 表名; //删除表\
      > DROP TABLE IF EXISTS 表名; //删除表时判断表是否存在
  *   修改表

      > ALTER TABLE 表名 RENAME TO 新的表名; //修改表名\
      > ALTER TABLE 表名 ADD 列名 数据类型; //添加一列\
      > ALTER TABLE 表名 MODIFY 列名 新数据类型; //修改数据类型\
      > ALTER TABLE 表名 CHANGE 列名 新列名 新数据类型; //修改列名和数据类型\
      > ALTER TABLE 表名 DROP 列名; //删除列

#### (2) DML(Data Manipulation Language):数据操作语言，用来对数据库中表的数据进行增删改

*   添加数据

    > INSERT INTO 表名(列名1,列名2,…) 1 VALUES(值1,值2,…); //给指定列添加数据\
    > INSERT INTO 表名 VALUES(值1,值2,…); //给全部列添加数据\
    > INSERT INTO 表名(列名1,列名2,…) VALUES(值1,值2,…),(值1,值2,…),(值1,值2,…)…;\
    > INSERT INTO 表名 VALUES(值1,值2,…),(值1,值2,…),(值1,值2,…)…; //批量添加数据
*   修改数据

    > 表名 SET 列名1=值1,列1 名2=值2,… \[WHERE 条件]; //修改表数据\
    > 注：修改语句中如果不加条件，则将所有数据都修改
*   删除数据

    > DELETE FROM 表名 \[WHERE 条件];

#### (3) DQL(Data Query Language):数据查询语言，用来查询数据库中表的记录(数据)

*   完整语法

    > SELECT\
    > 字段列表\
    > FROM\
    > 表名列表\
    > WHERE\
    > 条件列表\
    > GROUP BY\
    > 分组字段\
    > HAVING\
    > 分组后条件\
    > ORDER BY\
    > 排序字段\
    > LIMIT\
    > 分页限定\
    >
*   基础查询

    > SELECT 字段列表\[AS 别名] FROM 表名; //查询多个字段\
    > SELECT \* FROM 表名; //查询所有数据\
    > SELECT DISTINCT 字段列表 FROM 表名; //去除重复记录
*   条件查询

    > SELECT 字段列表 FROM 表名 WHERE 条件列表;

    <figure><img src="../.gitbook/assets/image_condition.png" alt=""><figcaption></figcaption></figure>

    *   模糊查询\
        模糊查询使用like关键字，可以使用通配符进行占位:\
        （1）\_ : 代表单个任意字符\
        （2）% : 代表任意个数字符

        > * 查询姓'马'的学员信息:\
        >   select \* from stu where name like '马%';

        > * 查询第二个字是'花'的学员信息:\
        >   select \* from stu where name like '\_花%';

        > * 查询名字中包含 '德' 的学员信息:\
        >   select \* from stu where name like '%德%';
*   排序查询

    > SELECT 字段列表 FROM 表名 ORDER BY 排序字段名1 \[排序方式1],排序字段名2 \[排序方式2] …;\
    > 注意：排序方式ASC：升序排列（默认值）、DESC：降序排列，如果有多个排序条件，当前边的条件值一样时，才会根据第二条件进行排序
*   聚合函数

    （1）将一列数据作为一个整体，进行纵向计算

    （2）分类：

    > * count(列名)：统计数量（一般选用不为null的列，可以直接使用 count(\*) ）&#x20;
    > * max(列名)：最大值&#x20;
    > * min(列名)：最小值&#x20;
    > * sum(列名)：求和&#x20;
    > * avg(列名)：平均值

    （3）语法：

    > SELECT 聚合函数名(列名) FROM 表;
    >
    > （注：null 值不参与所有聚合函数运算）
*   分组查询

    （1）语法：

    > SELECT 字段列表 FROM 表名 \[WHERE 分组前条件限定] GROUP BY 分组字段名 \[HAVING 分组后条件过滤];
    >
    > （注意：分组之后，查询的字段为聚合函数和分组字段，查询其他字段无任何意义）

    （2）eg：

    * 查询男同学和女同学各自的数学平均分

    > select sex, avg(math) from stu group by sex;
    >
    > 注意：分组之后，查询的字段为聚合函数和分组字段，查询其他字段无任何意义
    >
    > select name, sex, avg(math) from stu group by sex; -- 这里查询name字段就没有任何意义

    * 查询男同学和女同学各自的数学平均分，以及各自人数

    > select sex, avg(math),count(\*) from stu group by sex;

    * 查询男同学和女同学各自的数学平均分，以及各自人数，要求：分数低于70分的不参与分组

    > select sex, avg(math),count(\*) from stu where math > 70 group by sex

    * 查询男同学和女同学各自的数学平均分，以及各自人数，要求：分数低于70分的不参与分组，分组之后人数大于2个的

    > select sex, avg(math),count(_) from stu where math > 70 group by sex having count(_) > 2;

    （3）总结

    > where 和 having 区别：&#x20;
    >
    > * 执行时机不一样：where 是分组之前进行限定，不满足where条件，则不参与分组，而having是分组之后对结果进行过滤
    > * 可判断的条件不一样：where 不能对聚合函数进行判断，having 可以
*   分页查询

    （1）语法：

    > SELECT 字段列表 FROM 表名 LIMIT 起始索引 , 查询条目数;

    （2）索引计算公式：

    > 起始索引 = (当前页码 - 1) \* 每页显示的条数

#### (4) DCL(Data Control Language):数据控制语言，用来定义数据库的访问权限和安全级别，及创建用户
