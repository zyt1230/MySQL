# 一、数据的插入

## 1、insert

```sql
-- 插入一条
INSERT INTO 表名(字段名1,字段名2,....,字段名n) VALUES(字段值1,字段值2,.....,字段值n);

-- 插入多条
INSERT INTO 表名(字段名1,字段2,....字段名n)VALUES(字段值1,字段值1,...字段值n),(字段值1,字段值1,...字段值n),...,(字段值1,字段值1,...字段值n);

-- 字段的插入，无字段，也可以写，但是按创建的数据表字段来有序写。
INSERT INTO 表名 VALUES 
(值1, 值2, ...),
(值1, 值2, ...),
...;
```

> 表名里的字段名是一一对应字段值的。要按顺序写。
> 
> 第一个是：输入一条字段值
> 
> 第二个是：输入多个字段值



## 2、了解where条件

| 运算符             | 说明                                               |
| --------------- | ------------------------------------------------ |
| =               | 等于                                               |
| >               | 大于                                               |
| <               | 小于                                               |
| <>或!=           | 不等于                                              |
| between ... and | 2个值之间 ，等同于>=and<=                                |
| is null         | 为null（is not null，不为空）                           |
| and             | 并且                                               |
| or              | 或者                                               |
| in (字段)         | 包含，相当于多个or （not in不在这个范围内）                       |
| not             | not可以取非，主要用于is或in中                               |
| like            | **模糊查询**，支持%或下划线匹配，%匹配**任意个字符**，下划线只能**匹配一个字符**。 |
| &&              | 等同于and                                           |
|                 | \|                                               |
| >=              | 大于等于                                             |
| <=              | 小于等于                                             |

# 二、数据的删除

## 1、delete

```sql
DELETE FROM 表名 [WHERE 删除条件（也就是要删除的位置）]
```

# 三、数据的修改

## 1、update

```sql
UPDATE 数据表名字 SET 字段1=修改的数据,...,字段n=修改的数据;
```

# 四、数据的清空

## 1、delete

```sql
delete from 表名字;
```

## 2、truncate

```sql
TRUNCATE [TABLE] 表名;#[TABLE]可有可无
```

> **DELETE 和 TRUNCATE 的区别**
> 
> 1. 一个清空，一个删除某一个地方数据
> 2. 如果表中有自增长列，TRUNCATE会重置自增长的计数器，但DELETE语句不会。
> 3. TRUNCATE 语句执行后，数据无法恢复，而DELETE可以用事务回滚恢复。



# 五、DQL---select的使用【重点】

## 1、条件查询select的格式和基本用法

- 条件查询就是按条件查询数据 。

```sql
select 字段名1 AS 别名 [,字段名2 AS 别名....] FROM 表名 WHERE 查询条件;
```

- as 可以省略

- all是全部的记录----就是*符号

- 字段可以使用数学表达式

- **别名**是**中文**要用**引号**括起来
  
  

### 1.1、字段的别名

```sql
select number as "数字" ,name as "姓名",score as "成绩" ,time as "时间" from course;
```

![4598c194-7009-4649-a36a-b79cf1f96549](file:///C:/Users/LEGION/Pictures/4598c194-7009-4649-a36a-b79cf1f96549.png)

![03446f50-b688-416d-b1f2-ad389f7e8f8a](file:///C:/Users/LEGION/Pictures/03446f50-b688-416d-b1f2-ad389f7e8f8a.png)



### 1.2、表的别名

```sql
select c.name ,c.number,c.score from course c;
```

![90cf3497-6c07-4a9c-98f8-1aee028aca48](file:///C:/Users/LEGION/Pictures/90cf3497-6c07-4a9c-98f8-1aee028aca48.png)

### 1.3、数学计算的字段

```sql
select name as n,number* 10 from course ;
```

![5d51ffe9-a220-48fc-8b0e-e1ce516aa45b](file:///C:/Users/LEGION/Pictures/5d51ffe9-a220-48fc-8b0e-e1ce516aa45b.png)

### 1.4、条件查询的使用例子

- 这里只使用 like 和in 举例

```sql
# 从name 里查找包含 e 的记录

SELECT name FROM course WHERE name LIKE "%e%";
# 从name 里查找 包含 e开头的记录

SELECT name FROM course WHERE name LIKE "e%";
# 从name 里查找 e结尾的记录

SELECT name FROM course WHERE name LIKE "%e";
# 从name 里查找只有4个字符的记录
SELECT name FROM course WHERE name LIKE "____";

# 从name 里查找e开头后面还有3个字符的记录
SELECT name FROM course WHERE name LIKE "e___";
```





```sql
# 查询 `number` 为 1，2的课
select * from course where number in (1,2);
```



![3df83aaf-1b08-4a34-9993-f44aaf7eacfb](file:///C:/Users/LEGION/Pictures/3df83aaf-1b08-4a34-9993-f44aaf7eacfb.png)

### 1.5、分组GROUP BY

```sql
# 提供的数据sql
CREATE TABLE IF NOT EXISTS student(
    no BIGINT(20) NOT NULL AUTO_INCREMENT PRIMARY KEY COMMENT '学号',
    name VARCHAR(20) NOT NULL COMMENT '姓名',
    sex VARCHAR(2) DEFAULT '男' COMMENT '性别', 
    age INT(3) DEFAULT 0 COMMENT '年龄',
    score DOUBLE(5,2) COMMENT '成绩'
)ENGINE = InnoDB CHARSET = UTF8 COMMENT = '学生表';
#插入数据
INSERT INTO student(no,name,sex,age,score) VALUES(DEFAULT ,'张三','男',20,59);
INSERT INTO student(no,name,sex,age,score) VALUES(DEFAULT ,'李四','女',19,62);
INSERT INTO student(no,name,sex,age,score) VALUES(DEFAULT ,'王五','其他',28,81);
INSERT INTO student(no,name,sex,age,score) VALUES(DEFAULT ,'龙华','男',22,75);
INSERT INTO student(no,name,sex,age,score) VALUES(DEFAULT ,'金凤','女',18,80);
INSERT INTO student(no,name,sex,age,score) VALUES(DEFAULT ,'张华','其他',27,88);
INSERT INTO student(no,name,sex,age,score) VALUES(DEFAULT ,'李刚','男',30,88);
INSERT INTO student(no,name,sex,age,score) VALUES(DEFAULT ,'潘玉明','女',28,81);
INSERT INTO student(no,name,sex,age,score) VALUES(DEFAULT ,'凤飞飞','其他',32,90);
```



- 可能出现这个问题 

```sql
mysql> SELECT * FROM student WHERE score >80 GROUP BY sex;
ERROR 1055 (42000): Expression #1 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'study.student.no' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by
```

- 使用 `SET sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));`解决 。
  
  

### 1.6、聚合函数

#### COUNT()

- 提供的数据还是上面GROUP BY那个

- 统计满足该条件的数据总条数

```sql
SELECT COUNT(*) FROM student WHERE score>80;

--结果
+----------+
| COUNT(*) |
+----------+
|        5 |
+----------+
```



```sql
SELECT COUNT(*) total FROM student WHERE score>80;


-- 结果
+-------+
| total |
+-------+
|     5 |
+-------+
```

#### SUM()

- 提供的数据还是上面GROUP BY那个

- 只能用于数值类型的字段或者表达式，计算该满足条件的字段值的总和

```sql
SELECT SUM(字段) 【别名】 FROM student WHERE 条件;


select count(*) total,sum(score) sum from student where score > 60;
+-------+--------+
| total | sum    |
+-------+--------+
|     8 | 645.00 |
+-------+--------+
```

#### AVG()

- 只能用于数值类型的字段或者表达式，计算该满足条件的字段值的平均值

```sql
SELECT AVG(字段) 【别名】 FROM 数据表;
```

#### MAX()

- 只能用于数值类型的字段或者表达式，计算满足条件的字段值的最大值

```sql
SELECT MAX(字段) 【别名】 FROM 数据表; 
```

#### MIN()

- 只能用于数值类型的字段或表达式，计算满足条件的字段值的最小值

```sql
SELECT MIN(字段) 【别名】 FROM 数据表;
```

### 1.7、分组查询结果筛选

- **HAVING**：对**分组后的结果**进行筛选

```sql
select sum(score)  from student GROUP BY sex HAVING sum(score)>222;
+------------+
| sum(score) |
+------------+
|     223.00 |
|     259.00 |
+------------+
```

### 1.8、排序

- ORDER BY  字段名字 ASC/DESC
  
  - ASC  :是升序
  
  - DESC : 是降序

```sql
-- 按成绩升序，只打印成绩

select score from student order by score asc;
+-------+
| score |
+-------+
| 59.00 |
| 62.00 |
| 75.00 |
| 80.00 |
| 81.00 |
| 81.00 |
| 88.00 |
| 88.00 |
| 90.00 |
+-------+
-- 按成绩降序，只打印成绩
select score from student order by score desc;
+-------+
| score |
+-------+
| 90.00 |
| 88.00 |
| 88.00 |
| 81.00 |
| 81.00 |
| 80.00 |
| 75.00 |
| 62.00 |
| 59.00 |
+-------+
```

### 1.9、分页

- **LIMIT**：指定返回结果的最大数量。

- **OFFSET**：指定从结果集中跳过的记录数。



```sql
SELECT * FROM table_name
LIMIT 10 OFFSET 20;
```

- 这个查询将从`table_name`表中返回第3页的数据（每页10条记录）。
  
  - 即从第21条记录开始，返回10条记录。
