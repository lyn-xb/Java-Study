# 数据库设计

## 1  简介

* 数据库设计概念

  * 数据库设计就是根据业务系统的具体需求，结合我们所选用的DBMS，为这个业务系统构造出最优的数据存储模型。
  * 建立数据库中的`表结构`以及`表与表之间的关联关系`的过程。
  * 有哪些表？表里有哪些字段？表和表之间有什么关系？

* 数据库设计的步骤

  * 需求分析（数据是什么? 数据具有哪些属性? 数据与属性的特点是什么）

  * 逻辑分析（通过ER图对数据库进行逻辑建模，不需要考虑我们所选用的数据库管理系统）

    如下图就是ER(Entity/Relation)图：

    <img src="../.gitbook/assets/image-12.png" alt="image-12" style="zoom:80%;" />

  * 物理设计（根据数据库自身的特点把逻辑设计转换为物理设计）

  * 维护设计（1.对新的需求进行建表；2.表优化）

* 表关系

  * 一对一

    * 如：`用户` 和 `用户详情`
    * 一对一关系多用于表拆分，将一个实体中经常使用的字段放一张表，不经常使用的字段放另一张表，用于提升查询性能

  * 一对多

    * 如：`部门` 和 `员工`
  * 多对多

    * 如：`商品` 和 `订单`

    * 一个商品对应多个订单，一个订单包含多个商品


## 2 表关系(一对多)

* 一对多

  * 如：`部门` 和 `员工`
  * 一个部门对应多个员工，一个员工对应一个部门。

* 实现方式

  ​    在多的一方建立外键，指向一的一方的主键

* 案例

  建表语句如下：

  ```sql
  -- 删除表
  DROP TABLE IF EXISTS tb_emp;
  DROP TABLE IF EXISTS tb_dept;
  
  -- 部门表
  CREATE TABLE tb_dept(
  	id int primary key auto_increment,
  	dep_name varchar(20),
  	addr varchar(20)
  );
  -- 员工表 
  CREATE TABLE tb_emp(
  	id int primary key auto_increment,
  	name varchar(20),
  	age int,
  	dep_id int,
  
  	-- 添加外键 dep_id,关联 dept 表的id主键
  	CONSTRAINT fk_emp_dept FOREIGN KEY(dep_id) REFERENCES tb_dept(id)	
  );
  ```
  

## 2  表关系(多对多)

* 多对多

  * 如：`商品` 和 `订单`
  * 一个商品对应多个订单，一个订单包含多个商品

* 实现方式

  ​    建立第三张中间表，中间表至少包含两个外键，分别关联两方主键

* 案例

  我们以 `订单表` 和 `商品表` 举例：

  <img src="../.gitbook/assets/image-20210724134735939.png" alt="image-20210724134735939" style="zoom:70%;" />

  经过分析发现，订单表和商品表都属于多的一方，此时需要创建一个中间表，在中间表中添加订单表的外键和商品表的外键指向两张表的主键：

  <img src="../.gitbook/assets/image-20210724135054834.png" alt="image-20210724135054834" style="zoom:70%;" />

  建表语句如下：

  ```sql
  -- 删除表
  DROP TABLE IF EXISTS tb_order_goods;
  DROP TABLE IF EXISTS tb_order;
  DROP TABLE IF EXISTS tb_goods;
  
  -- 订单表
  CREATE TABLE tb_order(
  	id int primary key auto_increment,
  	payment double(10,2),
  	payment_type TINYINT,
  	status TINYINT
  );
  
  -- 商品表
  CREATE TABLE tb_goods(
  	id int primary key auto_increment,
  	title varchar(100),
  	price double(10,2)
  );
  
  -- 订单商品中间表
  CREATE TABLE tb_order_goods(
  	id int primary key auto_increment,
  	order_id int,
  	goods_id int,
  	count int
  );
  
  -- 建完表后，添加外键
  alter table tb_order_goods add CONSTRAINT fk_order_id FOREIGN key(order_id) REFERENCES tb_order(id);
  alter table tb_order_goods add CONSTRAINT fk_goods_id FOREIGN key(goods_id) REFERENCES tb_goods(id);
  ```

  查看表结构模型图：

  <img src="../.gitbook/assets/image-20210724140307910.png" alt="image-20210724140307910" style="zoom:80%;" />

## 2  表关系(一对一)

* 一对一

  * 如：`用户` 和 `用户详情`
  * 一对一关系多用于表拆分，将一个实体中经常使用的字段放一张表，不经常使用的字段放另一张表，用于提升`查询性能`

* 实现方式

  ​    在任意一方加入外键，关联另一方主键，并且设置外键为唯一(UNIQUE)

* 案例

  我们以 `用户表` 举例：

  <img src="../.gitbook/assets/image-20210724135346913.png" alt="image-20210724135346913" style="zoom:70%;" />

  而在真正使用过程中发现 id、photo、nickname、age、gender 字段比较常用，此时就可以将这张表查分成两张表。

​	<img src="../.gitbook/assets/image-20210724135649341.png" alt="image-20210724135649341" style="zoom:70%;" />

​	建表语句如下：

```sql
create table tb_user_desc (
	id int primary key auto_increment,
	city varchar(20),
	edu varchar(10),
	income int,
	status char(2),
	des varchar(100)
);

create table tb_user (
	id int primary key auto_increment,
	photo varchar(100),
	nickname varchar(50),
	age int,
	gender char(1),
	desc_id int unique,
	-- 添加外键
	CONSTRAINT fk_user_desc FOREIGN KEY(desc_id) REFERENCES tb_user_desc(id)	
);
```

​	查看表结构模型图：

<img src="../.gitbook/assets/image-20210724141445785.png" alt="image-20210724141445785" style="zoom:80%;" />
