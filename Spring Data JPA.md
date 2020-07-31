# Spring Data JPA

1. orm思想
2. hibernate框架介绍
3. JPA规范
4. jpa的基本操作
   1. 搭建环境的过程
   2. 完成基本的CRUD操作

简介：

Hibernate是一个开放源代码的对象关系映射框架，它对JDBC进行了非常轻量级的对象封装，它将POJO与数据表建立映射，是一个全自动框架，Hibernate可以自动生成SQL语句，自动执行，使得Java程序员可以随心所欲的使用对象编程思维来操控数据库。

**JPA：**

JSP全称是Java Persistence API，即Java持久化API，是SUN公司推出的一套基于ORM的规范，内部是一系列的接口和抽象类构成。

**JPA优势：**

- 标准化

![image-20200616143655586](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200616143655586.png)

- 容器级特性的支持

![image-20200616143746677](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200616143746677.png)

- 简单方便

![image-20200616143758643](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200616143758643.png)

- 查询能力

![image-20200616143831638](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200616143831638.png)

- 高级特性

![image-20200616143902537](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200616143902537.png)

![image-20200616143943300](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200616143943300.png)

## JPA的基本操作

1. 搭建环境的过程
   1. 创建maven工程导入坐标
   2. 需要配置jpa的核心配置文件
      1. 位置：配置到类路径下的一个叫做META0-INF 的文件夹下
      2. 命名：persistence.xml
   3. 编写客户的实体类
   4. 配置实体类和表，类中属性和表中字段的映射关系
   5. 保存客户到数据库中
2. 完成基本CRUD案例

**jpa操作的操作步骤**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence xmlns="http://java.sun.com/xml/ns/persistence" version="2.0">
    <!-- jpa的约束当中需要配置persistence-unit节点
        持久化单元:
            name:持久化单元名称
            transaction-type:事务管理的方式
                    JTA: 分布式事务管理
                    RESOURCE_LOCAL:本地事务管理
    -->

    <persistence-unit name="myJpa" transaction-type="RESOURCE_LOCAL">
        <!-- jpd的实现方式 -->
        <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>
        <!-- 数据库信息 -->
        <!-- 可选的配置:配置jpa实现方的配置信息 -->
        <properties>
            <!-- 数据库信息
                   用户名：javax.persistence.jdbc.user
                   密码:javax.persistence.jdbc.password
                   驱动:javax.persistence.jdbc.driver
                   数据库地址:javax.persistence.jdbc.url
            -->
            <property name="javax.persistence.jdbc.user" value="root"/>
            <property name="javax.persistence.jdbc.password" value=""/>
            <property name="javax.persistence.jdbc.driver" value="com.mysql.jdbc.Driver"/>
            <property name="javax.persistence.jdbc.url" value="jdbc:mysql:///test"/>

            <!-- 配置jpa实现方（hibernate）的配置信息
                  显示sql          ：  false|true
                  自动创建数据库表  ：hibernate.hbm2ddl.auto
                            create: 程序运行时创建数据库表(如果有表先删除表，再创建
                            update: 程序运行时创建表(如果有表，不会创建表)
                            none  : 不会创建表
            -->
            <property name="hibernate.show_sql" value="true"/>
            <property name="hibernate.hbm2ddl.auto" value="update"/>

        </properties>

    </persistence-unit>
</persistence>
```



```java
package cn.iscas.domain;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/6/16 15:42
 * @Version V1.0
 **/

import javax.persistence.*;

/**
 * 客户实体类
 *      配置映射关系：
 *          1.实体类和表的映射关系
 *          @Entity:声明实体类
 *          @Table:配置实体类和表的映射关系
 *              name:配置数据库表的名称
 *          2. 实体类中属性和表中字段的映射关系
 *
 */
@Entity
@Table(name = "cst_customer")
public class Customer {

    /**
     * //客户的主键
     * @Id：声明主键的配置
     * @GeneratedValue : 配置主键的生成策略
     *          GenerationType.IDENTITY：自增，mysql
     *                  * 底层数据库必须支持自动增长(底层数据库支持的自动增长方式，对id自增)
     *          GenerationType.SEQUENCE:序列
     *                  * 底层必须支持序列，Oracle
     *          GenerationType.TABLE：jpa提供的一种机制，通过一张数据库表的形式帮助我们完成主键自增
     *          GenerationType.AUTO: 由程序自动的帮助我们选择主键生成策略
     * @Column :配置属性和字段的映射关系
     *      name:数据库表中字段的名称
     */
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "cust_id")
    private Long custId;
    //客户名称
    @Column(name = "cust_name")
    private  String custName;
    //客户来源
    @Column(name = "cust_source")
    private String custSource;
    //客户级别
    @Column(name = "cust_level")
    private String custLevel;
    //客户所属行业
    @Column(name = "cust_industry")
    private String custIndustry;
    //客户手机号码
    @Column(name = "cust_phone")
    private String custPhone;
    //客户地址
    @Column(name = "cust_address")
    private String custAddress;

    @Override
    public String toString() {
        return "Customer{" +
                "custId=" + custId +
                ", custName='" + custName + '\'' +
                ", custSource='" + custSource + '\'' +
                ", custLevel='" + custLevel + '\'' +
                ", custIndustry='" + custIndustry + '\'' +
                ", custPhone='" + custPhone + '\'' +
                ", custAddress='" + custAddress + '\'' +
                '}';
    }

    public Long getCustId() {
        return custId;
    }

    public void setCustId(Long custId) {
        this.custId = custId;
    }

    public String getCustName() {
        return custName;
    }

    public void setCustName(String custName) {
        this.custName = custName;
    }

    public String getCustSource() {
        return custSource;
    }

    public void setCustSource(String custSource) {
        this.custSource = custSource;
    }

    public String getCustLevel() {
        return custLevel;
    }

    public void setCustLevel(String custLevel) {
        this.custLevel = custLevel;
    }

    public String getCustIndustry() {
        return custIndustry;
    }

    public void setCustIndustry(String custIndustry) {
        this.custIndustry = custIndustry;
    }

    public String getCustPhone() {
        return custPhone;
    }

    public void setCustPhone(String custPhone) {
        this.custPhone = custPhone;
    }

    public String getCustAddress() {
        return custAddress;
    }

    public void setCustAddress(String custAddress) {
        this.custAddress = custAddress;
    }
}
```

1. 加载配置文件创建实体管理器工厂

2. 根据实体管理工厂，创建实体管理器

   - EntityManagerFactory:获取EntityManager对象
   - 方法：createEntityManager
   - * 内部维护的很多的内容
     * 内部维护了数据库信息
     * 内部维护了缓存信息
     * 维护了所有的实体管理器对象
     * 再创建EntityManagerFactory对象的过程中会根据配置创建数据库表
   - EntityManagerFactory创建过程比较浪费资源

   特点：线程安全的对象

   - 多个线程访问同一个EntityManagerFactory不有线程安全问题
   - 如何解决EntityManagerFactory的创建过程浪费资源（耗时的问题）？
     - 思路：创建一个公共的EntityManagerFactory对象
     - 借助静态代码块来创建

3. 创建事务对象，开启事务

   EntityManager对象：实体类管理器

   ​	beginTransaction:创建事务对象

   ​	presist:  保存

   ​	merge：更新

   ​	remove：删除

   ​	find/getRefrence： 根据id查询

   Transaction 对象：事务

   ​	begin:开启事务

   ​	commit：提交事务

   ​	roolback：回滚

4. 增删改查操作

4. 提交事务

5. 释放资源

```java
package cn.isaca.test;

import cn.iscas.domain.Customer;
import org.junit.Test;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/6/16 15:53
 * @Version V1.0
 **/

public class JpaTest {

    /**
     * 测试jpa的保存：
     *      案例：保存一个客户到数据库中
     * JPA的操作步骤:
     *      1.加载配置文件创建工厂(实体管理工厂)对象
     *      2.通过实体管理类工厂获取实体管理器
     *      3.获取事务对象，开启事务
     *      4.完成增删改查c操作
     *      5.提交事务(回滚事务)
     *      6.释放资源
     */
    @Test
    public void testSave(){
        //1.加载配置文件创建工厂(实体管理工厂)对象
        EntityManagerFactory factory = Persistence.createEntityManagerFactory("myJpa");
        //2.通过实体管理类工厂获取实体管理器
        EntityManager em = factory.createEntityManager();
        //3.获取事务对象，开启事务
        EntityTransaction tx = em.getTransaction();
        tx.begin();
        //4.完成增删改查c操作
        Customer customer = new Customer();
        customer.setCustName("陈彬");
        customer.setCustIndustry("广东金融学院");
        customer.setCustPhone("13060868459");
        //保存
        em.persist(customer);//保存操作
        //5.提交事务(回滚事务)
        tx.commit();
        //6.释放资源
        em.close();
        factory.close();
    }

}
```

### Hibernate的增删改查操作

```java
package cn.isaca.test;

import cn.iscas.domain.Customer;
import cn.iscas.utils.JpaUtils;
import org.junit.Test;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/6/16 15:53
 * @Version V1.0
 **/

public class JpaTest {

    /**
     * 测试jpa的保存：
     *      案例：保存一个客户到数据库中
     * JPA的操作步骤:
     *      1.加载配置文件创建工厂(实体管理工厂)对象
     *      2.通过实体管理类工厂获取实体管理器
     *      3.获取事务对象，开启事务
     *      4.完成增删改查c操作
     *      5.提交事务(回滚事务)
     *      6.释放资源
     */
    @Test
    public void testSave(){
//        //1.加载配置文件创建工厂(实体管理工厂)对象
//        EntityManagerFactory factory = Persistence.createEntityManagerFactory("myJpa");
//        //2.通过实体管理类工厂获取实体管理器
//        EntityManager em = factory.createEntityManager();
        EntityManager em = JpaUtils.getEntityManager();
        //3.获取事务对象，开启事务
        EntityTransaction tx = em.getTransaction();
        tx.begin();
        //4.完成增删改查c操作
        Customer customer = new Customer();
        customer.setCustName("陈彬");
        customer.setCustIndustry("广东金融学院");
        customer.setCustPhone("13060868459");
        //保存
        em.persist(customer);//保存操作
        //5.提交事务(回滚事务)
        tx.commit();
        //6.释放资源
        em.close();
//        factory.close();
    }

    /**
     * 根据id查询各户
     *  使用find方法查询:
     *      1.查询的对象就算当前客户对象本身
     *      2.再调用find方法的时候，就会发送sql语句查询你数据库
     *
     *  立即加载
     *
     */
    @Test
    public void testFind(){
        //1.通过工具类获取entityManager对象
        EntityManager entityManager = JpaUtils.getEntityManager();
        //2.开启事务
        EntityTransaction transaction = entityManager.getTransaction();
        transaction.begin();
        //3.增删改查
        /**
         *  find: 根据id查询数据
         *          class:查询数据的结果需要包装的实体类类型的字节码
         *          id:查询的主键的取值
         */
        Customer customer = entityManager.find(Customer.class, 1l);
        System.out.println(customer);
        //4.提交事务
        transaction.commit();
        //5.释放事务
        entityManager.close();

    }


    /**
     * 根据id查询各户
     *  使用getRederence方法
     *             1. 获取的对象是一个动态代理对象
     *             2. 调用getRederence方法不会立刻发送sql语句查询数据库
     *              *   当调用查询结果对象的时候，才会发送查询的sql语句：什么时候时候发送sql？
     *
     *  延迟加载（懒加载
     *      * 得到的是一个动态代理对象，什么时候用，什么时候才进行查询
     */
    @Test
    public void testReference(){
        //1.通过工具类获取entityManager对象
        EntityManager entityManager = JpaUtils.getEntityManager();
        //2.开启事务
        EntityTransaction transaction = entityManager.getTransaction();
        transaction.begin();
        //3.增删改查
        /**
         *  find: 根据id查询数据
         *          class:查询数据的结果需要包装的实体类类型的字节码
         *          id:查询的主键的取值
         */
        Customer customer = entityManager.getReference(Customer.class, 1l);
        System.out.println(customer);
        //4.提交事务
        transaction.commit();
        //5.释放事务
        entityManager.close();
    }

    /**
     * 删除客户案例
     *
     */
    @Test
    public void testRemove(){
        //1.通过工具类获取entityManager对象
        EntityManager entityManager = JpaUtils.getEntityManager();
        //2.开启事务
        EntityTransaction transaction = entityManager.getTransaction();
        transaction.begin();

        //3.增删改查--删除客户

        //i 根据id插叙客户
        Customer customer = entityManager.find(Customer.class, 1l);
        //ii 调用remove方法完成删除操作
        entityManager.remove(customer);

        //4.提交事务
        transaction.commit();
        //5.释放事务
        entityManager.close();
    }


    /**
     * 跟新客户的操作
     *      merge(Object)
     */
    @Test
    public void testUpdate(){
        //1.通过工具类获取entityManager对象
        EntityManager entityManager = JpaUtils.getEntityManager();
        //2.开启事务
        EntityTransaction transaction = entityManager.getTransaction();
        transaction.begin();

        //3.增删改查--删除客户

        //i 查询客户
        Customer customer = entityManager.find(Customer.class, 2l);
        //ii 更新客户
        customer.setCustIndustry("IT计算机科学与技术");
         entityManager.merge(customer);

        //4.提交事务
        transaction.commit();
        //5.释放事务
        entityManager.close();
    }
}
```

基本CRUD案例：

- persist:保存
- merge：更新
- remove：删除
- find/getRefrence：根据id进行查询

### jpql 查询（Java  Persistence Query Language）

- **sql**：查询的是表和表中的字段
- **jpql**：查询的是实体类和类中的属性

1.  查询全部
2. 分页查询
3. 统计查询
4. 条件查询
5. 排序

```java
package cn.isaca.test;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/6/16 17:16
 * @Version V1.0
 **/

import cn.iscas.utils.JpaUtils;
import org.junit.Test;

import javax.persistence.EntityManager;
import javax.persistence.EntityTransaction;
import javax.persistence.Query;
import java.util.List;

/**
 *   测试jpql查询
 */
public class JpqlTest {
    /**
     * 查询全部
     *      jpql:   from cn.iscas.domain.Customer
     *      sql:    select * from cst_customer
     */
     @Test
    public  void testFindAll(){
         //1.  获取entityManager对象
         EntityManager em = JpaUtils.getEntityManager();
         //2.开启事务
         EntityTransaction tx = em.getTransaction();
         tx.begin();
         //3.查询全部
         String jpql = "from Customer";
         Query query = em.createQuery(jpql);// 创建Query查询对象，query对象才是执行jqpl的对象

         //发送查询，并封装结果
         List list = query.getResultList();

         list.forEach(x->{
             System.out.println(x);
         });

         //4.提交事务
         tx.commit();
         //5.释放紫云
         em.close();
     }

    /**
     *  排序查询：倒叙查询
     *      sql: select * from cst_customer order by cust_id desc
     *      jpql:from Customer order by custId
     *
     *  进行jpql查询
     *      1. 创建query查询对象
     *      2. 对参数进行赋值
     *      3. 查询，并得到结果集
     */
    @Test
    public  void testOrders(){
        //1.  获取entityManager对象
        EntityManager em = JpaUtils.getEntityManager();
        //2.开启事务
        EntityTransaction tx = em.getTransaction();
        tx.begin();
        //3.查询全部
        String jpql = "from Customer order by custId desc";
        Query query = em.createQuery(jpql);// 创建Query查询对象，query对象才是执行jqpl的对象

        //发送查询，并封装结果
        List list = query.getResultList();

        list.forEach(x->{
            System.out.println(x);
        });

        //4.提交事务
        tx.commit();
        //5.释放紫云
        em.close();
    }


    /**
     * 使用jpql查询客户的总数
     *      sql:select count(id) from cst_customer
     *      jpql:select count(custId) from Customer
     */
    @Test
    public  void testCount(){
        //1.  获取entityManager对象
        EntityManager em = JpaUtils.getEntityManager();
        //2.开启事务
        EntityTransaction tx = em.getTransaction();
        tx.begin();
        //3.查询全部
        String jpql = "select count(custId) from Customer";
        Query query = em.createQuery(jpql);// 创建Query查询对象，query对象才是执行jqpl的对象

        Object singleResult = query.getSingleResult();
        System.out.println(singleResult);

        //4.提交事务
        tx.commit();
        //5.释放紫云
        em.close();
    }


    /**
     *  分页查询
     *      sql:select * from cst_customer limit 0,2
     *      jpql:from Customer
     */
    @Test
    public  void testPage(){
        //1.  获取entityManager对象
        EntityManager em = JpaUtils.getEntityManager();
        //2.开启事务
        EntityTransaction tx = em.getTransaction();
        tx.begin();
        //3.查询全部
        String jpql = "from Customer";
        Query query = em.createQuery(jpql);// 创建Query查询对象，query对象才是执行jqpl的对象

        query.setFirstResult(0);
        query.setMaxResults(2);


        //4.提交事务
        tx.commit();
        //5.释放紫云
        em.close();
    }


    /**
     * 条件查询
     *  案例：查询客户
     *      sql： select * from cst_customer where cust_name like %陈彬%
     *      jpql: from Customer where custName like ?
     */
    @Test
    public  void testCondition(){
        //1.  获取entityManager对象
        EntityManager em = JpaUtils.getEntityManager();
        //2.开启事务
        EntityTransaction tx = em.getTransaction();
        tx.begin();
        //3.查询全部
        String jpql = "from Customer where custName like ?";
        Query query = em.createQuery(jpql);// 创建Query查询对象，query对象才是执行jqpl的对象

        // 参数赋值，占位符参数
        //第一个参数：占位符索引位置(从1 开始),第二个参数:取值
        query.setParameter(1," chenbin%");

        //4.提交事务
        tx.commit();
        //5.释放紫云
        em.close();
    }

}
```

## SpringDataJpa

### **概述：**

![image-20200616234933579](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200616234933579.png)

Jpa是一套规范，内部是有接口和抽象类组成的。Hibernate是一套成熟的ORM框架，而且Hibernate实现了JPA规范，所以称hibernate为JPA的一种实现方式，我们使用JPA的Api编程，意味着站在更高的角度上看待问题(面向接口编程)

Spring Data JPA是Spring提供的一套对JPA操作更加高级的封装，是在JPA规范下的专门用来进行数据持久化的解决方案。

### **入门操作**

案例：客户的基本CRUD

1. 搭建环境

   1. 创建工程导入坐标
   2. 配置spring的配置文件 (配置spring Data jpa的整合)
   3. 编写实体类(Customer),使用jpa注解配置映射关系

2. 编写一个符合springDataJpa的dao层接口

   1. 只需要编写dao层接口，不需要编写dao层接口的实现类
   2. dao层接口规范
      1. 需要继承两个接口 (JpaRepository,JpaSpecificationExecutor)
      2. 需要提供相应

   findOne(id):根据id查询

   save(Customer): 保存或者更新( 依据： 传递的实体类对象中，是否包含id属性)

   delete(id): 根据id删除

   findAll() : 查询全部

### **运行过程和原理刨析**

![image-20200617095752078](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200617095752078.png)

1. 通过JdkDynamicAopProxy的invoke方法创建了一个动态代理对象
2. SimpleJpaRepository 当中封装了JPA的操作 (借助JPA的api完成数据库的CRUD)
3. 通过hibernate完成数据库操作 (封装了jdbc)

### **复杂查询**

1. 借助接口中定义好的方法完成查询

   1. findOne(id): 根据id查询

2. jpql的查询方式

   1. jpql: jpa query language   (jpq查询语言)
   2. 特点：语法或关键字和sql语句类似
      1. 查询的是类和类中的属性

   - 需要将JPQL语句配置到接口上
     - 特有的查询：需要在dao接口上配置方法
     - 在新添加的方法上，使用注解的形式配置jpql查询语句
     - 注解： @Query

3. sql语句的查询

   1. 特有的查询：需要在dao接口上配置方法
   2. 在新添加的方法上，使用注解的形式配置jpql查询语句
   3. 注解： @Query
      1. value:jpql语句 | sql语句
      2. nativeQuery：false | true (使用本地查询:sql 查询)
         1. 是否使用本地查询

4.  方法名称规则查询

   1.   是对jpql查询，更加深入的一层封装

   2. 我们只需要按照SpringDataJpa提供的方法名称规则定义方法，不需要再去配置jpql语句查询，完成查询。

      ​		

      ​	findBy开头：代表查询

      ​			对象开头：代表查询

      ​					对象中属性的名称 ( 首字母大写)

      ​			含义：根据属性名进行查询

实体类

```java
package cn.iscas.domain;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/6/17 9:07
 * @Version V1.0
 **/

import javax.persistence.*;

/**
 * 1. 实体类和表的映射关系
 *      @Entity
 *      @Table
 * 2. 类中属性和表中字段的映射关系
 *      @Id
 *      @GeneratedValue
 *      @Column
 */
@Entity
@Table(name = "cst_customer")
public class Customer {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "cust_id")
    private Long custId;
    @Column(name = "cust_address")
    private String custAddress;
    @Column(name = "cust_industry")
    private String custIndustry;
    @Column(name = "cust_level")
    private String custLevel;
    @Column(name = "cust_name")
    private String custName;
    @Column(name = "cust_phone")
    private String custPhone;
    @Column(name = "cust_source")
    private String custSource;

    @Override
    public String toString() {
        return "Customer{" +
                "custId=" + custId +
                ", custAddress='" + custAddress + '\'' +
                ", custIndustry='" + custIndustry + '\'' +
                ", custLevel='" + custLevel + '\'' +
                ", custName='" + custName + '\'' +
                ", custPhone='" + custPhone + '\'' +
                ", custSource='" + custSource + '\'' +
                '}';
    }

    public Long getCustId() {
        return custId;
    }

    public void setCustId(Long custId) {
        this.custId = custId;
    }

    public String getCustAddress() {
        return custAddress;
    }

    public void setCustAddress(String custAddress) {
        this.custAddress = custAddress;
    }

    public String getCustIndustry() {
        return custIndustry;
    }

    public void setCustIndustry(String custIndustry) {
        this.custIndustry = custIndustry;
    }

    public String getCustLevel() {
        return custLevel;
    }

    public void setCustLevel(String custLevel) {
        this.custLevel = custLevel;
    }

    public String getCustName() {
        return custName;
    }

    public void setCustName(String custName) {
        this.custName = custName;
    }

    public String getCustPhone() {
        return custPhone;
    }

    public void setCustPhone(String custPhone) {
        this.custPhone = custPhone;
    }

    public String getCustSource() {
        return custSource;
    }

    public void setCustSource(String custSource) {
        this.custSource = custSource;
    }
}
```



配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:jpa="http://www.springframework.org/schema/data/jpa"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/data/jpa http://www.springframework.org/schema/data/jpa/spring-jpa-1.3.xsd
    http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
    http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd">

    <!-- spring 和springdata jpa的配置 -->

    <!-- 1.创建entityManagerFactory对象交给spring容器管理 -->
    <bean id="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!-- 配置的是扫描的包(实体类所在的包) -->
        <property name="packagesToScan" value="cn.iscas.domain"/>
        <!-- jpa的实现厂家 -->
        <property name="persistenceProvider">
            <bean class="org.hibernate.jpa.HibernatePersistenceProvider"/>
        </property>
        <!-- jpa的供应商适配器 -->
        <property name="jpaVendorAdapter">
            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter">
                <!-- 配置是否自动创建数据库表 -->
                <property name="generateDdl" value="false"/>
                <!-- 指定数据库类型 -->
                <property name="database" value="MYSQL"/>
                <!-- 数据库方言：支持特有的语法 -->
                <property name="databasePlatform" value="org.hibernate.dialect.MySQLDialect"/>
                <!-- 是否显示sql -->
                <property name="showSql" value="true"/>
            </bean>
        </property>

        <!-- jpa的方言，高级特性 -->
        <property name="jpaDialect">
            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaDialect"/>
        </property>

    </bean>

    <!-- 2. 创建数据库连接池 -->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="user" value="root"/>
        <property name="password" value=""/>
        <property name="jdbcUrl" value="jdbc:mysql:///test"/>
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
    </bean>

    <!--3. 整合spring dataJpa -->
    <jpa:repositories base-package="cn.iscas.dao" transaction-manager-ref="transationManager"
                      entity-manager-factory-ref="entityManagerFactory"/>

    <!-- 4. 配置事务管理器 -->
    <bean id="transationManager" class="org.springframework.orm.jpa.JpaTransactionManager">
        <property name="entityManagerFactory" ref="entityManagerFactory"></property>
    </bean>

    <!--5. 声明式事务  -->

    <!--6. 配置包扫描  -->
    <context:component-scan base-package="cn.isaca"></context:component-scan>

</beans>
```



用到接口

```java
package cn.iscas.dao;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/6/17 9:20
 **/

import cn.iscas.domain.Customer;
import com.sun.xml.internal.bind.v2.model.core.ID;
import org.junit.jupiter.api.DynamicTest;
import org.junit.jupiter.api.Test;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.JpaSpecificationExecutor;
import org.springframework.data.jpa.repository.Modifying;
import org.springframework.data.jpa.repository.Query;

import java.util.List;

/**
 * 符合SpringDataJpa的dao层接口规范
 *      JpaRepository<操作实体类类型，实体类中主键属性
 *          * 封装了基本的CRUD操作
 *      JpaSpecificationExecutor<操作的实体类型
 *          * 封装了复杂查询(分页)
 */
public interface CustomerDao extends JpaRepository<Customer, Long>, JpaSpecificationExecutor<Customer> {

    /**
     * 案例：根据客户名称查询客户
     *      使用jpql的形式查询
     * jpql：from Customer where custName = ?
     *
     * 配置jpql语句：使用@Query注解
     */
    @Query(value = "from Customer where custName = ?1")
    public Customer findJpql(String custName);

    /**
     * 案例：根据客户名称和客户id查询
     *         jpql:from Customer where custName = ? and custId = ?
     *
     * 对于多个占位符参数
     *        赋值的时候，默认情况下，占位符的位置要和方法的参数中的位置保持一致
     *
     * 可以指定占位符参数的位置
     *        ? 索引的方式，指定此占位的取值来源
     */
    @Query(value = "from Customer where custName = ?1 and custId = ?2")
    public Customer findCustNameAndId(String name,Long id);

    /**
     * 使用jpql完成更新的操作
     *      案例：根据id更新客户的名称
     *          更新4号客户的名称，将名称改为:"lvrr"
     *
     * sql: update cst_customer set cust_name = ? where cust_id = ?
     * jpql: update Customer set custName = ? where custId = ?
     *
     * @Query: 代表的是进行查询
     *          * 声明此方法是用来进行更新操作
     *
     * @Modifying
     *          * 当前执行的是一个更新操作
     */
    @Query(value = "update Customer set custName = ?2 where custId = ?1")
    @Modifying
    public void updateCustomer(long custId ,String custName);


    /**
     * 使用sql的形式查询
     *       查询全部的客户
     * sql: select * from cst_customer
     * Query: 配置sql查询
     *      value ： sql语句
     *      nativeQuery： 查询方式
     *              true：sql查询
     *              false:jpql查询
     */
    //@Query(value = "select * from cst_customer",nativeQuery = true)
    @Query(value = "select * from cst_customer where cust_name like ?1",nativeQuery = true)
    public List<Object []> findsql(String name);


    /**
     *  方法名的约定：
     *          findBy：查询
     *              对象中属性名(首字母大写 ):查询的条件
     *              CustName
     *              * 默认情况： 使用 等于的方式进行查询
     *                  特性的查询方式
     *  findByCustName   --   根据客户名称查询
     *
     *  在SpringdataJpa的运行阶段
     *          会根据方法名称进行解析   findBy    from  xxx(实体类
     *                                      属性名称       where custName =
     *
     *  findBy + 属性名称(根据属性名称进行完成匹配的查询= )
     *      findBy + 属性名称 + "查询方式(Like | isnull)"
     *          findByCustNameLike
     *
     *   多条件查询
     *      findBy + 属性名 + “查询方式”   +  “多条件的连接符(and|or)”+ 属性名 + “查询方式”
     */
    public Customer findByCustName(String custName);


    public List<Customer> findByCustNameLike(String custName);

    //使用客户名称模糊匹配和客户所属行列精准匹配的查询
    public Customer findByCustNameLikeAndCustIndustry(String custName,String custIndustry);
}
```



测试代码：

```java
package cn.iscas.test;

import cn.iscas.dao.CustomerDao;
import cn.iscas.domain.Customer;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.annotation.Rollback;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.transaction.annotation.Transactional;

import java.util.Arrays;
import java.util.List;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/6/17 10:25
 * @Version V1.0
 **/
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class JpqlTest {

    @Autowired
    private CustomerDao customerDao;

    @Test
    public void testFindJpql(){
        Customer customer = customerDao.findJpql("chenbin");
        System.out.println(customer);
    }

    @Test
    public void testFindCustNameAndId(){
        Customer customer = customerDao.findCustNameAndId("chenbin", 3l);
        System.out.println(customer);
    }


    /**
     * 测试jpql的更新操作
     *     * springDataJpa中使用jpql完成，更新/删除操作
     *          * 需要手动添加事务的支持
     *          * 默认会执行结束之后，回滚事务
     *     @Rollback:设置事务是否混滚
     *          fasle | true
     */
    @Test
    @Transactional
    @Rollback(value = false)
    public void testUpdate(){
        customerDao.updateCustomer(3l,"lvrr");
    }

    //sql查询测试
    @Test
    public void testFindSql(){
        List<Object[]> list = customerDao.findsql("lv%");
        list.forEach(x->{
            System.out.println(Arrays.toString(x));
        });
    }


    //测试方法命名规则的查询
    @Test
    public void testNaming(){
        Customer customer = customerDao.findByCustName("lvrr");
        System.out.println(customer);
    }

    //测试方法命名规则的查询
    @Test
    public void testFindByCustomer(){
        List<Customer> customers = customerDao.findByCustNameLike("lv%");
        customers.forEach(x->{
            System.out.println(x.toString());
        });
    }


    @Test
    public void findByCustNameLikeAndCustIndustry(){
        Customer cu = customerDao.findByCustNameLikeAndCustIndustry("lv%", "itttttttttttt");
        System.out.println(cu);
    }

}
```

![image-20200617113346714](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200617113346714.png)

## 动态查询

**Specifications 动态查询**

<img src="C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200617114458178.png" alt="image-20200617114458178" style="zoom:150%;" />

### 多表之间的关系和操作多表的操作步骤

**表关系**

- 一对一：
- 一对多：
  - 一的一方：主表
  - 多的以方：从表
  - 外键：需要再从表上新建一列作为外键，它的取值来源于主表的主键
- 多对多：
  - 中间表：中间表最少有两个字段组成，这两个字段作为外键指向两张表的主键，又组合成了联合主键

实体类中的关系

​		包含关系

​		继承关系

**一对多操作**
	案例：客户和联系人的案例

​	从一方查询多方

			* 默认：使用延迟加载

从多方查询一方

- 默认：使用立即加载

---------------------------------------

# 第二次学习JPA

## Specifications动态查询

> 方法列表

- T findone(Specifition<T> spec);    //查询单个对象
- List<T> findAll(Specification<T> spec);    //查询列表

//查询全部，分页

//pageable:  分页参数	

//返回值：分页pageBean(page: 是springdatajps提供的)

- Page<T> findAll(Specification<T> spec,	Pageable pageable);

//查询列表

// Sort: 排序参数

- List<T> findAll(Speciton<T> spec,Sort sort);

// 统计查询

- long count(Specifition<T> spec);



> Specification 查询条件

自定义我们自己的Specification实现类

​		//root：	查询的根对象(查询的任何属性都可以从根对象中获取)

​		//CriteriaQuery:    顶层查询对象，自定义查询方式(了解：一般不用)

​		//CriteriaBuilder:	查询的构造器，封装了很多的查询条件

​	Predicate toPredicate(Root<T> var1, CriteriaQuery<?> var2, CriteriaBuilder var3);//封装查询条件

```java
 /**
     * 查询单个对象
     */
    @Test
    public void testSpec(){
        /**
         * 匿名内部类
         *  1.实现Specification接口,(提供泛型，查询的对象类型)
         *  2. 实现toPredicate(构造查询条件)
         *  3. 需要借助方法参数中的两个参数(
         *      root；获取需要查询的对象属性
         *      CriterBuilder:构造查询条件的，内部封装了很多的查询条件(模糊 匹配，精准匹配)
         *  )
         *  案例：根据客户名称查询，查询客户为  chenbin 的客户
         *       查询条件
         *          1.查询方式
         *              criteriaBuilder对象
         *          2.比较的属性名称
         *              root对象
         */
        Specification<Customer> spec = new Specification<Customer>() {
            @Override
            public Predicate toPredicate(Root<Customer> root, CriteriaQuery<?> criteriaQuery, CriteriaBuilder criteriaBuilder) {
                //1.获取比较的属性
                Path<Object> custName = root.get("custName");
                //2.构造查询条件    select * from cst_customer where cust_name = 'chenbin'
                /**
                 * 第一个参数：需要比较的属性（path对象）
                 * 第二个参数：当前需要比较的取值
                 */
                Predicate predicate = criteriaBuilder.equal(custName, "chenbin");//进行精准匹配(比较的属性名，比较的属性取值)
                return predicate;
            }
        };
        Optional<Customer> customer = customerDao.findOne(spec);
        System.out.println(customer);
    }
```

> 多条件查询

```java
 /**
     * 多条件查询
     *      案例：根据客户名(XXX) 和客户所属公司查询
     */
    public void testSpec1(){
        /**
         * root：获取属性
         *          客户名
         *          所属行业
         *  cb： 构造查询
         *          1，构造客户名的精准匹配查询
         *          2. 构造所属行业的精准匹配查询
         *          3. 将以上两个查询联系起来
         */
        Specification spec = new Specification() {
            @Override
            public Predicate toPredicate(Root root, CriteriaQuery criteriaQuery, CriteriaBuilder criteriaBuilder) {
                Path custName = root.get("custName");//客户名
                Path custIndustry = root.get("custIndustry");

                //构造查询
                //1.构造客户名精准匹配
                Predicate chenbin = criteriaBuilder.equal(custName, "chenbin");
                Predicate it = criteriaBuilder.equal(custIndustry, "it");
                //2.将多个查询条件组合到一起:组合（满足条件1 并且满足条件2）     满足条件1 或者 满足条件2
                Predicate and = criteriaBuilder.and(chenbin, it);//以与的形式拼接多个查询条件
//                criteriaBuilder.or();
                return and;
            }
        };
        List<Customer> customers = customerDao.findAll(spec);
        System.out.println(customers);
    }
```

> 模糊条件查询   并且 排序

```java
 /**
     * 案例：根据客户名称的模糊匹配，返回客户列表
     *
     * equal:  直接得到path对象属性，然后直接进行比较就可
     * gt,lt,le,like  : 得到path对象，根据path指定比较的参数类型，再去进行比较
     *      指定参数类型: path.as(类型的字节码对象)
     */
    @Test
    public void testSpec3(){
        //构造查询条件
        Specification<Customer> spec = new Specification() {
            @Override
            public Predicate toPredicate(Root root, CriteriaQuery criteriaQuery, CriteriaBuilder criteriaBuilder) {
                //查询属性：客户名
                Path custName = root.get("custName");
                //查询方式：模糊
                Predicate like = criteriaBuilder.like(custName.as(String.class), "chen%");
                return like;
            }
        };
       // List<Customer> list = customerDao.findAll(spec);
        //for (Customer customer : list) {
        //    System.out.println(customer);
       // }
         //添加排序
        //创建排序对象, 需要调用构造方法传递参数
        //第一个参数排序的顺序（倒序    正序）
        //      Sort.Direction.DESC: 倒序   Sort.Direction.ASC：升序
        //第二个参数：排序属性的名称
        Sort sort = new Sort(Sort.Direction.DESC,"custId");
        customerDao.findAll(spec,sort);
    }
```

--------------------

## 多表操作

表关系

​		一对一

​		一对多

​					一的一方：主表

​					多的一方：从表

​					外键：需要再从表上新建一列作为外键，他的取值来源于主表的主键

​		多对多

​					中间表:中间表中最少应该由两个字段组成，这两个字段作为外键指向两张表的主键，又组成了联合主键



分析步骤：

1. 明确表关系
2. 确定表关系（描述  外键|中间表）
3. 编写实体类 ，在实体类中描述表关系（包含关系）
4. 配置映射关系

完成多表操作

1. 一对多操作
   1. 案例:客户和联系人的案例
      1. 客户：一家公司
      2. 联系人：公司的员工
   2. 一个客户可以有多个联系人
   3. 一个联系人从属于一家公司



> 一对多、

**配置客户与联系人之间的关系**

在customer中创建一对多关系

@OneToMany(targetEntity = LinkMan.class);  //添加如下注解

 @JoinColumn(name = "lkm_cust_id",referencedColumnName = "cust_id")   //name:外键的名称  

​																												 referencedColumnName：参照的主表主键名称

```java
package cn.itcast.domain;

import javax.persistence.*;
import java.util.HashSet;
import java.util.Set;

/**
 * 1.实体类和表的映射关系
 *      @Eitity
 *      @Table
 * 2.类中属性和表中字段的映射关系
 *      @Id
 *      @GeneratedValue
 *      @Column
 */
@Entity
@Table(name="cst_customer")
public class Customer {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name="cust_id")
    private Long custId;
    @Column(name="cust_address")
    private String custAddress;
    @Column(name="cust_industry")
    private String custIndustry;
    @Column(name="cust_level")
    private String custLevel;
    @Column(name="cust_name")
    private String custName;
    @Column(name="cust_phone")
    private String custPhone;
    @Column(name="cust_source")
    private String custSource;

    //配置客户和联系人之间的关系（一对多关系）
    /**
     * 使用注解的形式配置多表关系
     *      1.声明关系
     *          @OneToMany : 配置一对多关系
     *              targetEntity ：对方对象的字节码对象
     *      2.配置外键（中间表）
     *              @JoinColumn : 配置外键
     *                  name：外键字段名称
     *                  referencedColumnName：参照的主表的主键字段名称
     *
     *  * 在客户实体类上（一的一方）添加了外键了配置，所以对于客户而言，也具备了维护外键的作用
     *
     */

//    @OneToMany(targetEntity = LinkMan.class)
//    @JoinColumn(name = "lkm_cust_id",referencedColumnName = "cust_id")
    /**
     * 放弃外键维护权
     *      mappedBy：对方配置关系的属性名称\
     * cascade : 配置级联（可以配置到设置多表的映射关系的注解上）
     *      CascadeType.all         : 所有
     *                  MERGE       ：更新
     *                  PERSIST     ：保存
     *                  REMOVE      ：删除
     *
     * fetch : 配置关联对象的加载方式
     *          EAGER   ：立即加载
     *          LAZY    ：延迟加载

      */
    @OneToMany(mappedBy = "customer",cascade = CascadeType.ALL)
    private Set<LinkMan> linkMans = new HashSet<>();

    public Long getCustId() {
        return custId;
    }

    public void setCustId(Long custId) {
        this.custId = custId;
    }

    public String getCustAddress() {
        return custAddress;
    }

    public void setCustAddress(String custAddress) {
        this.custAddress = custAddress;
    }

    public String getCustIndustry() {
        return custIndustry;
    }

    public void setCustIndustry(String custIndustry) {
        this.custIndustry = custIndustry;
    }

    public String getCustLevel() {
        return custLevel;
    }

    public void setCustLevel(String custLevel) {
        this.custLevel = custLevel;
    }

    public String getCustName() {
        return custName;
    }

    public void setCustName(String custName) {
        this.custName = custName;
    }

    public String getCustPhone() {
        return custPhone;
    }

    public void setCustPhone(String custPhone) {
        this.custPhone = custPhone;
    }

    public String getCustSource() {
        return custSource;
    }

    public void setCustSource(String custSource) {
        this.custSource = custSource;
    }

    public Set<LinkMan> getLinkMans() {
        return linkMans;
    }

    public void setLinkMans(Set<LinkMan> linkMans) {
        this.linkMans = linkMans;
    }

    @Override
    public String toString() {
        return "Customer{" +
                "custId=" + custId +
                ", custAddress='" + custAddress + '\'' +
                ", custIndustry='" + custIndustry + '\'' +
                ", custLevel='" + custLevel + '\'' +
                ", custName='" + custName + '\'' +
                ", custPhone='" + custPhone + '\'' +
                ", custSource='" + custSource + '\'' +
                '}';
    }
}
```

**配置联系人与客户之间的关系**



```java
package cn.itcast.domain;

import javax.persistence.*;

@Entity
@Table(name = "cst_linkman")
public class LinkMan {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "lkm_id")
    private Long lkmId; //联系人编号(主键)
    @Column(name = "lkm_name")
    private String lkmName;//联系人姓名
    @Column(name = "lkm_gender")
    private String lkmGender;//联系人性别
    @Column(name = "lkm_phone")
    private String lkmPhone;//联系人办公电话
    @Column(name = "lkm_mobile")
    private String lkmMobile;//联系人手机
    @Column(name = "lkm_email")
    private String lkmEmail;//联系人邮箱
    @Column(name = "lkm_position")
    private String lkmPosition;//联系人职位
    @Column(name = "lkm_memo")
    private String lkmMemo;//联系人备注

    /**
     * 配置联系人到客户的多对一关系
     *     使用注解的形式配置多对一关系
     *      1.配置表关系
     *          @ManyToOne : 配置多对一关系
     *              targetEntity：对方的实体类字节码
     *      2.配置外键（中间表）
     *
     * * 配置外键的过程，配置到了多的一方，就会在多的一方维护外键
     *
     */
    @ManyToOne(targetEntity = Customer.class,fetch = FetchType.LAZY)
    @JoinColumn(name = "lkm_cust_id",referencedColumnName = "cust_id")
    private Customer customer;

    public Long getLkmId() {
        return lkmId;
    }

    public void setLkmId(Long lkmId) {
        this.lkmId = lkmId;
    }

    public String getLkmName() {
        return lkmName;
    }

    public void setLkmName(String lkmName) {
        this.lkmName = lkmName;
    }

    public String getLkmGender() {
        return lkmGender;
    }

    public void setLkmGender(String lkmGender) {
        this.lkmGender = lkmGender;
    }

    public String getLkmPhone() {
        return lkmPhone;
    }

    public void setLkmPhone(String lkmPhone) {
        this.lkmPhone = lkmPhone;
    }

    public String getLkmMobile() {
        return lkmMobile;
    }

    public void setLkmMobile(String lkmMobile) {
        this.lkmMobile = lkmMobile;
    }

    public String getLkmEmail() {
        return lkmEmail;
    }

    public void setLkmEmail(String lkmEmail) {
        this.lkmEmail = lkmEmail;
    }

    public String getLkmPosition() {
        return lkmPosition;
    }

    public void setLkmPosition(String lkmPosition) {
        this.lkmPosition = lkmPosition;
    }

    public String getLkmMemo() {
        return lkmMemo;
    }

    public void setLkmMemo(String lkmMemo) {
        this.lkmMemo = lkmMemo;
    }

    public Customer getCustomer() {
        return customer;
    }

    public void setCustomer(Customer customer) {
        this.customer = customer;
    }

    @Override
    public String toString() {
        return "LinkMan{" +
                "lkmId=" + lkmId +
                ", lkmName='" + lkmName + '\'' +
                ", lkmGender='" + lkmGender + '\'' +
                ", lkmPhone='" + lkmPhone + '\'' +
                ", lkmMobile='" + lkmMobile + '\'' +
                ", lkmEmail='" + lkmEmail + '\'' +
                ", lkmPosition='" + lkmPosition + '\'' +
                ", lkmMemo='" + lkmMemo + '\'' +
                '}';
    }
}
```

级联：

​		操作一个对象的同时也操作他的关联的对象

​		级联操作：

		1. 需要区分操作主体
  		2. 需要在操作主体的实体类上，添加级联属性（需要添加到多表映射关系的注解上）
                		3. cascade（配置级联）

级联添加：

​		案例：当我保存一个客户的同时保存联系人

级联删除：

​		案例：当我们删除一个客户的同时删除此客户的所有联系人



级联添加/删除

```java
 /**
     * 级联添加：保存一个客户的同时，保存客户的所有联系人
     *      需要在操作主体的实体类上，配置casacde属性
     */
    @Test
    @Transactional //配置事务
    @Rollback(false) //不自动回滚
    public void testCascadeAdd() {
        Customer customer = new Customer();
        customer.setCustName("百度1");

        LinkMan linkMan = new LinkMan();
        linkMan.setLkmName("小李1");

        linkMan.setCustomer(customer);
        customer.getLinkMans().add(linkMan);

        customerDao.save(customer);
    }


    /**
     * 级联删除：
     *      删除1号客户的同时，删除1号客户的所有联系人
     */
    @Test
    @Transactional //配置事务
    @Rollback(false) //不自动回滚
    public void testCascadeRemove() {
        //1.查询1号客户
        Customer customer = customerDao.findOne(1l);
        //2.删除1号客户
        customerDao.delete(customer);
    }
```

==配置级联删除 全部==

<kbd>cascade = CascadeType.ALL</kbd>

![image-20200709225600924](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200709225600924.png)

多对多操作

​		案例：用户和角色（多对多）

​				用户：

​				角色：

分析步骤：

1. 明确表关系
   1. 多对多关系
2. 确定表关系（描述 外键|中间表）
   1. 中间间表
3. 编写实体类，在实体类中描述表关系（包含关系）
   1. 用户：包含角色的集合
   2. 角色：包含用户的集合
4. 配置映射关系

> 多表查询

1. 对象导航查询 

   查询一个对象的同时，通过此对象查询他的关联对象

只需要通过id查询出某个对象就能根据这个对象获得关于这个对象的关联对象

```java
  //could not initialize proxy - no Session
    //测试对象导航查询（查询一个对象的时候，通过此对象查询所有的关联对象）
    @Test
    @Transactional // 解决在java代码中的no session问题
    public void  testQuery1() {
        //查询id为1的客户
        Customer customer = customerDao.getOne(1l);
        //对象导航查询，此客户下的所有联系人
        Set<LinkMan> linkMans = customer.getLinkMans();

        for (LinkMan linkMan : linkMans) {
            System.out.println(linkMan);
        }
    }
```

延迟加载

```java
 /**
     * 对象导航查询：
     *      默认使用的是延迟加载的形式查询的
     *          调用get方法并不会立即发送查询，而是在使用关联对象的时候才会差和讯
     *      延迟加载！
     * 修改配置，将延迟加载改为立即加载
     *      fetch，需要配置到多表映射关系的注解上
     *
     */

    @Test
    @Transactional // 解决在java代码中的no session问题
    public void  testQuery2() {
        //查询id为1的客户
        Customer customer = customerDao.findOne(1l);
        //对象导航查询，此客户下的所有联系人
        Set<LinkMan> linkMans = customer.getLinkMans();

        System.out.println(linkMans.size());
    }
```

从一方查询多方：

​		默认：使用延迟加载

从多方查询一方

​		默认：使用立即加载

## 条件查询(简单高级查询)

在接口中实现抽象方法;

>  (抽象方法名称有规则)
>
> ① 方法名称必须以findBy  或者 findAllBy 开头
>
> ② findBy  或者 findAllBy后面紧跟domain实体类中的属性字段名称（首字母转为大写）
>
> ③遵守驼峰命名法
>
> ④属性名称后紧跟SQL语句中的条件符号(单词)
>
> ​		Like									like
>
> ​		Equals								=
>
> ​		In										in
>
> ​		Between							between.....and
>
> ​		LessThan							<
>
> ​		LessThanEqual				<=
>
> ​		GreaterThan					>
>
> ​		GreaterThanEqual			>=
>
> ⑤ 多条件可以用And或者Or来连接，例如：findByNameLikeAndIdGreaterThanEqual
>
> ​			select * from t_user where name like ? or id >= ?
>
> ⑥ 方法参数个数必须与条件个数匹配，顺序要匹配，类型要匹配

```java
public interface IUserDao extends JpaRepository<User ,Long>{
    List<User> findAllByNameLike(String name);
}
```

在测试类中使用该方法:

```java
@Test
public void testFindAllByNameLike() throws Exception{
    List<User> list = userDao.findAllByNameLike("张%");
	list.forEach(user -> System.out.println(user));
}
```

![image-20200714090325359](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200714090325359.png)

# 第三次总结JPA

## 基本配置

> 1. 配置pom.xml文件 依赖

```xml
<dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
</dependency>
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

> 2. 当前springboot的版本信息如下：

```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.4.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
</parent>
```

> 3. 创建dto对象

```java
package com.example.demo.dto;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Customer {
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Long id;
    private String firstName;
    private String lastName;
    protected Customer() {}
    public Customer(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
    @Override
    public String toString() {
        return String.format(
                "Customer[id=%d, firstName='%s', lastName='%s']",
                id, firstName, lastName);
    }
}
```

> 4. 创建操作数据库的Repository对象

```java
package com.example.demo.repositories;
import com.example.demo.dto.Customer;
import org.springframework.data.jpa.repository.JpaRepository;
public interface CustomerRepository extends JpaRepository<Customer, Long> {
}
```

继承了JpaRepository之后拥有了

```java
public interface JpaRepository<T,ID extends Serializable>
extends PagingAndSortingRepository<T,ID>, QueryByExampleExecutor<T>

Methods inherited from interface org.springframework.data.repository.PagingAndSortingRepository
findAll
Methods inherited from interface org.springframework.data.repository.CrudRepository
count, delete, delete, delete, deleteAll, exists, findOne, save
Methods inherited from interface org.springframework.data.repository.query.QueryByExampleExecutor
count, exists, findAll, findOne
```

 从上面可以看出，CustomerRepository继承了JpaRepository之后就拥有了CurdRepository，QueryByExampleExecutor，PagingAndSortingRepository的基本能力了，包括基本的增删改查。

> 配置数据库信息

需要在application.properties加上如下数据

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.jpa.properties.hibernate.hbm2ddl.auto=create
```

这里需要注意到最后一行数据spring.jpa.properties.hibernate.hbm2ddl.auto=create，这个参数是为了方便处理数据库表，该属性的值一般有如下几个：

```xml
validate               加载hibernate时，验证创建数据库表结构
create                  每次加载hibernate，重新创建数据库表结构，这就是导致数据库表数据丢失的原因。
create-drop        加载hibernate时创建，退出是删除表结构
update                 加载hibernate自动更新数据库结构
```

## 继承的方法

1. 方法列表

   1）、从PagingAndSortingRepository继承的findAll方法
   2）、从CrudRepository继承的count, delete,deleteAll, exists, findOne, save等方法
   3）、从QueryByExampleExecutor继承的count, exists, findAll, findOne**

2. demo示例

> 使用save方法，初始化，保存Customer数据

==注意：这里当主键的id在数据库中存在的时候就会变为更新的方法==

```java
 @Controller
    @RequestMapping("/customer")
    public class CustomerController {
        @Autowired
        private CustomerRepository repository;

        /**
         * 初始化数据
         */
        @RequestMapping("/index")
        public void index() {
            // save a couple of customers
            repository.save(new Customer("Jack", "Bauer"));
            repository.save(new Customer("Chloe", "O'Brian"));
            repository.save(new Customer("Kim", "Bauer"));
            repository.save(new Customer("David", "Palmer"));
            repository.save(new Customer("Michelle", "Dessler"));
            repository.save(new Customer("Bauer", "Dessler"));
        }
```

> findAll方法，查找出完成初始化的数据

```java
 /**
  * 查询所有
 */
@RequestMapping("/findAll")
public void findAll(){
   List<Customer> result = repository.findAll();
    for (Customer customer:result){
        System.out.println(customer.toString());
    }
    System.out.println("-------------------------------------------");
}
```

> findOne方法，根据long型Id为参数，返回对应的人

```java
/**
 * 查询ID为1的数据
 */
@RequestMapping("/findOne")
public void findOne(){
    Customer result = repository.findOne(1L);
    if(result!=null){
        System.out.println(result.toString());
    }
    System.out.println("-------------------------------------------");
}
```

> delete方法，根据id删除指定数据，也可以用批量删除方法

```java
/**
 * 查询ID为1的数据
 */
@RequestMapping("/delete")
public void delete(){

    System.out.println("删除前数据：");
    List<Customer> customers = repository.findAll();
    for (Customer customer:customers){
        System.out.println(customer.toString());
    }

    System.out.println("删除ID=3数据：");
    repository.delete(3l);

    System.out.println("删除后数据：");
    customers = repository.findAll();
    for (Customer customer:customers){
        System.out.println(customer.toString());
    }
    System.out.println("-------------------------------------------");
}
```

## 创建查询

1. 在CustomerRepository接口中，有如下定义:

```java
/**
         * 根据lastName查询结果
         * @param lastName
         * @return
         */
        List<Customer> findByLastName(String lastName);
```

这是通过参数lastName去解析，并将查询结果返回，语义相当于`select * from customer where last_name=${lastName}`,在controller中就可以直接进行使用该方法了。



2. 在CustomerController代码中使用CustomerRepository进行数据查询操作。如下：

```java
 /**
         * 查询lastName为指定用户昵称
         */
        @RequestMapping("/findByLastName")
        public void findByLastName(){
            List  <;Customer>; result = repository.findByLastName("Bauer");
            for (Customer customer:result){
                System.out.println(customer.toString());
            }
            System.out.println("-------------------------------------------");
        }
```



3. 创建查询的方法名命名指南

![img](http://springforall.ufile.ucloud.com.cn/static/img/9a32bc484cda2b7d89371d909296b81d1515160)

![img](http://springforall.ufile.ucloud.com.cn/static/img/a0af26b59b775a77cf5d5702ce0597a21515160)

![img](http://springforall.ufile.ucloud.com.cn/static/img/99481fa01fabba86843fb2422b9e85771515160)

## 使用@Query注解

> 1. 在CustomerReporsitory里面添加

```java
/**
         * 模糊匹配
         * @param bauer
         * @return
         */
        @Query("select c from Customer c where c.firstName=?1")
        Customer findByFirstName2(String bauer);

        @Query("select c from Customer c where c.lastName=?1 order by c.id desc")
       List<Customer> findByLastName2(String lastName);

        /**
         * 一个参数，匹配两个字段
         * @param name2
         * @return
         * 这里Param的值和=:后面的参数匹配，但不需要和方法名对应的参数值对应
         */
        @Query("select c from Customer c where c.firstName=:name or c.lastName=:name  order by c.id desc")
       List<Customer> findByName(@Param("name") String name2);

        /**
         * 一个参数，匹配两个字段
         * @param name
         * @return
         * 这里的%只能放在占位的前面，后面不行
         */
        @Query("select c from Customer c where c.firstName like %?1")
       List<Customer> findByName2(@Param("name") String name);

        /**
         * 一个参数，匹配两个字段
         * @param name
         * @return
         * 开启nativeQuery=true，在value里可以用原生SQL语句完成查询
         */
        @Query(nativeQuery = true,value = "select * from Customer c where c.first_name like concat('%' ,?1,'%') ")
       List<Customer> findByName3(@Param("name") String name);
```

> 2. 在CustomerController内添加

```java
 /**
         * @Query注解方式查询
         * 查询FirstName为指定字符串
         */
        @RequestMapping("/findByFirstName2")
        public void findByFirstName2(){
            Customer customer = repository.findByFirstName2("Bauer");
            if(customer!=null){
                System.out.println(customer.toString());
            }
            System.out.println("-------------------------------------------");
        }

        /**
         * @Query注解方式查询
         * 查询LastName为指定字符串
         */
        @RequestMapping("/findByLastName2")
        public void findByLastName2(){
             List<Customer>  result = repository.findByLastName2("Bauer");
            for (Customer customer:result){
                System.out.println(customer.toString());
            }
            System.out.println("-------------------------------------------");
        }

        /**
         * @Query注解方式查询,
         * 用@Param指定参数，匹配firstName和lastName
         */
        @RequestMapping("/findByName")
        public void findByName(){
             List<Customer>  result = repository.findByName("Bauer");
            for (Customer customer:result){
                System.out.println(customer.toString());
            }
            System.out.println("-------------------------------------------");
        }

        /**
         * @Query注解方式查询,使用关键词like
         * 用@Param指定参数，firstName的结尾为e的字符串
         */
        @RequestMapping("/findByName2")
        public void findByName2(){
            List<Customer>  result = repository.findByName2("e");
            for (Customer customer:result){
                System.out.println(customer.toString());
            }
            System.out.println("-------------------------------------------");
        }

        /**
         * @Query注解方式查询，模糊匹配关键字e
         */
        @RequestMapping("/findByName3")
        public void findByName3(){
            List<Customer> result = repository.findByName3("e");
            for (Customer customer:result){
                System.out.println(customer.toString());
            }
            System.out.println("-------------------------------------------");
        }
```

解释：

> ？加数字表示占位符，？1代表在方法参数里的第一个参数，区别于其他的index，这里从1开始
> =:加上变量名，这里是与方法参数中有@Param的值匹配的，而不是与实际参数匹配的
> JPQL的语法中，表名的位置对应Entity的名称，字段对应Entity的属性,详细语法见相关文档
> 要使用原生SQL需要在@Query注解中设置nativeQuery=true，然后value变更为原生SQL即可

使用了@Query之后 还是可以使用Sort对象来进行排序的，在Repostory接口定义的方法中，传入Sort对象就可了。

## 使用@Modifying

1. 在CustomerRepository上增加新方法

```java
    /**
         * 根据lastName去更新firstName，返回结果是更改数据的行数
         * @param firstName
         * @param lastName
         * @return
         */
        @Modifying//更新查询
        @Transactional//开启事务
        @Query("update Customer c set c.firstName = ?1 where c.lastName = ?2")
        int setFixedFirstnameFor(String firstName, String lastName);
```

这里需要注意，在使用@Modifying注解的时候，一定要加上事务注解@Transactional，如果你忘了或者加错了，那很可能报如下错误：

 2017-07-01 01:03:24.844 ERROR 8072 --- [nio-8080-exec-1] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed; nested exception is org.springframework.dao.InvalidDataAccessApiUsageException: Executing an update/delete query; nested exception is javax.persistence.TransactionRequiredException: Executing an update/delete query] with root cause

2. 在Customer中测试：

```java
    /**
         * 根据FirstName进行修改
         */
        @RequestMapping("/modifying")
        public void modifying(){
            Integer result = repository.setFixedFirstnameFor("Bauorx","Bauer");
            if(result!=null){
                System.out.println("modifying result:"+result);
            }
            System.out.println("-------------------------------------------");

        }
```



## 使用@Query之后的分页page

1. 改变CustomerRepository方法

```java
 /**
         * 一个参数，匹配两个字段
         * @param name2
         * @Param pageable 分页参数
         * @return
         * 这里Param的值和=:后面的参数匹配，但不需要和方法名对应的参数值对应
         * 这里增加了@QueryHints注解，是给查询添加一些额外的提示
         * 比如当前的name值为HINT_COMMENT是在查询的时候带上一些备注信息
         */
        @QueryHints(value = { @QueryHint(name = HINT_COMMENT, value = "a query for pageable")})
        @Query("select c from Customer c where c.firstName=:name or c.lastName=:name")
        Page&lt;Customer&gt; findByName(@Param("name") String name2,Pageable pageable);
```

2. 增加了CustomerController方法pageable

```java
 /**
         * 分页
         * 应用查询提示@QueryHints，这里是在查询的适合增加了一个comment
         * 查询结果是lastName和firstName都是bauer这个值的数据
         */
        @RequestMapping("/pageable")
        public void pageable(){
            //Pageable是接口，PageRequest是接口实现
            //PageRequest的对象构造函数有多个，page是页数，初始值是0，size是查询结果的条数，后两个参数参考Sort对象的构造方法
            Pageable pageable = new PageRequest(0,3, Sort.Direction.DESC,"id");
            Page&lt;Customer&gt; page = repository.findByName("bauer",pageable);
            //查询结果总行数
            System.out.println(page.getTotalElements());
            //按照当前分页大小，总页数
            System.out.println(page.getTotalPages());
            //按照当前页数、分页大小，查出的分页结果集合
            for (Customer customer: page.getContent()) {
                System.out.println(customer.toString());
            }
            System.out.println("-------------------------------------------");
        }
```



## Specification1

1. 定义一个JpaSpecificationExecutor的接口

```java
import com.example.demo.dto.Customer;
    import org.springframework.data.jpa.repository.JpaRepository;
    import org.springframework.data.jpa.repository.JpaSpecificationExecutor;

    /**
     * Created by Administrator on 2017/7/12 0012.
     */
    public interface CustomerSpecificationRepository extends JpaRepository&lt;Customer,Long&gt;,
        JpaSpecificationExecutor&lt;Customer&gt; {
    }
```

这里只是声明继承接口声明的方法，比如

```java
 T findOne(Specification<T> spec);
    List<T> findAll(Specification<T> spec);
    Page<T> findAll(Specification<T> spec, Pageable pageable);
    List<T> findAll(Specification<T> spec, Sort sort);
    long count(Specification<T> spec);
```



2. 创建SpecificationFactory工具类

```java
import org.springframework.data.jpa.domain.Specification;
    import javax.persistence.criteria.Path;
    /**
     * Created by Administrator on 2017/7/13 0013.
     */
    public final class SpecificationFactory {
        public static Specification containsLike(String attribute, String value) {
            return (root, query, cb) -> cb.like(root.get(attribute), "%" + value + "%");
        }
        public static Specification isBetween(String attribute, int min, int max) {
            return (root, query, cb) -> cb.between(root.get(attribute), min, max);
        }
        public static Specification isBetween(String attribute, double min, double max) {
            return (root, query, cb) -> cb.between(root.get(attribute), min, max);
        }
        public static<T extends Enum> T<> Specification enumMatcher(String attribute, T queriedValue) {
            return (root, query, cb) -> {
                Path<T> actualValue = root.get(attribute);
                if (queriedValue == null) {
                    return null;
                }
                return cb.equal(actualValue, queriedValue);
            };
        }
    }
```



3. 在CustomerController中添加测试方法

- 初始化

```java
@Autowired
private CustomerSpecificationRepository csr;
```

- 单一条件查询

```java
 @RequestMapping("/spec")
    public void specificationQuery(){
        Specification<Customer> spec = SpecificationFactory.containsLike("lastName","bau");
        Pageable pageable = new PageRequest(0,5, Sort.Direction.DESC,"id");
        Page<Customer> page = csr.findAll(spec,pageable);
        System.out.println(page);
        System.out.println(page.getTotalElements());
        System.out.println(page.getTotalPages());
        for (Customer c:page.getContent()){
            System.out.println(c.toString());
        }
    }
```



- 复合条件查询

```java
@RequestMapping("/spec2")
    public void specificationQuery2(){
        Specification<Customer> spec2 = Specifications
                .where(SpecificationFactory.containsLike("firstName","bau"))
                .or(SpecificationFactory.containsLike("lastName","bau"));
        Pageable pageable = new PageRequest(0,5, Sort.Direction.DESC,"id");
        Page<Customer> page = csr.findAll(spec2,pageable);
        System.out.println(page);
        System.out.println(page.getTotalElements());
        System.out.println(page.getTotalPages());
        for (Customer c:page.getContent()){
            System.out.println(c.toString());
        }
    }
```

## Specifiction 2

1. 更改Customer类

增加@OneToMany注解的订单对象

```java
 import lombok.AllArgsConstructor;
    import lombok.Data;
    import lombok.NoArgsConstructor;
    import org.hibernate.annotations.NamedQuery;
    import javax.persistence.*;
    import java.util.List;
    @Entity
    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    @NamedQuery(name="Customer.findByFirstName",query = "select c from Customer c where c.firstName = ?1")
    public class Customer {
        @Id
        @GeneratedValue(strategy=GenerationType.AUTO)
        private Long id;
        private String firstName;
        private String lastName;

        //一对多，一个客户对应多个订单，关联的字段是订单里的cId字段
        @OneToMany
        @JoinColumn(name = "cId")
        private List<MyOrder> myOrders;
        public Customer(String firstName, String lastName) {
            this.firstName = firstName;
            this.lastName = lastName;
        }
        @Override
        public String toString() {
            return String.format(
                    "Customer[id=%d, firstName='%s', lastName='%s']",
                    id, firstName, lastName);
        }
    }
```

2. 增加MyOrder类

我的订单对象

```java
import lombok.AllArgsConstructor;
    import lombok.Data;
    import lombok.NoArgsConstructor;
    import javax.persistence.*;
    import java.math.BigDecimal;
    /**
     * Created by Administrator on 2017/7/17 0017.
     */
    @Entity
    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    public class MyOrder {
        @Id
        @GeneratedValue(strategy = GenerationType.AUTO)
        private Long id;
        private String code;
        private Long cId;
        private BigDecimal total;
        //实体映射重复列必须设置：insertable = false,updatable = false
        @OneToOne
        @JoinColumn(name = "cId",insertable = false,updatable = false)
        private Customer customer;
        @Override
        public String toString() {
            return "MyOrder{" +
                    "id=" + id +
                    ", code='" + code + '\'' +
                    ", cId=" + cId +
                    ", total=" + total +
                    ", customer=" + customer +
                    '}';
        }
    }
```

3. 新增MyOrderRepository类

```java
//这里主要是继承JpaSpecificationExecutor接口，进行Specification查询

    import com.example.demo.dto.MyOrder;
    import org.springframework.data.jpa.repository.JpaSpecificationExecutor;
    import org.springframework.data.repository.CrudRepository;
    /**
     * Created by Administrator on 2017/7/17 0017.
     */
    public interface MyOrderRepository extends 		           JpaSpecificationExecutor<MyOrder>,CrudRepository<MyOrder,Long> {
    }
```

4. 新增ShoppingController类

```java
  import com.example.demo.dto.Customer;
    import com.example.demo.dto.MyOrder;
    import com.example.demo.repositories.MyOrderRepository;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.data.domain.Page;
    import org.springframework.data.domain.PageRequest;
    import org.springframework.data.domain.Pageable;
    import org.springframework.data.domain.Sort;
    import org.springframework.data.jpa.domain.Specification;
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.RequestMapping;
    import javax.persistence.criteria.*;
    @Controller
    @RequestMapping("/shop")
    public class ShoppingController {
        @Autowired
        private MyOrderRepository myOrderRepository;
        /**
         * 内连接查询
         */
        @RequestMapping("/q1")
        public void specification1(){
            //根据查询结果，声明返回值对象，这里要查询用户的订单列表，所以声明返回对象为MyOrder
            Specification<MyOrder> spec = new Specification<MyOrder>() {
                //Root&lt;X&gt;  根查询，默认与声明相同
                @Override
                public Predicate toPredicate(Root<MyOrder> root, CriteriaQuery<> query, CriteriaBuilder cb) {
                    //声明并创建MyOrder的CriteriaQuery对象
                    CriteriaQuery<MyOrder> q1 = cb.createQuery(MyOrder.class);
                    //连接的时候，要以声明的根查询对象（这里是root，也可以自己创建）进行join
                    //Join&lt;Z,X&gt;是Join生成的对象，这里的Z是被连接的对象，X是目标对象，
                    //  连接的属性字段是被连接的对象在目标对象的属性，这里是我们在MyOrder内声明的customer
                    //join的第二个参数是可选的，默认是JoinType.INNER(内连接 inner join)，也可以是JoinType.LEFT（左外连接 left join）
                    Join<Customer,MyOrder> myOrderJoin = root.join("customer",JoinType.INNER);
                    //用CriteriaQuery对象拼接查询条件，这里只增加了一个查询条件，cId=1
                    q1.select(myOrderJoin).where(cb.equal(root.get("cId"),1));
                    //通过getRestriction获得Predicate对象
                    Predicate p1= q1.getRestriction();
                    //返回对象
                    return p1;
                }
            };
            resultPrint(spec);
        }
        /**
         * 增加查询条件，关联的对象Customer的对象值
         */
        @RequestMapping("/q2")
        public void specification2(){
            Specification<MyOrder> spec = new Specification<MyOrder>() {
                @Override
                public Predicate toPredicate(Root&lt;MyOrder&gt; root, CriteriaQuery<?>query, CriteriaBuilder cb) {
                    CriteriaQuery<MyOrder> q1 = cb.createQuery(MyOrder.class);
                    Join<Customer,MyOrder> myOrderJoin = root.join("customer");
                    q1.select(myOrderJoin)
                            .where(
                                    cb.equal(root.get("cId"),1),//cId=1
                                    cb.equal(root.get("customer").get("firstName"),"Jack")//对象customer的firstName=Jack
                            );
                    Predicate p1= q1.getRestriction();
                    return p1;
                }
            };
            resultPrint(spec);
        }
        /**
         * in的条件查询
         * 需要将对应的结果集以root.get("attributeName").in(Object.. values)的方式传入
         * values支持多个参数，支持对象（Object），表达式Expression&lt;?&gt;，集合Collection以及Expression&lt;Collection&lt;?&gt;&gt;
         */
        @RequestMapping("/q3")
        public void specification3(){
            Specification<MyOrder> spec = new Specification<MyOrder>() {
                @Override
                public Predicate toPredicate(Root<MyOrder> root, CriteriaQuery<?> query, CriteriaBuilder cb) {
                    CriteriaQuery<MyOrder> q1 = cb.createQuery(MyOrder.class);
                    Join<Customer,MyOrder> myOrderJoin = root.join("customer");
                    q1.select(myOrderJoin)
                            .where(
                                    cb.equal(root.get("cId"),1)
                                    ,root.get("id").in(1,2,4)
                            );

                    Predicate p1= q1.getRestriction();
                    return p1;
                }
            };
            resultPrint(spec);
        }
        /**
         * 左外链接查询，对比inner join，
         * 这里只是改了一个参数，将JoinType.INNER改成JoinType.LEFT
         *
         * 注意，当前示例不支持JoinType.RIGHT，用的比较少，没有探究
         */
        @RequestMapping("/q4")
        public void specification4(){
            Specification<MyOrder> spec = new Specification<MyOrder>() {
                @Override
                public Predicate toPredicate(Root<MyOrder> root, CriteriaQuery<?> query, CriteriaBuilder cb) {
                    CriteriaQuery<MyOrder> q1 = cb.createQuery(MyOrder.class);
                    Join<Customer,MyOrder> myOrderJoin = root.join("customer",JoinType.LEFT);
                    q1.select(myOrderJoin).where(cb.equal(root.get("cId"),1));
                    Predicate p1= q1.getRestriction();
                    return p1;
                }
            };
            resultPrint(spec);
        }
        /***
        *输出分页信息
        **/
        private void resultPrint(Specification<MyOrder> spec) {
            //分页查询
            Pageable pageable = new PageRequest(0,10, Sort.Direction.DESC,"id");
            //查询的分页结果
            Page<MyOrder>page =myOrderRepository.findAll(spec,pageable);
            System.out.println(page);
            System.out.println(page.getTotalElements());
            System.out.println(page.getTotalPages());
            for (MyOrder c:page.getContent()){
                System.out.println(c.toString());
            }
        }

    }
```





























































