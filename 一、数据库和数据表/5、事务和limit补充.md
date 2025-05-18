# 一、事务【都是重点】

## 1、了解

- 一个事务其实就是一个**完整的业务逻辑**。
- **要么同时发生，要么同时结束**。

- 是一个**最小的工作单元**。
- **不可再分**。

[看这个视频，黑马的，4分钟多点就能理解到](https://www.bilibili.com/video/BV1Kr4y1i7ru?spm_id_from=333.788.videopod.episodes&vd_source=c6d25b8c148a15b1c45b8da5d613a042&p=51)

> 可以理解成：
>
> **开始事务-----如果中间抛出异常，进行回滚事务---->提交事务**

## 2、事务的四大特性(ACID)

- 1、**原子性**：事务是不可分割的最小操作单元，要么全部成功，要么全部失败。

- 2、**一致性**：事务完成时，必须使所有的数据都保持一致状态。
  - 当转账操作完成时，所有账户的总金额应该保持不变，此时数据处于一致性状态；如果总金额发生了改变，说明数据处于非一致性状态。

- 3、**隔离性**：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行。
  - 比如张三和李四同时都在进行转账操作，但彼此都不影响对方。

- 4、**持久性**：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。

## 3、事务的操作

- 创建账户表

```sql
CREATE TABLE account(
	id INT primary key AUTO_INCREMENT comment "主键",
	name VARCHAR(10) COMMENT "姓名",
	money INT COMMENT "余额"
)COMMENT "账户表";
INSERT INTO account VALUES(null,"张三",2000),(null,"李四",2000);
```

------

#### 3.1、查看/设置事务的提交方式

```sql
SELECT @@autocommit;
SET @@autocommit = 0;--设置为0是手动提交，1是自动提交		
```



#### 3.2、提交事务

```sql
COMMIT;
```



#### 3.3、回滚事务

```sql
ROLLBACK;
```



#### 3.4、事务的举例

```sql
CREATE TABLE account(
	id INT primary key AUTO_INCREMENT comment "主键",
	name VARCHAR(10) COMMENT "姓名",
	money INT COMMENT "余额"
)COMMENT "账户表";
INSERT INTO account VALUES(null,"张三",2000),(null,"李四",2000);

SELECT @@autocommit;
set @@autocommit=0;--设置为手动提交

-- 转账操作（张三给李四1000）
--1、张三的余额是多少
SELECT * FROM ACCOUNT WHERE name = "张三";
--2、将张三的余额减1000
UPDATE account SET money=money-1000 WHERE name = "张三";
lsdfjlsd
--3、将李四的余额加1000
UPDATE account SET money= money+1000 WHERE name = "李四";

--提交事务
COMMIT;

--回滚事务
ROLLBACK;

--恢复数据
UPDATE account SET money = 2000 WHERE name = "张三" or name = '李四';

```

```sql
START TRANSACTION;      -- 或 BEGIN
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;                -- 提交事务（持久化）
-- 或 ROLLBACK;        -- 回滚事务（撤销所有操作）
```



## 4、并发事务问题

1. **脏读（Dirty Read）**：
   - 一个事务读取了另一个事务未提交的更改数据。
   - 如果第二个事务回滚，那么第一个事务读取的数据将是无效的。
2. **不可重复读（Non-repeatable Read）**：
   - **在同一事务中，多次读取同一数据集合，但读取的结果不同。**
   - 这通常是由于其他事务在两次读取之间更改了数据。
3. **幻读（Phantom Read）**：
   - 在**同一事务**内，多次执行**相同条件的查询**时，由于其他事务的插入操作，导致前后查询结果**行数**不一致（出现了"幻影行"）。
   - 这通常是由于其他事务插入或删除了数据。

## 5、事务隔离级别

- **读未提交**：允许一个事务可以看到其他事务未提交的修改。
- **读已提交**：允许一个事务**只能**看到其他事务**已经提交的修改**，未提交的修改是不可见的。
- **可重复读**：确保如果在一个事务中**执行两次相同的 SELECT 语句**，都能得到相同的结果，**不管其他事务是否提交这些修改**。 （银行总账） 
- **串行化**：【序列化】 将一个事务与其他事务**完全地隔离**。



![image-20250518184610713](C:\Users\LEGION\AppData\Roaming\Typora\typora-user-images\image-20250518184610713.png)

-----

- **查看当前事务隔离级别**

```sql
SELECT @@TRANSACTION_ISOLATION;
```

- **设置隔离级别**
  - `SESSION`：**仅对当前会话有效**
  - `GLOBAL`：**对所有新创建的会话有效**
  - 如果不指定，**默认为SESSION**

```sql
SET [SESSION|GLOBAL] TRANSANCTION ISOLATION LEVEL {READ UNCOMMITTED|READ COMMITTED|REPEATABLE READ |SERIALIZABLE}
```

----

- 脏读
  - 创建2个终端。[看黑马视频做的](https://www.bilibili.com/video/BV1Kr4y1i7ru?spm_id_from=333.788.videopod.episodes&vd_source=c6d25b8c148a15b1c45b8da5d613a042&p=55)

```sql
--在终端1里做一下操作
CREATE TABLE account (
    id TINYINT(2) AUTO_INCREMENT PRIMARY KEY COMMENT "编号",
    name VARCHAR(4) NOT NULL COMMENT "姓名",
    money INT(4) NOT NULL COMMENT "钱"

)ENGINE = INNODB CHARSET=UTF8 COMMENT = "账户";

INSERT INTO account(id,name,money) VALUES(1,"张三",2000),(2,"李四",2000);

SELECT * FROM account;
#开始分终端操作
--在终端2
start transaction;
update account set money=money-1000 WHERE name = "张三";
--然后再次回到终端1
SELECT * FROM account;
会发现没有 money - 1000 ;#因为没有提交
--回到终端2，提交事务
COMMIT;
```

- 不可重复读取



```sql
-- 事务A
BEGIN;
SELECT balance FROM accounts WHERE id = 1; -- 第一次读取，值为1000
-- 此时事务B更新并提交了数据

SELECT balance FROM accounts WHERE id = 1; -- 第二次读取，值可能变为900（不可重复读）
COMMIT;
```

- 幻读

```sql
-- 事务A
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
BEGIN;
SELECT * FROM accounts WHERE balance > 1000; -- 第一次查询返回2条记录

-- 事务B在此期间执行并提交：
INSERT INTO accounts(id, balance) VALUES(3, 1500);
COMMIT;

SELECT * FROM accounts WHERE balance > 1000; -- 第二次查询仍然返回2条记录
-- 但如果执行：
SELECT COUNT(*) FROM accounts WHERE balance > 1000; -- 可能返回3（幻读）
COMMIT;
```





# 二、limit

## 1、limit startIndex ,length

- startIndex 是起始下标，也就是展示 **[startIndex+1,startIndex+5]** 

- length 是长度

```sql
-- 按照薪资降序，取出排名在前5名的员工？

select 
		ename,sal
	from
		emp
	order by 
		sal desc
	limit 0,5;
```



## 2、limit 数字

- 是指展示前“数字” 位 。

```sql
--按照薪资降序，取出排名在前5名的员工？
select 
		ename,sal
	from
		emp
	order by 
		sal desc
	limit 5; //取前5
	
	1、2点一样。 
```

## 3、公式：

- `pageNo`：当前页码（通常从1开始）。

- `pageSize`：每页显示的记录数。

- `(pageNo - 1) * pageSize` 计算的是当前页的第一条记录在所有记录中的位置。

```sql
--例如
#pageNo = 3（当前是第三页）
#pageSize = 10（每页显示10条记录）
#起始位置：(3 - 1) * 10 = 20（即从第20条记录开始）
#每页记录数：10
SELECT * FROM your_table
LIMIT 20, 10;
```

