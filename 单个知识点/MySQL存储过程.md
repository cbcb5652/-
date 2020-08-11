# MySQL存储过程

## 存储过程简介

> 存储过程是存储在数据库中的一段声明性的SQL

![image-20200730093736657](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200730093736657.png)

**MySQL存储过程的优点**

- 有助于提高性能
- 可以减少应用程序和数据库服务器之间的流量(只需发送存储过程名和参数)
- 对任何应用程序都是可重用和透明的。
- 存储过程是安全的。(管理员可以给相应的用户赋予权限)

**MySQL存储过程的缺点、**

- 大量存储过程，使每个连接内存大大增加。使用大量逻辑操作会降低CPU使用率。
- 使开发具有复杂的业务逻辑，存储变得困难。
- 很难调试存储过程，MySQL不能调试存储过程
- 开发和维护存储过程不是很容易

## 存储过程入门

**第一个存储过程**

GetAllProducts() 存储过程从products表中选择所有产品

```sql
DELIMITER //
 CREATE PROCEDURE GetAllProducts()
   BEGIN
   SELECT *  FROM products;
   END //
DELIMITER ;
```

> 解释上面的语句

- **DELIMITER** 将分号`;`更改为`//` 表示开始。 结束的时候用DELIMITER； 表示 `;`
- **CREATE PROCEDURE** 表示创建一个存储过程。后面接存储过程名。GetAllProducts()
- **BEGIN****和 **END** 之间的部分为主体，放sql语句

==也可以使用可视化页面来创建存储过程==









































