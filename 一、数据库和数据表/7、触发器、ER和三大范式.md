# 一、触发器

- **它与表有关**，**当表上的特定事件（insert，update，delete）发生时**，**触发器会自动执行**。

```sql

-- 创建触发器
CREATE TRIGGER 触发器名称 
BEFORE|AFTER   -- 触发器类型
INSERT|UPDATE|DELETE    -- 触发器事件
ON 表名 
FOR EACH ROW -- 触发器的作用范围
BEGIN
-- 触发器执行的操作
END;

-- 删除触发器

DROP TRIGGER IF EXISTS 触发器名字;

```

- before / after ： 表示触发器的类型，分别表示发生前/发生后执行
- insert / update / delete ： 表示触发器的事件类型，分别表示插入 / 更新 / 删除操作
- on 表名称 ： 为触发器所在的表名
- for each row ： 表示触发器作用的范围，**即每一行记录都会触发该触发器**
- begin 和 end之间是触发器执行的操作，可以是一条或者多条SQL语句

> 触发器是自动执行的，无需手动调用，

```sql
-- 创建两张表
create table table1(
    id int,
    name varchar(20) character set utf8
)charset = utf8;

create table table2(
    id int,
    name varchar(20) character set utf8
)charset = utf8;

```

```sql
-- 创建一个触发器，当向表中插入一条记录时，自动向另一个表中插入一条记录
create trigger insert_trigger_1
after insert on table1
    for each row
    begin
        -- 触发器的具体事件
        insert into table2(id,name) values (NEW.id,New.name);
    end;

```







# 二、ER图

- 实体：一类数据对象的个体

- 数据库设计：将实体和实体之间的关系进行**规划和结构化**的过程。

- 实体关系的描述：称为**关系模式**，关系模式通常使用二维表的形式表示

  ![image-20250518205035192](C:\Users\LEGION\AppData\Roaming\Typora\typora-user-images\image-20250518205035192.png)

- 例如：

![image-20250518205056650](C:\Users\LEGION\AppData\Roaming\Typora\typora-user-images\image-20250518205056650.png)

# 三、三大范式

[推荐文章](https://www.cnblogs.com/linjiqin/archive/2012/04/01/2428695.html)

## 1、第一范式1NF

- 是最基本范式，**确保每列保存原子性**，也就是每列不可再分。

![image-20250518213030357](C:\Users\LEGION\AppData\Roaming\Typora\typora-user-images\image-20250518213030357.png)

## 2、第二范式2NF

- 第二范式是建立在第一范式基础上的，另外要求所有**非主键字段完全依赖主键**，**不能产生部分依赖** 。



![image-20250518213448403](C:\Users\LEGION\AppData\Roaming\Typora\typora-user-images\image-20250518213448403.png)

## 3、第三范式3NF

- 建立在第二范式基础上的，**非主键字段不能传递依赖于主键字段**。（**不要产生传递依赖，比如A依赖B，B依赖C，A就间接依赖C**） 

![image-20250518214441784](C:\Users\LEGION\AppData\Roaming\Typora\typora-user-images\image-20250518214441784.png)

第一范式：有主键，具有原子性，字段不可分割 。  

第二范式：完全依赖，没有部分依赖 。  

第三范式：没有传递依赖 。
