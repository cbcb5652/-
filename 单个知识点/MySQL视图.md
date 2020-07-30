# MySQL视图

## 数据库视图简介

> **数据库视图**是虚拟表或逻辑表，它被定义为具有连接的SQL SELECT查询语句。因为数据库视图与数据库表类似，它由行和列组成，因此可以根据数据库表查询数据。大多数数据库管理系统(包括MySQL)允许您通过具有一些先决条件的数据库视图来更新基础表中的数据。

![image-20200729094644063](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200729094644063.png)

数据视图是动态的(它与物理模式无关)。当有关联的数据发生变化时，视图也反应了这些数据的变化。

### 数据库视图的优点：

- **数据库视图允许简化复杂查询：**数据库视图由许多基础的SQL语句组成，可以减少很多重复的复杂查询语句
- **数据库视图有助于限制用户对数据的访问：**可以利用视图将非敏感的数据仅显示给特定的用户(只能查看)
- **数据库视图提供额外的安全层：**数据库视图允许只创建只读视图，用户只能读视图，无法更新
- **数据库视图启用列计算：**可以创建一个列名为total的计算列，显示，某些列的乘积。(销售额)
- **数据库视图向后兼容：**有一天要删除并创建新的表，并且不希望更改影响到其他应用程序，可以创建对应的视图

### 数据库视图的缺点：

- **性能上：**数据库视图查询会比较慢，特别是视图基于其他视图创建的
- **表依赖关系：**将根据数据库基础表创建一个视图。每当更改与其相关的表结构时，都必须更改视图

## MySQL中的视图

> MySQL以两种方式处理对视图的查询
>
> - 第一种方式：MySQL会根据视图定义语句创建一个临时表，并在此临时表上执行传入查询。
> - 第二种方式：MySQL传入查询与查询定义为一个查询并执行组合查询

### MySQL视图的限制

- 不能在视图上创建索引
- MySQL5.7.7之前不能使用SELECT语句的FROM的子句中使用子查询来定义视图
- 如果删除或重命名视图所基于的表，MySQL不会出现错误。但是MySQL会使视图无效。可以使用CHECK TABLE语句来检测视图。
- 一个简单的视图可以更新表中的数据。基于具有连接，子查询的复杂SELECT语句创建的视图无法更新。

## MySQL中创建视图

要在MySQL中创建一个新视图，可以使用CREATE VIEW语句。在MySQL中创建视图的语句如下：

```sql
CREATE 
   [ALGORITHM = {MERGE  | TEMPTABLE | UNDEFINED}]
VIEW [database_name].[view_name] 
AS
[SELECT  statement]
```

### 查看处理算法

算法属性控制着使用MySQL视图的机制。MySQL提供了三种算法：**MERGE**,**TEMPTABLE**和**UNDEINED**

- 使用**MERGE**：MySQL首先将输入查询与定义视图的SELECT语句组合成单个查询。然后MySQL执行组合查询返回结果集。如果SELECT包含一些函数(MIN,MAX,SUM... 或排序 DISTINCT,GROUP BY....) 不允许使用MERGE ———>  UNDEFFINED。
- 使用**TEMPTABLE**算法，根据视图的SELECT语句创建一个临时表，然后针对该临时表执行输入查询。因为MySQL必须创建临时表来存储结果集并将数据从基表移动到临时表，所以TEMPTABLE算法的效率比MERGE算法效率低。(不可更新)
- **UNDEFINED算法：**(默认算法) ---> 使MySQL可以选择MERGE或TEMPTABLE算法。优先使用MERGE算法进行(效率高)

**规定：**

在数据库中，视图和表共享相同的命名空间，因此视图和表不能具有相同的名称。 另外，视图的名称必须遵循表的命名规则。

### 创建MySQL视图示例

```sql
CREATE VIEW SalePerOrder AS
    SELECT 
        orderNumber, SUM(quantityOrdered * priceEach) total
    FROM
        orderDetails
    GROUP by orderNumber
    ORDER BY total DESC;
```

**创建完成**

可以使用`show full tables`查看数据库中表和视图的状态

![image-20200729105142730](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200729105142730.png)

> 上图创建的视图之后，当联系的表删除数据的时候，==视图中的数据也会随之变化==
>
> 也可以基于视图创建另一个视图

### 使用连接表创建视图

```sql
CREATE VIEW customerOrders AS
    SELECT 
        c.customerNumber,
        p.amount
    FROM
        customers c
            INNER JOIN
        payments p ON p.customerNumber = c.customerNumber
    GROUP BY c.customerNumber
    ORDER BY p.amount DESC;
```

**使用子查询创建视图**

```sql
CREATE VIEW aboveAvgProducts AS
    SELECT 
        productCode, productName, buyPrice
    FROM
        products
    WHERE
        buyPrice > 
 (SELECT 
                AVG(buyPrice)
            FROM
                products)
    ORDER BY buyPrice DESC;
```

## 	创建MySQL可更新视图

> 视图不仅是可查询的，而且是可更新的！(可以使用insert或update更新视图，插入或更新基表的行)

但是要创建可更新视图不能包含以下任何元素：

- 聚合函数：MIN,MAX,SUM,AVG,COUNT等
- DISTINCT子句
- GRDOUP BY子句
- HAVING子句
- UNION或UNION ALL子句
- 左连接或外连接
- SELECT子句中的子查询或引用该表的WHERE 子句中的子查询出现在FROM子句中
- 引用FROM子句中的不可更新视图
- 仅引用文字值
- 对基表的任何列多次引用

如果使用TEMPTABLE算法创建视图，则无法更新视图

### MySQL可更新视图示例

```sql
CREATE VIEW officeInfo
 AS 
   SELECT officeCode, phone, city
   FROM offices;
```

**更新视图**

```sql
UPDATE officeInfo 
SET 
    phone = '+86 089866668888'
WHERE
    officeCode = 4;
```

==更新视图之后，对应的那个表也会随之改变==

### 查询可更新视图信息

通过从information_schema数据库中的views表查询is_updatable 列来检查数据库中的视图是否可以更新

以下查询语句将查询 yibaidb 数据库获取所有视图，并显示哪些是可以更新的

```sql
SELECT 
    table_name, is_updatable
FROM
    information_schema.views
WHERE
    table_schema = 'yiibaidb';
```

### 	通过视图删除行

> 删除视图中行的信息之后，对应的视图的那个表对应的行也会随之删除。

## 检查确保视图的一致性

**WITH CHECK OPTION子句简介**

> 有时，创建一个视图只表示某些表的一部分数据，那些在视图上没有显示的数据，对于视图是不可见的。
>
> 当更新了这些不可见的数据时，为了确保视图的一致性。在创建或修改视图时使用WITH CHECK OPTION子句。

```sql
CREATE OR REPLACE VIEW view_name 
AS
  select_statement
  WITH CHECK OPTION;
```

**①根据表创建一个vps视图(只显示jobtitle包含VP的用户)**

```sql
CREATE OR REPLACE VIEW vps AS
    SELECT 
       employeeNumber,
        lastname,
        firstname,
        jobtitle,
        extension,
        email,
        officeCode,
        reportsTo
    FROM
        employees
    WHERE
        jobTitle LIKE '%VP%';
```

**②但是当我们再次向视图中插入数据的时候，这条插入虽然不包括VP，但是还是可以插入，这样是不对的**

```sql
INSERT INTO vps(employeeNumber,firstname,lastname,jobtitle,extension,email,officeCode,reportsTo)
values(1703,'Lily','Bush','IT Manager','x9111','lilybush@yiiibai.com',1,1002);
```

**③我们可以在在创建视图的语句后面加with check potion。这样在插入不包含`VP`的时候是不被允许的**

```sql
CREATE OR REPLACE VIEW vps AS
    SELECT 
        employeeNumber,
        lastname,
        firstname,
        jobtitle,
        extension,
        email,
        officeCode,
        reportsTo
    FROM
        employees
    WHERE
        jobTitle LIKE '%VP%' 
WITH CHECK OPTION;   // 这行
```

插入以下这行不被允许(不包含VP)

```sql
INSERT INTO vps(employeeNumber,firstname,lastname,jobtitle,extension,email,officeCode,reportsTo)
VALUES(1704,'John','Minsu','IT Staff','x9112','johnminsu@yiibai.com',1,1703);
```

==Error Code: 1369 - CHECK OPTION failed 'yiibaidb.vps'==

插入以下这行会成功(包含了VP)

```sql
INSERT INTO vps(employeeNumber,firstname,lastname,jobtitle,extension,email,officeCode,reportsTo)
VALUES(1704,'John','Minsu','SVP Marketing','x9112','johnminsu@classicmodelcars.com',1,1076);
```

## LOCAL和CASCADED在检查

> MySQL会通过视图检查正在更改的每个行，例如：插入，更新，删除，以使其符合视图定义。
>
> 因为MySQ允许基于另一个视图创建视图，它还会检查依赖视图中的规则保持一致。
>
> 为了确定检查范围；MySQL提供了两个选项：**LOCAL**和**CASCADED**（默认）

### CASCADC检查选项

①首先创建一张基础表

```sql
USE testdb;
CREATE TABLE t1 (
    c INT
);
```

②基于基础表创建视图v1(没有检查)   ==这样的话，插入任意数据都是可以的==

```sql
CREATE OR REPLACE VIEW v1 
AS
    SELECT 
        c
    FROM
        t1
    WHERE
        c > 10;
```

③基于v1创建v2视图(添加`WITH CASCADED CHECK OPTION`检查子句) 要符合c>10 才能插入

```sql
CREATE OR REPLACE VIEW v2 
AS
    SELECT 
        c
    FROM
        v1 
WITH CASCADED CHECK OPTION;
```

④根据v2创建v3 (没有指定检查子句)  ==同样还是符合v2的效果==

```sql
CREATE OR REPLACE VIEW v3 
AS
    SELECT 
        c
    FROM
        v2
    WHERE
        c < 20;
```

**结论：**

当视图使用`WITH CASCADED CHECK OPTION`时，MySQL会循环检查视图的规则以及底层视图的规则。

![image-20200729135020940](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200729135020940.png)

### LOCAL检查选项

如果MySQL使用WITH LOCAL CHECK OPTION,MySQL会检查WITH LOCAL CHECK OPTION和WITH CASCADED CHECK OPTION选项的视图规则。

与使用WITH CASCADED CHECK OPTION的视图不同，MySQL检查所有依赖视图的规则。

![image-20200729135608955](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200729135608955.png)

## 管理视图

### 显示视图的定义

```sql
SHOW CREATE VIEW 视图名
```

### 修改视图

修改语句有：`ALTER VIEW` 和 `CREATE OR REPLACE VIEW`

- **使用ALTER VIEW语句修改视图**

alter view语句语法类似create view 除了create关键字被alter关键字替换外，其他都一样。

```sql
ALTER
 [ALGORITHM =  {MERGE | TEMPTABLE | UNDEFINED}]
  VIEW [database_name].  [view_name]
   AS 
 [SELECT  statement]
```

例子：以下语句通过添加email列来演示如何修改organization视图。

```sql
ALTER VIEW organization
  AS 
  SELECT CONCAT(E.lastname,E.firstname) AS Employee,
         E.email AS  employeeEmail,
         CONCAT(M.lastname,M.firstname) AS Manager
  FROM employees AS E
  INNER JOIN employees AS M
    ON M.employeeNumber = E.ReportsTo
  ORDER BY Manager;
```

- **使用CREATE OR REPLACE VIEW语句修改视图**

> 如果一个视图已经存在，只会修改MySQL的视图，如果不存在MySQL将创建新的视图

```sql
CREATE OR REPLACE VIEW v_contacts AS
    SELECT 
        firstName, lastName, extension, email
    FROM
        employees;
-- 查询视图数据
SELECT * FROM v_contacts;
```

添加jobtitle的列

```sql
CREATE OR REPLACE VIEW v_contacts AS
    SELECT 
        firstName, lastName, extension, email, jobtitle
    FROM
        employees;
-- 查询视图数据
SELECT * FROM v_contacts;
```

### 删除视图

创建视图后可以使用DROP VIEW语句将其删除。

 ```sql
DROP VIEW [IF EXISTS] [database_name].[view_name]
 ```

`IF EXISTS`是语句的可选子句，它允许您检查视图是否存在。它可以避免删除不存在的视图的错误。

```sql
DROP VIEW IF EXISTS organization;
```















































