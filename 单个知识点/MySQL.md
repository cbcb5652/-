# MySQL

## 基础

> 主键值不能修改，也不允许复用(不能将已经删除的主键赋值给新数据行的主键)。

==SQL支持以下三种注释:==

```sql
# 注释
select *
From mytable; -- 注释
/*	注释1
	注释2	*/
```

==数据库创建语句==

```sql
create database test;
use test;
```

## 创建表

```sql
create table mytable(
	# int 类型 不为空 自增
    id int not null auto_increment,
    # int类型，不可为空，默认值为1，不为空
    col1 INT NOT NULL DEFAULT 1,
    # 边长字符串类型，最长为45个字符，可以为空
    clo2 VARCHAR(45) NULL,
    # 日期类型，可为空
    col3 DATE NULL,
    # 设置主键为id
    PRIMARY KEY(`id`)
)
```

## 修改表

**添加列**

```sql
alter table mytable
add col char(20);
```

**删除列**

```sql
alter table mytable
drop column col;
```

**删除表**

```sql
drop table mytable;
```

## 插入

**普通插入**

```sql
insert into mytable(col1,col2)
values(val1,val2);
```

**插入检索出来的数据**

```sql
insert into mytabl1(col1,col2)
select col1,col2
from mytable2;
```

**将一个表的内容插入到一个新表**

```sql
create table newtable as
select * from mytable;
```

## 更新

```sql
update mytable
set col = val
where id = 1;
```

## 删除

```sql
delete from mytable
where id = 1;
```

TRUNCATE TABLE可以清空表，也就是删除所有行

```sql
TRUNCATE TABLE mytable;
```

使用更新和删除操作时，一定要用where子句，不然会把整张表的数据都破坏。可以先用select语句进行测试，防止错误删除。

## 查询

**DISTINCT**

相同的值只会出现一次。它作用于所有列，也就是说所有列的值都相同才算相同。

```sql
select distinct col1,col2 form mytable;
```

**LIMIT**

限制返回的行数。可以有两个参数，第一个参数为起始行，从 0 开始；第二个参数为返回的总行数。

返回前5行：

```sql
select * from mytable limit 5;
select * from mytable limit 0,5;
```

返回第3-5行

```sql
select * from mytable limit 2,3;
```

## 排序

- **ASC:** 升序 (默认)
- **DESC:** 降序

可以按多个列进行排序，并且为每个列指定不同的排序方式

```sql
select * from mytable order by col1 desc,col2 asc;
```

## 过滤

不进行过滤的数据非常大，导致通过网络传输了多余的数据，从而浪费了网络带宽。因此尽量使用 SQL 语句来过滤不必要的数据，而不是传输所有的数据到客户端中然后由客户端进行过滤。

```sql
select * from mytable where col is null;
```

下表显示了 where 子句可用的操作符

| 操作符  | 说明         |
| ------- | ------------ |
| =       | 等于         |
| <       | 小于         |
| >       | 大于         |
| <> !=   | 不等于       |
| <= !>   | 小于等于     |
| >= !<   | 大于等于     |
| BETWEEN | 在两个值之间 |
| IS NULL | 为 NULL 值   |

应该注意到，NULL 与 0、空字符串都不同。

**AND 和 OR** 用于连接多个过滤条件。优先处理 AND，当一个过滤表达式涉及到多个 AND 和 OR 时，可以使用 () 来决定优先级，使得优先级关系更清晰。

**IN** 操作符用于匹配一组值，其后也可以接一个 SELECT 子句，从而匹配子查询得到的一组值。

**NOT** 操作符用于否定一个条件。

## 通配符

通配符也是用在过滤语句中的，但它只能用于文本字段。

- % 匹配 >=0 个任意字符
- _ 匹配 ==1 个字符
- [] 可以匹配集合内的字符,例如[ab] 将匹配字符a或者b . 用^ 可以表示否定,也就是不匹配集合内的字符.

使用Like来进行通配符的匹配

```sql
select * from mytable
where col like `[^AB]%`;		-- 不以A和B开头的任意文本
```

不要滥用通配符，通配符位于开头处匹配会非常慢。

## 计算字段

在数据库服务器上完成数据的转换和格式化的工作往往比客户端上快得多，并且转换和格式化后的数据量更少的话可以减少网络通信量。

计算字段通常需要使用 **AS** 来取别名，否则输出的时候字段名为计算表达式。

```sql
select col1 * col1 as newcol from mytable;
```

concat() 用于连接两个字段. 许多数据库会使用空格把一个值填充为列宽,因此连接的结果会出现一些不必要的空格.使用TRIM()可以取出首尾空格.

```sql
SELECT CONCAT(TRIM(col1), '(', TRIM(col2), ')') AS concat_col
FROM mytable;
```

## 函数

各个 DBMS 的函数都是不相同的，因此不可移植，以下主要是 MySQL 的函数。

**汇总**

| 函 数   | 说 明            |
| ------- | ---------------- |
| AVG()   | 返回某列的平均值 |
| COUNT() | 返回某列的行数   |
| MAX()   | 返回某列的最大值 |
| MIN()   | 返回某列的最小值 |
| SUM()   | 返回某列值之和   |

AVG() 会忽略 NULL 行。

使用 DISTINCT 可以汇总不同的值。

```sql
select AVG(DISTINCE col1) as avg_col from mytable;
```

**文本处理**

| 函数      | 说明           |
| --------- | -------------- |
| LEFT()    | 左边的字符     |
| RIGHT()   | 右边的字符     |
| LOWER()   | 转换为小写字符 |
| UPPER()   | 转换为大写字符 |
| LTRIM()   | 去除左边的空格 |
| RTRIM()   | 去除右边的空格 |
| LENGTH()  | 长度           |
| SOUNDEX() | 转换为语音值   |

其中， **SOUNDEX()** 可以将一个字符串转换为描述其语音表示的字母数字模式。

```sql
select * from mytable where soundex(col1) = soundex('apple');
```

**日期和时间处理**

- 日期格式：YYYY-MM-DD
- 时间格式：HH:MM:SS

| 函 数         | 说 明                          |
| ------------- | ------------------------------ |
| ADDDATE()     | 增加一个日期（天、周等）       |
| ADDTIME()     | 增加一个时间（时、分等）       |
| CURDATE()     | 返回当前日期                   |
| CURTIME()     | 返回当前时间                   |
| DATE()        | 返回日期时间的日期部分         |
| DATEDIFF()    | 计算两个日期之差               |
| DATE_ADD()    | 高度灵活的日期运算函数         |
| DATE_FORMAT() | 返回一个格式化的日期或时间串   |
| DAY()         | 返回一个日期的天数部分         |
| DAYOFWEEK()   | 对于一个日期，返回对应的星期几 |
| HOUR()        | 返回一个时间的小时部分         |
| MINUTE()      | 返回一个时间的分钟部分         |
| MONTH()       | 返回一个日期的月份部分         |
| NOW()         | 返回当前日期和时间             |
| SECOND()      | 返回一个时间的秒部分           |
| TIME()        | 返回一个日期时间的时间部分     |
| YEAR()        | 返回一个日期的年份部分         |

```sql
select now();

--> 2018-4-14 20:25:11
```

把具有相同的数据值的行放在同一组中.

可以对同一分组数据使用汇总函数进行处理,例如求分组数据的平均值等.

指定的分组字段除了能按该字段进行分组,也会自动按该字段进行排序.

```sql
SELECT col, COUNT(*) AS num
FROM mytable
GROUP BY col;
```

> 数据库数据如下:

![image-20200728112308465](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200728112308465.png)

结果:

![image-20200728112943493](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200728112943493.png)



GROUP BY 自动按分组字段进行排序，ORDER BY 也可以按汇总字段来进行排序。

可以按照num大小降序排列.(后面可加 desc asc)

```sql
select col1 ,count(*) as num
from mytable
group by col1
order by num ;
```

WHERE 过滤行，HAVING 过滤分组，行过滤应当先于分组过滤。

```sql
select col1,count(*) as num
from mytable 
where col1 > 2 
group by col1
having num >= 2
```

> 分组规定：
>
> - GROUP BY 子句出现在 WHERE 子句之后，ORDER BY 子句之前；
> - 除了汇总字段外，SELECT 语句中的每一字段都必须在 GROUP BY 子句中给出；
> - NULL 的行会单独分为一组；
> - 大多数 SQL 实现不支持 GROUP BY 列具有可变长度的数据类型。

## 子查询

子查询中只能返回一个字段的数据

可以将子查询的结果作为where语句的过滤条件

```sql
select * from mytable1 where col1 in (select col2 from mytables2);
```

下面的语句可以检索出客户的订单数量，子查询语句会对第一个查询检索出的每个客户执行一次：

```sql
select cust_name,(select count(*) 
                 from Orders
                 where Order.cust_id = Customers.cust_id
                 as Orders_num)
from Customers
order by cust_name;
```

## 连接

连接用于连接多个表，使用 JOIN 关键字，并且条件语句使用 ON 而不是 WHERE。

连接可以替换子查询，并且比子查询的效率一般会更快。

可以用 AS 给列名、计算字段和表名取别名，给表名取别名是为了简化 SQL 语句以及连接相同表。

### 内连接

内连接又称为等值连接,使用INNER JOIN 关键字

```sql
select A.value ,B.value
from tablea as A inner join tableb as B
on A.key = B.key
```

可以不明确使用 INNER JOIN，而使用普通查询并在 WHERE 中将两个表中要连接的列用等值方法连接起来。

```sql
select A.value ,B.value from tablea as A,tableb as B where A.key = B.key
```

### 自连接

自连接可以看成内连接的一种，只是连接的表是自身而已。

一张员工表，包含员工姓名和员工所属部门，要找出与 Jim 处在同一部门的所有员工姓名。

### 子查询版本

```sql
select name from employee where department= (
												select department from employee where name = "tom"
											)
```

### 自连接版本

```sql
SELECT e1.name FROM employee AS e1 INNER JOIN employee AS e2
ON e1.department = e2.department
	AND e2.name = "tom"
```

**自然连接**

自然连接是把同名列通过等值测试连接起来的，同名列可以有多个。

内连接和自然连接的区别：内连接提供连接的列，而自然连接自动连接所有同名列。

```sql
select A.value ,B.value from table AS A NATURAL JOIN tableb AS B
```

**外连接**

外连接保留了没有关联的那些行。分为**左外连接**，**右外连接**以及**全外连接**，左外连接就是保留左表没有关联的行。

检索所有顾客的订单信息，包括还没有订单信息的顾客。

> 使用左连接之后,会把左表未满足ON的条件的数据列出来 即使对应的cust_name 为空

```sql
SELECT Customers.cust_id,Customer.cust_name ,Orders.order_id 
FROM Customers LEFT OUTER JOIN Orders
ON Customers.cust_id = Orders.cust_id
```



![image-20200728120007354](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200728120007354.png)

## 组合查询

使用 **UNION** 来组合两个查询，如果第一个查询返回 M 行，第二个查询返回 N 行，那么组合查询的结果一般为 M+N 行。

- 每个查询必须包含相同的列、表达式和聚集函数。
- 默认会去除相同行，如果需要保留相同行，使用 UNION ALL。
- 只能包含一个 ORDER BY 子句，并且必须位于语句的最后。

```sql
select col from mytable where col = 1
union
select col from mytable where col = 2;
```

## 视图

视图是虚拟的表，本身不包含数据，也就不能对其进行索引操作。

对视图的操作和对普通表的操作一样。

**视图具有如下好处：**

- 简化复杂的SQL操作,比如复杂的连接
- 只使用实际表的一部分数据
- 通过值给用户访问视图的权限,保证数据的安全性
- 更改数据格式和表示

```sql
create view myview AS
select Concat(col1,col2) as concat_col,col3 * col4 as compute_col
from mytable
where col5 = val;
```

## 存储过程

存储过程可以看成是对

使用存储过程的好处：

- 代码封装,保证了一定的安全性
- 代码复用
- 由于是预先编译,因此具有很高的性能

命令行中创建存储过程需要自定义分隔符，因为命令行是以 ; 为结束符，而存储过程中也包含了分号，因此会错误把这部分分号当成是结束符，造成语法错误。

包含 in、out 和 inout 三种参数。

给变量赋值都需要用 select into 语句。

每次只能给一个变量赋值，不支持集合的操作。

```sql
delimiter

create procedure myprocedure(out ret int)
	begin
		declare y int;
		select sum(col1)
		from mytable
		into y ;
		select y*y into ret;
	end
delimiter;
```

## 游标

在存储过程中使用游标可以对一个结果集进行移动遍历。

游标主要用于交互式应用，其中用户需要对数据集中的任意行进行浏览和修改。

使用游标的四个步骤：

1. 声明游标，这个过程没有实际检索出数据；
2. 打开游标；
3. 取出数据；
4. 关闭游标；

```sql
delimiter //
create procedure myprocedure(out ret int)
    begin
        declare done boolean default 0;

        declare mycursor cursor for
        select col1 from mytable;
        # 定义了一个 continue handler，当 sqlstate '02000' 这个条件出现时，会执行 set done = 1
        declare continue handler for sqlstate '02000' set done = 1;

        open mycursor;

        repeat
            fetch mycursor into ret;
            select ret;
        until done end repeat;

        close mycursor;
    end //
 delimiter ;
```



## 触发器

触发器会在某个表执行以下语句时而自动执行：**DELETE**、**INSERT**、**UPDATE**。

> 触发器必须指定在语句执行之前还是之后自动执行，之前执行使用 BEFORE 关键字，之后执行使用 AFTER 关键字。BEFORE 用于数据验证和净化，AFTER 用于审计跟踪，将修改记录到另外一张表中。

INSERT 触发器包含一个名为NEW的虚拟表

```sql
CREATE TRIGGER mytrigger AFTER INSERT ON mytable
FOR EACH ROW SELECT NEW.col into @result;

SELECT @result; -- 获取结果
```

DELETE 触发器包含一个名为OLD的虚拟表,并且是只读的.

UPDATE 触发器包含一个名为NEW 和一个名为OLD的虚拟表,其中NEW是可以被修改的,而OLD是只读的

MySQL 不允许在触发器中使用CALL语句.也就是说不能调用存储过程.

## 事务管理

**基本语句**

- 事务（transaction）指一组 SQL 语句；
- 回退（rollback）指撤销指定 SQL 语句的过程；
- 提交（commit）指将未存储的 SQL 语句结果写入数据库表；
- 保留点（savepoint）指事务处理中设置的临时占位符（placeholder），你可以对它发布回退（与回退整个事务处理不同）。

不能回退 SELECT 语句，回退 SELECT 语句也没意义；也不能回退 CREATE 和 DROP 语句。

> MySQL 的事务提交默认是隐式提交，每执行一条语句就把这条语句当成一个事务然后进行提交。当出现 START TRANSACTION 语句时，会关闭隐式提交；当 COMMIT 或 ROLLBACK 语句执行后，事务会自动关闭，重新恢复隐式提交。

设置 autocommit 为 0 可以取消自动提交；autocommit 标记是针对每个连接而不是针对服务器的。

如果没有设置保留点，ROLLBACK 会回退到 START TRANSACTION 语句处；如果设置了保留点，并且在 ROLLBACK 中指定该保留点，则会回退到该保留点。

```sql
START TRANSACTION
// ...
SAVEPOINT delete1
// ...
ROLLBACK TO delete1
// ...
COMMIT
```

## 字符集

**基本术语**

- 字符集为字母和符号的集合
- 编码为某个字符集成员的内部表示
- 校对字符集指定如何比较,主要用于排序和分组.

除了给表指定字符集和校对外，也可以给列指定：

```sql
CREATE TABLE mytable
(col VARCHAR(10) CHARACTER SET latin COLLATE latin1_general_ci )
DEFAULT CHARACTER SET hebrew COLLATE hebrew_general_ci;
```

可以在排序,分组时指定校对:

```sql
select * from mytable order by col collate latin1_general_ci;
```

## 权限管理

MySQL的账户信息保存在mysql这个数据库中.

```sql
USE mysql;
SELECT user FROM user;
```

**创建账户**

新创建的账户没有任何权限

```sql
create user myuser IDENTIFIED BY 'mypassword';
```

**修改账户名**

```sql
RENAME USER myuser TO newuser;
```

**删除账户**

```sq;
DROP USER myuser;
```

**查看权限**

```sql
SHOW GRANTS FOR myuser;
```

**授予权限**

账户用username@host 的形式定义,username@% 使用的是默认主机名

```sql
GRANT SELECT,INSERT ON mydatabase.* TO myuser;
```

**删除权限**

GRANT 和 REVOKE 可在几个层次上控制访问权限：

- 整个服务器，使用 GRANT ALL 和 REVOKE ALL；
- 整个数据库，使用 ON database.*；
- 特定的表，使用 ON database.table；
- 特定的列；
- 特定的存储过程。

```sql
REVOKE SELECT ,INSERT ON mydatabase.* FROM mysuer;
```

**更改密码**

必须使用Password()函数进行加密

```sql
SET PASSWORD FOR myuser = Password('new_password')
```

# 触发器

> 使用触发器的时候会根据所设置的状态!
>
> 当改变设置触发器的表的时候,就会去根据触发器调用其他设置好需要更改的表去操作对应的数据

例如:

- 当一个粉丝关注了一个博主(博主对应的表有了响应的字段)
- 博主统计粉丝的那个表也会相应的+1 
- 而且在粉丝对应的那个用户信息中,也会增加一条数据  --->  新关注了一个博主

























