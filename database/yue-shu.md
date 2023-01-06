# 约束

## 1 概念

* 约束是作用于表中列上的规则，用于限制加入表的数据。例如：我们可以给id列加约束，让其值不能重复，不能为null值
* 约束的存在保证了数据库中数据的正确性、有效性和完整性，添加约束可以在添加数据的时候就限制不正确的数据，继而保障数据的完整性

## 2 分类

*   **非空约束： 关键字 NOT NULL**

    > 保证列中所有的数据不能有null值
*   **唯一约束：关键字 UNIQUE**

    > 保证列中所有数据各不相同
* **主键约束： 关键字 PRIMARY KEY**

> 主键是一行数据的唯一标识，要求非空且唯一。一般都会给每张表添加一个主键列用来唯一标识数据

*   **检查约束： 关键字 CHECK**

    > 保证列中的值满足某一条件
    >
    > 注意：MySQL不支持检查约束,可以在java代码中进行限制，一样也可以实现要求
*   **默认约束： 关键字 DEFAULT**

    > 保存数据时，未指定值则采用默认值。
*   **外键约束： 关键字 FOREIGN KEY**

    > 外键用来让两个表的数据之间建立链接，保证数据的一致性和完整性。 外键约束现在可能还不太好理解，后面我们会重点进行讲解。

### 2.1 非空约束

*   概念

    > 非空约束用于保证列中所有数据不能有NULL值
*   语法

    * 添加约束

    ```sql
    -- 创建表时添加非空约束 
    CREATE TABLE 表名( 
       列名 数据类型 NOT NULL, 
       …  
    );
    ```

    ```sql
    -- 建完表后添加非空约束
    ALTER TABLE 表名 MODIFY 字段名 数据类型 NOT NULL;
    ```

    * 删除约束

    ```sql
    ALTER TABLE 表名 MODIFY 字段名 数据类型;
    ```

### 2.2 唯一约束

*   概念

    > 唯一约束用于保证列中所有数据各不相同
* 语法
  *   添加约束

      ```sql
      -- 创建表时添加唯一约束
      -- 定义完列之后直接指定唯一约束
      CREATE TABLE 表名(
         列名 数据类型 UNIQUE [AUTO_INCREMENT],
         -- AUTO_INCREMENT: 当不指定值时自动增长
         …
      );
      -- 定义完所有列之后指定唯一约束,并给约束命名
      CREATE TABLE 表名(
         列名 数据类型,
         …
         [CONSTRAINT 约束名称] UNIQUE(列名)
      );
      ```

      ```sql
      -- 建完表后添加唯一约束
      ALTER TABLE 表名 MODIFY 字段名 数据类型 UNIQUE;
      ```
  *   删除约束

      ```sql
      ALTER TABLE 表名 DROP INDEX 字段名;
      ```

### 2.3 主键约束

*   概念

    > 主键是一行数据的唯一标识，要求非空且唯一，一张表只能有一个主键
* 语法
  *   添加约束

      ```sql
      -- 创建表时添加主键约束
      CREATE TABLE 表名(
         列名 数据类型 PRIMARY KEY [AUTO_INCREMENT],
         …
      );
      CREATE TABLE 表名(
         列名 数据类型,
         [CONSTRAINT 约束名称] PRIMARY KEY(列名)
      );
       
      ```

      ```sql
      -- 建完表后添加主键约束
      ALTER TABLE 表名 ADD PRIMARY KEY(字段名);
      ```
  *   删除约束

      ```sql
      ALTER TABLE 表名 DROP PRIMARY KEY;
      ```

### 2.4 默认约束

* 概念 保存数据时，未指定值则采用默认值（默认约束只有在不给值时才会采用默认值。如果给了null，那值就是null值）
* 语法
  *   添加约束

      ```sql
      -- 创建表时添加默认约束
      CREATE TABLE 表名(
         列名 数据类型 DEFAULT 默认值,
         …
      );
      ```

      ```sql
      -- 建完表后添加默认约束
      ALTER TABLE 表名 ALTER 列名 SET DEFAULT 默认值;
      ```
  *   删除约束

      ```sql
      ALTER TABLE 表名 ALTER 列名 DROP DEFAULT;
      ```

### 2.4 外键约束

#### 2.4.1 概述

外键用来让两个表的数据之间建立链接，保证数据的一致性和完整性。

#### 2.4.2 语法

* 添加外键约束

```sql
-- 创建表时添加外键约束
CREATE TABLE 表名(
   列名 数据类型,
   …
   [CONSTRAINT] [外键名称] FOREIGN KEY(外键列名) REFERENCES 主表(主表列名)
);
-- 建完表后添加外键约束
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名称) REFERENCES 主表名称(主表列名称);
```

* 删除外键约束

```sql
ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;
```

#### 2.4.2 eg

根据上述语法创建员工表和部门表，并添加上外键约束：

```sql
-- 删除表
DROP TABLE IF EXISTS emp;
DROP TABLE IF EXISTS dept;
-- 部门表
CREATE TABLE dept(
  id int primary key auto_increment,
  dep_name varchar(20),
  addr varchar(20)
);
-- 员工表
CREATE TABLE emp(
  id int primary key auto_increment,
  name varchar(20),
  age int,
  dep_id int,
  -- 添加外键 dep_id,关联 dept 表的id主键
  CONSTRAINT fk_emp_dept FOREIGN KEY(dep_id) REFERENCES dept(id)  
);
```

添加数据

```sql
-- 添加 2 个部门
insert into dept(dep_name,addr) values
('研发部','广州'),('销售部', '深圳');
-- 添加员工,dep_id 表示员工所在的部门
INSERT INTO emp (NAME, age, dep_id) VALUES
('张三', 20, 1),
('李四', 20, 1),
('王五', 20, 1),
('赵六', 20, 2),
('孙七', 22, 2),
('周八', 18, 2);
```

此时删除 `研发部`这条数据，会发现无法删除。 删除外键

```sql
alter table emp drop FOREIGN key fk_emp_dept;
```

重新添加外键

```sql
alter table emp add CONSTRAINT fk_emp_dept FOREIGN key(dep_id) REFERENCES dept(id);
```
