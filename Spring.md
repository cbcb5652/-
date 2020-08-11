# Spring

> ==特点==
>
> - 轻量级
> - 控制反转
> - 面向切面
> - 容器
> - 框架集合

![image-20200810225520048](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200810225520048.png)

## Spring常用模块

**核心容器**

> 核心容器提供Spring框架的基本功能，核心容器的主要组件是BeanFactory，它是工厂模式的实现。BeanFactory使用控制反转(IOC)模式将应用程序的配置和依赖性规范与实际应用程序代码分开。

**Spring上下文**

> Spring上下文是一个配置文件，向Spring框架提供上下文信息，Spring上下文包括企业服务。
>
> 例如：JNDI，EJB，电子邮件，国际化，校验和调度功能

**Spring AOP**

> 通过配置管理特性，Spring AOP模块直接将切面的编程功能集成到Spring框架中，可以将一些通用任务，如安全，事务，日志等集中进行管理，提高了复用性和管理的便捷性

**Spring DAO**

> 为JDBC DAO抽象层提供了有意义的异常层次结构，可用该结构来管理异常处理和不同数据库供应商抛出的错误信息。异常层次结构简化了错误处理，并且极大的降低了需要编写的异常代码数量(例如，打开关闭)。Spring DAO 的面向JDBC的异常遵从通用的DAO异常层次结构。

**Spring ORM**

> Spring框架插入了若干个ORM框架，从而提供了ORM的对象关系工具，其中包括JDO,Hibernate和Ibatis SQL Map。
>
> 所有这些都遵从Spring的通用事务和DAO异常层次结构。

**Spring Web模块**

> Web上下文模块建立在应用程序上下文模块之上，为基于Web的应用提供了上下文，所以，Spring框架支持与Jakarta Structs的集成，Web模块还简化了处理多部分请求以及将请求参数绑定到域对象的工作。

**Spring MVC框架**

> MVC框架是一个全功能的构建Web应用程序的MVC实现。通过策略接口，MVC框架变成为高度可配置的，MVC容纳了大量视图技术，其中包括JSP，Velocity，Tiles，iText和POI

## Spring IOC原理

> 通过配置文件描述Bean及Bean之间的依赖关系，利用反射功能实例化Bena并建立Bean之间的依赖关系。

**Spring容器的高层视图**

 Spring启动时读取应用程序提供的Bean配置信息，并在Spring容器中生成一份Bena的配置注册表，然后根据注册表实例化Bean。

装配好Bean之间的依赖关系，为上层提供准备就绪的运行环境。其中Bean缓存池为HashMap实现的。

![image-20200810233046277](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200810233046277.png)

## Spring Bean

**作用域**

> Spring3 中为Bean定义了5种作用域，分别为singletion(单例)，prototype(原型)，request，session和global session。

----

**singleton:** 单例模式 (多线程模式下不安全)

Spring Ioc容器种只会存在一个共享的Bean实例，无论有多少个Bean引用它，始终指向同一对象。==该模式在多线程环境下是不安全的==。Singleton 作用域是 Spring 中的缺省作用域，也可以显示将Bean 定义为singleton模式，配置为:

```xml
<bean id="userDao" class="com.ioc.UserDaoImpl" scope="singleton"/>
```

**prototype:**

每次通过Spring容器获取prototype定义的Bean时，容器都将创建一个新的Bean实例，每个Bean实例都有自己的属性和状态，而singleton全局只有一个对象。根据经验，对有状态的bean使用prototype作用域，而对无状态的bean使用singleton作用域。

**Requst：**一次request一个实例

在一次http请求中，容器会返回该Bean的同一实例。而对于不同的http请求则会产生新的Bean，而且该bean仅在当前http Request生效，当前http请求结束，该bean实例也将会被销毁。

```xml
<bean id="loginAction" class="com.cnblogs.Login" scope="request"/>
```

**Session**

在一次http Session中，容器会返回该Bean的同一实例。而对不同的Session请求则会创建新的实例，该bean实例仅在当前Session内有效。同http请求相同，每一次session请求创建新的实例，而不同的实例之间不共享属性。且实例仅在自己的session请求内有效，请求结束，则实例将被销毁。

```xml
<bean id="userPreference" class="com.ioc.UserPreference" scope="session"/>
```

**global Session**

在一个全局的http Session中，容器会返回该Bean的同一个实例，仅在使用portlet context时有效。

-------------

**生命周期** 

![image-20200811114016150](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200811114016150.png)

1. 实例化一个 Bean，也就是我们常说的 new。 
IOC 依赖注入 
2. 按照 Spring 上下文对实例化的 Bean 进行配置，也就是 IOC 注入。 
setBeanName 实现实现 
3. 如果这个 Bean 已经实现了 BeanNameAware 接口，会调用它实现的 setBeanName(String)
方法，此处传递的就是 Spring 配置文件中 Bean 的 id 值 
BeanFactoryAware 实现实现 
4. 如果这个 Bean 已经实现了 BeanFactoryAware 接口，会调用它实现的 setBeanFactory，
setBeanFactory(BeanFactory)传递的是 Spring 工厂自身（可以用这个方式来获取其它 Bean，
只需在 Spring 配置文件中配置一个普通的 Bean 就可以）。 

ApplicationContextAware 实现实现 
5. 如果这个 Bean 已经实现了 ApplicationContextAware 接口，会调用
setApplicationContext(ApplicationContext)方法，传入 Spring 上下文（同样这个方式也
可以实现步骤 4 的内容，但比 4 更好，因为 ApplicationContext 是 BeanFactory 的子接
口，有更多的实现方法） 
postProcessBeforeInitialization 接口实现接口实现-初始化预处理初始化预处理 
6. 如果这个 Bean 关联了 BeanPostProcessor 接口，将会调用
postProcessBeforeInitialization(Object obj, String s)方法，BeanPostProcessor 经常被用
作是 Bean 内容的更改，并且由于这个是在 Bean 初始化结束时调用那个的方法，也可以被应
用于内存或缓存技术。 
init-method 
7. 如果 Bean 在 Spring 配置文件中配置了 init-method 属性会自动调用其配置的初始化方法。 
postProcessAfterInitialization 
8. 如果这个 Bean 关联了 BeanPostProcessor 接口，将会调用
postProcessAfterInitialization(Object obj, String s)方法。 
注：以上工作完成以后就可以应用这个 Bean 了，那这个 Bean 是一个 Singleton 的，所以一
般情况下我们调用同一个 id 的 Bean 会是在内容地址相同的实例，当然在 Spring 配置文件中
也可以配置非 Singleton。 
Destroy 过期自动清理阶段 
9. 当 Bean 不再需要时，会经过清理阶段，如果 Bean 实现了 DisposableBean 这个接口，会调
用那个其实现的 destroy()方法； 
destroy-method 自配置清理 
10. 最后，如果这个 Bean 的 Spring 配置中配置了 destroy-method 属性，会自动调用其配置的
销毁方法。

## Spring AOP原理

> 切面：那些与业务无关。却为业务模块所共同调用的逻辑或责任封装起来，便于减少系统的重复代码，降低模块之间的耦合度，并有利于未来的可操作性和可维护性。
>
> 分为：==核心关注点和横切关注点==。横切关注点----> 权限认证，日志，事务。

**AOP的主要应用场景**

1. Authentication 权限
2. Caching 缓存
3. Context passing 内容传递
4. Error handing 错误处理
5. Lazy loading 懒加载
6. Debugging 调试
7. logging tracing profiling and monitoring 记录跟踪，优化，校准
8. Performance optimization 性能优化
9. Persistence 持久化
10. Resource pooling 资源池
11. Synchronization 同步
12. Transactions 事务

**AOP两种代理方式**

> Spring提供了两种方式来生成代理对象: JDKProxy 和 Cglib。
>
> 默认的策略是如果目标类是接口，则使用JDK动态代理技术，否则使用Cglib来生成代理

**JDK动态接口代理**

涉及到java.lang.reflect包中的两个类：Proxy 和 InvocationHandler。

InvocationHandler是一个接口，通过实现该接口定义横切逻辑，并通过反射机制调用目标类的代码，将横切逻辑和业务逻辑编制在一起。

Proxy利用InvocationHandler 动态创建一个符合某一接口的实例，生成目标类的代理对象。

**Cglib动态代理**

Cglib全称为Code Generation Library，是一个强大的高性能，高质量的代码生产库，可以在运行期间扩展java类与实现java接口。

Cglib封装了asm，可以在运行期间动态生成新的class。和JDK动态代理比较:JDK创建代理有一个限制，就是只能为接口创建代理实例，而对于没有接口定义业务方法的类，则可以通过Cglib创建动态代理。

```java
@Aspect 
public class TransactionDemo { 
       @Pointcut(value="execution(* com.yangxin.core.service.*.*.*(..))") 
    public void point(){ 
      } 
     @Before(value="point()") 
    public void before(){ 
        System.out.println("transaction begin"); 
    } 
       @AfterReturning(value = "point()") 
    public void after(){ 
        System.out.println("transaction commit"); 
    } 
        @Around("point()") 
    public void around(ProceedingJoinPoint joinPoint) throws Throwable{ 
        System.out.println("transaction begin"); 
        joinPoint.proceed(); 
        System.out.println("transaction commit"); 
    } 
}
```

## Spring MVC原理

> Spring的模型-视图-控制器 框架是围绕一个DispatcherServlet来设计的。这个Servlet会把请求分发给各个处理器，并支持可配置的处理器映射，视图渲染，本地化，时区与主体渲染等，甚至还能支持文件上传。

![image-20200811155231494](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200811155231494.png)

## Spring Boot原理

有以下几个特点:

- 创建独立的Spring应用程序
- 嵌入Tomcat，无序部署War文件
- 简化Maven配置
- 自动配置Spring
- 提供生产就绪型功能，如指标，健康检测和外部配置
- 绝对没有代码生成和对XML没有要求配置

## Mybatis 缓存

> Mybatis中有一级缓存和二级缓存，默认情况下一级缓存是开启的，而且不能关闭。
>
> 一级缓存是指SqlSession级别的缓存 即：同一个SqlSession中进行相同的SQL语句查询时，第二次后查询不会从数据库查询，																		而是直接从缓存中获取，一级缓存最多缓存1024条SQL。
>
> 二级缓存是指可以跨SqlSession缓存  即： 是mapper级别的缓存，对于mapper级别的缓存不同的sqlSession是可以共享的。

**一级缓存的原理**      (sqlSession级别)

第一次发出一个查询sql，sql查询写入sqlSession的一级缓存中，缓存使用的数据结构是map

key： MapperID + offset + limit + Sql + 所有入参

value: 用户信息

同一个sqlSession再次发出相同的sql，就从缓存中取出数据。如果两次中间出现commit操作(修改，添加，删除)，本sqlSession中的一级缓存区域全部清空，下次再去缓存中查询不到所以要从数据库查询，从数据库查询再写入缓存。

**二级缓存**   (mapper级别)

二级缓存的范围是mapper级别(mapper 同一个命名空间)，mapper以命名空间为单位创建缓存数据结构，结构是map。

mybatis的二级缓存是通过CacheExcutor实现的。CacheExecutor其实是Executor的代理对象。所有的查询操作，在CacheExecutor中都会匹配缓存中是否存在，不存在则查询数据库。

key : MapperID + offset + limit + sql + 所有入参

































































