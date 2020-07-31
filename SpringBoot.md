# SpringBoot

> 去看看Dubbo+zookeeper

==SpringSecurity:Shiro 看看！！！==

**往下了解的springCloud**

- 微服务
- guspringcloud入门
- Restful
- Eureka
- Ribbon
- Feign
- HyStrix
- Zuul路由网关
- SpringCloud config：git

**现在了解：Springboot**

- 配置yml
- 自动装配：重要：谈资
- 继承web开发：业务的核心
- 继承数据库 Druid
- 分布式开发：Doubbo(RPC) +zookeeper
- swagger:接口文档
- 任务调度
- SpringSecurity :Shiro

## 注解

`注解说明`

```java
@RestController：  注解在类上面的，表示类里面的函数都返回字符串---> @ResponseBody;

@SpringBootApplication:     程序主入口，注解的那个类本身就是spring的组件;

@SpringBootConfiguration:    Spring的配置;

@Value:    给对应的属性赋值  --->  @Value("旺财") private String name;

@Component/@Autowired:     添加进容器(注入pojo类)/注入容器(对象);

@PropertySource/@value:     指定pojo所要加载的配置文件/@value("${name}"): 取出配置文件中所对应的值，并附上;

@Validated/@Email:         指定校对对应的pojo类，声明在类上面/添加在属性上面，指定属性必须是email格式;

@RequestParam/@PathVariable:    解析/user/?id=123中的id /解析/user/{username}/blog 中的username
```

![image-20200612172457891](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200612172457891.png)

**启动器**

```xml
<dependency>
	<groupId>org.spring.framework.boot</groupId>
    <artifactId>spring-boot-start-web</artifactId>
</dependency>
```



**结论：**springboot所有的自动配置都是在启动的时候扫描加载:Spring.factories所有的配置都在这里面，但是不一定生效，要判断条件是否成立，只要导入了对应的start，就有了对应的启动器了，有了启动器，我们自动装配就会生效，然后就配置成功！

1. springboot在启动的时候，从类路径下/META-INF/spring.factories 获取指定的值；
2. 将这些自动配置的类导入容器，自动配置就会生效，帮我们进行自动配置
3. 以前我们需要自动配置的东西，现在Springboot帮我们做了
4. 整个javaee，和自动配置的东西都在spring-boot-autoconfigure-2.2.0.RELEASE.jar包下
5. 它会把所有需要导入的组件，以类名的方式返回；这些组件就会被添加到容器；
6. 容器中也会存在非常多的XXXAutoConfiguration的文件@Bean，就是这些类给容器中导入了这个场景需要的所有组件，并自动配置,@Configuration,JavaConfig
7. 有了自动配置类，就免去了很多的自动配置。

> SpringApplication类主要做的四件事情

1. 推断应用的类似是普通的项目还是web项目
2. 查找并加载所有可用初始化器，设置initializers属性中
3. 找出所有应用程序监听器，设置到listeners属性中
4. 推断并设置main方法的定义类，找到运行的主类

> 关于SpringBoot谈谈你的理解

- 自动装配
- run()

给一个**pojo**对象添加上一个``@Component` 相当于将这个对象注入到容器中

在别的类中直接 `@Autowired` 就可以将那个对象注入了。

> @PropertySource/@value

![image-20200613140102606](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200613140102606.png)

> @Validated/@Email
>
> JSR303校验

![image-20200613140953112](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200613140953112.png)

`@RequestParam/@PathVariable`

@RequestParam和@PathVariable都能够完成类似的功能——因为本质上，它们都是用户的输入，只不过输入的部分不同，一个在URL路径部分，另一个在参数部分。要访问一篇博客文章，这两种URL设计都是可以的

- 通过@PathVariable，例如/blogs/1
- 通过@RequestParam，例如blogs?blogId=1

**使用选择方式：**

1、当URL指向的是某一具体业务资源（或资源列表），例如博客，用户时，使用@PathVariable

2、当URL需要对资源或者资源列表进行过滤，筛选时，用@RequestParam

一旦我们在方法中定义了@RequestParam变量，如果访问的URL中不带有相应的参数，就会抛出异常——这是显然的，Spring尝试帮我们进行绑定，然而没有成功。但有的时候，参数确实不一定永远都存在，这时我们可以通过定义required属性：

```java
@RequestParam(value = "id", required = false)
```

当然，在参数不存在的情况下，可能希望变量有一个默认值：

```java
@RequestParam(value = "id", required = false, defaultValue = "0")
```



## yaml解析

可以注入到配置类中

```yml
#普通的key-value
name: chenbin

# 对象

student:
	name: chenbin
	age:  3
	
# 行内写法
student: {name: chenbin,age: 3}

#数组
pets:
	- cat
	- dog
	- pig

pets: {cat,dog,pig}

```

yaml也可以保存对应的对象:

```yaml
person:
	name: chenbin
	age: 3
	happy: false
	birth: 1998/02/04
	list:
		- code
		- music
		- girl
	dog:
		name: hehe
		age: 2
```

可以在一个pojo类上面通过`@configurationProperties(prefix = ""person)`将上面所写是yaml 导入到对应的pojo。但是导入值的时候，需要所有的属性都对应起来

![image-20200613135533920](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200613135533920.png)

## 配置文件位置及多环境配置

配置文件位置

1. file:./config/
2. file:./
3. classpath:/config/
4. classpath:/

以上文件位置 等级 从高到低

**springboot的多环境配置**

可以在默认环境中配置对应的 properties文件,有以下三个环境

- appllication.properties
- application-dev.properties
- application-test.properties

只需要在默认环境下application.properties 中指定

spring.profiles.active=dev   即可 指定application-dev.properties 文件为配置文件

==yaml一个文件，可以指定多个配置，不用新建三个文件==

**在配置文件中能配置的东西，都存在一个固有的规律  -->  xxxAutoConfiguration   xxxProperties   配置文件绑定，我们就可以使用自定义的配置了。**

> 自动装配的原理

- springBoot启动会加载大量的自动配置类
- 我们看我们需要的功能有没有在SpirngBoot默认写好的自动配置类当中
- 我们再来看这个自动配置类中到底配置了哪些组件;(只要我们要用的组件存在其中，我们就不需要再手动配置了)
- 给容器中自动配置类添加组件的时候，会从properties类中获取某些属性，我们只需要再配置文件中指定这些属性的值即可；
- xxxAutoConfiguration:自动配置类：给容器中添加组件
- xxxProperties：封装配置文件中相关属性

## SpringBoot web开发

**要解决的问题：**

- 导入静态资源
- 首页
- jsp，模板引擎Thymealeaf
- 配置扩展SpringMVC
- 增删改查
- 拦截器
- 国际化！

### **静态资源问题**

静态资源目录包含以下的目录

- classpath:META-INF/resources/
- classpath:/resources/
- classpath:/static/
- classpath:/public/

以上目录优先级 由上往下排列

**总结:**

1. 在springboot，我们可以使用以下方式处理静态资源
   1. webjars   localhost:8080/webjars/
   2. public ,static , /** ,  resouces       localhost:8080/
2. 优先级: resouces>static(默认)>public

### 首页如何定制

在templates目录下的所有文件 只能通过controller跳转

导入对应的thymeleaf，只需要导入对应的依赖就行了。

**自动装配**：只需要实现WebMvcConfigurer 就可以定义一个自己的自动装配的类了。

![image-20200613153621196](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200613153621196.png)

Springboot在自动配置很多组件的时候，先看容器中有没有用户自己配置的(如果用户自己配置@bean)，如果有就用用户配置的，如果没有就用自动配置的；如果有些组件可以存在多个，比如我们的视图解析器，就将用户配置的和组件默认的组合起来！、

#### 自动配置取代首页跳转配置

![image-20200613160925223](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200613160925223.png)

以上配置可以取代@Controller里面的默认配置 

![image-20200613160956968](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200613160956968.png)

register可以添加多个，可以取代多个访问跳转到同一个连接。

1. 首页配置：注意点，所有的页面的静态资源都需要使用thymeleaf接管：@{}
2. 页面国际化(i18n): internationalization i++++n



#### 配置国际化

1. 编写对应的properties文件，原文-中文-英文
2. 在application.peroperties中配置

```properties
# 我们的配置文件的真实位置
spring.message.basename=i18n.login
```

3. 在前端text中设置中，从配置文件中取对应的值
   1. 首先需要配置i18n文件
   2. 我们如果需要在项目中进行按钮自动切换，我们需要自定义一个组件LocalRe'solver
   3. 记得将自己写的组件配置到spring容器 @Bean
   4. #{}

判断前端传过来的`l`参数，在LocalResolver中设置对应的语言。

```java
public class MyLocaleResolver implements LocaleResolver {
    @Override
    public Locale resolveLocale(HttpServletRequest request) {
        //获取请求
        String language=request.getParameter("l");
        Locale locale=Locale.getDefault();//如果没有就使用默认的
        //如果请求的链接携带了国际化的参数
        if(!StringUtils.isEmpty(language)){
            //zh_CN
            String[] split=language.split("_");
            //国家，地区
            locale=new Locale(split[0],split[1]);
        }
        return locale;
    }

    @Override
    public void setLocale(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Locale locale) {

    }
}
```

编写完以上的类之后需要将这个类添加进自己写的MyMvcConfig类中@bean 才可生效

```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("index");
        registry.addViewController("/index.html").setViewName("index");
        registry.addViewController("/main.html").setViewName("dashboard");
    }
    //返回自定义的国际化
    @Bean
    public LocaleResolver localeResolver(){
        return new MyLocaleResolver();
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginHandlerInterceptor())
                .addPathPatterns("/**")
                .excludePathPatterns("/index.html","/","/user/login","/css/**","/img/**","/js/**");
    }
}
```

#### 拦截器的实现

1. 首先编写一个拦截器类,实现HandlerInterceptor接口

```java
// 编写拦截器
public class LoginHandlerInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //登录成功之后，是有用户的session的
        Object loginUser = request.getSession().getAttribute("loginUser");
        //没有session登录失败
        if (loginUser==null){
            request.setAttribute("msg","没有权限,请先登录!");
            request.getRequestDispatcher("/index.html").forward(request,response);
            return false;
        }else{
            return true;
        }
    }
}
```

2. 在自己的配置类中，将我们需要的拦截器类添加进去，设置过滤，拦截请求

```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("index");
        registry.addViewController("/index.html").setViewName("index");
        registry.addViewController("/main.html").setViewName("dashboard");
    }
    //返回自定义的国际化
    @Bean
    public LocaleResolver localeResolver(){
        return new MyLocaleResolver();
    }

    ///!!!!!!!!!!!!这里
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginHandlerInterceptor())
                .addPathPatterns("/**")
                .excludePathPatterns("/index.html","/","/user/login","/css/**","/img/**","/js/**");
    }
}
```

**前端页面**

可以提取公共页面。

	1. th:fragment="sidebar"
 	2. th:replace="~{commons/commons::topbar}"
 	3. 如果要传递参数，可以直接传参

前端框架：**x-admin**

### 配置数据库连接

1. 在application.peroperties 文件中配置数据库相关的配置
2. @Autowired注入DataSource 就可以使用 数据库连接了。
3. 也可以直接注入JdbcTemplate，来获取数据库操作对象

xxx Template：SpringBoot已经配置哈皮模板bean，拿来即用

jdbcTemplate

redisTemplate

**配置Druid数据源**

只需要在application.properties里面添加一行配置

spring.datasource.type = com.alibaba.druid.pool.DruidDataSource

以下是Druid默认的配置

![image-20200613220150273](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200613220150273.png)

而且使用Druid的时候记得导入log4j的jar

```xml
 <dependency>
            <groupId>log4</groupId>
            <artifactId>log4j</artifactId>
			<version>1.2.17</version>
</dependency>
```

添加入Druid的时候可以添加直接的配置文件

配置登录查看数据库操作的账号和密码

initParameters.put("allow",""): 后面为空是允许所有人访问。记得注入容器中，就可以登录Druid内置网站查看sql信息

//因为Springboot内置了servlet容器，所以没有web.xml文件，替代方法：ServletRegisteationBean

![df874f443ec23739b5f4602e7fe7820](C:\Users\晨边\AppData\Local\Temp\WeChat Files\df874f443ec23739b5f4602e7fe7820.jpg)

## Mybatis

mybatis扫描包：

① 可以直接在启动类上添加@MapperScan("com.iscas.Mapper") 直接要扫描的包

② 可以直接在需要扫描的Mapper接口上面添加@Mapper注解

1. 导入包
2. 配置文件
3. mybatis配置
4. 编写sql
5. service层调用dao层
6. controller 调用 service层

## SpringSecurity(  安全)

 在web开发中，安全第一！过滤器，拦截器

- shiro，springsecurity：很像，除了类不一样，名字不一样
- 认证，授权(vip1,vip2,vip3)





- 功能权限
- 访问权限
- 菜单权限
- 。。拦截器，过滤器：大量的原生代码~ 冗余

MVC - SPRING -SPRINGBOOT    框架思想

**简介**

Spring Security 是针对Spring项目的安全框架，也是Spring底层安全模块默认的技术选型，他可以实现强大的Web安全控制，对于安全控制，我们仅需要引入spring-boot-starter-security模块。进行少量的配置，即可实现强大的安全管理！

记住几个类：

- WebSecurityConfigureAdapter:自定义Security策略
- AuthenticationManagerBuilder:自定义认证策略
- @EnableWebSecurity:开启WebSecurity模式

SPring Security 的两个主要目标是"认证"和"授权" (访问控制)

### 配置用户认证和授权 配置类

```java
package com.iscas.config;

import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/6/14 0:40
 * @Version V1.0
 **/
//AOP:  拦截器！
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    //链式编程
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //首页所有人可以访问，功能页只有对应有权限的人才能访问

        http.authorizeRequests().antMatchers("/").permitAll()
        .antMatchers("/level1/**").hasRole("vip1")
        .antMatchers("/level2/**").hasRole("vip2")
        .antMatchers("/level3/**").hasRole("vip3");

        //没有权限默认会到登录页面，需要开启登录页面 ---->  跳转到/login
        http.formLogin().loginPage("/toLogin").usernameParameter("user").passwordParameter("pwd").loginProcessingUrl("/login");

        //防止网站攻击 get
        http.csrf().disable();//关闭csrf功能

        //开启了注销功能,退出后 转到首页
        http.logout().logoutSuccessUrl("/");

        //开启记住我功能,cookies 默认保存两周,自定义接收前端的参数
        http.rememberMe().rememberMeParameter("remember");
    }

    //认证 ，springboot 2.1.x 可以直接使用
    //密码编码: passwordEncoder
    // 在Spring security 5.0+ 新增了很多加密方法
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //定制一些认证的规则
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
            .withUser("chenbin").password(new BCryptPasswordEncoder().encode("123456")).roles("vip1","vip3")
            .and()
            .withUser("root").password(new BCryptPasswordEncoder().encode("123")).roles("vip1","vip2","vip3")
            .and()
            .withUser("customer").password(new BCryptPasswordEncoder().encode("123")).roles("vip1");

    }
}
```

**配置数据库用户的认证和授权，jdbc**

![image-20200614005344521](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200614005344521.png)

spring版本要2.0.9以下才可以呢

## Shiro简介

### 什么是Shiro

- Apache Schiro是一个Java的安全(权限)框架
- Shiro可以非常容易的开发出足够好用的应用，不仅可以用在javase环境
- Shrio可以完成，认证，授权，加密，会话管理，web继承，缓存

```java
///获得当前用户信息:
Subject currentUser = SecurityUtils.getSubject();
//通过当前用户对象获得session
Session session = currentUser.getSession();
  //判断当前的用户是否被认证
currentUser.isAuthenticated();
// 当前用户的角色
currentUser.hasRole("schwartz");
//当前用户的权限
currentUser.isPermitted("winnebago:drive:eagle5")
```

### springBoot中集成

打扰了！！。没完成

## Swagger

**简介**

开发中产生的问题：

- 前后端集成联调，前后端人员无法做到及时协商，今早解决，最终导致问题集中爆发。

解决方案：

- 首先制订一个schema[计划的提纲]，实时更新最新的API，降低集成的风险；
- 早些年：制订word计划文档；
- 前后端分离：
  - 前端测试后端接口：postman
  - 后端提供接口：需要实时更新最新的消息及改动

**swagger：世界上最流行的API框架**

- 号称世界上最流行的API框架
- ResuFul Api 文档在线自动生成工具 = > Api文档与API定义同步更新
- 直接运行，可以在线测试API接口
- 支持多种语言

官网：https://swagger.io/

在项目中使用Swagger需要使用springbox；

- swagger2
- ui

### springboot 集成swagger

1. 新建一个springboot-web项目
2. 导入相关依赖

```xml
<!--        导入swagger依赖-->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.6.1</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.6.1</version>
        </dependency>
```

3. 编写一个helloworld工程
4. 配置swagger = 》 config

```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {

}
```

5. 测试运行！http://localhost:8844/swagger-ui.html

![image-20200614154031106](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200614154031106.png)

### 配置Swagger

Swagger的bean实例Docket；

```java
package com.iscas.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

import java.util.ArrayList;

import static springfox.documentation.service.ApiInfo.DEFAULT_CONTACT;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/6/14 15:37
 * @Version V1.0
 **/
@Configuration
@EnableSwagger2
public class SwaggerConfig {

    //配置swagger 的Docket的bean实例；
    @Bean
    public Docket docket(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo());
    }

    //配置swagger信息=apiInfo
    private ApiInfo apiInfo(){
        //作者信息
        Contact contact = new Contact("陈彬", "https://www.baidu.como", "1501695397@qq.com");

        return new ApiInfo(
                "晨边的swagger Api文档",
                "很酷哦！",
                "v1.0",
                "https://www.baidu.com",
                contact,
                "Apache 2.0",
                "http://www.apache.org/licenses/LICENSE-2.0");
    }

}
```

### Swagger配置扫描接口

Docket select()

```java
 @Bean
    public Docket docket(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
         	   .enable(false)	//关闭，默认是true  false为比启动swagger
                //RequestHandlerSelectors 配置要扫描的接口的方式
                //basePackage：制订要扫描的包
                //any() : 扫描全部
                //non(): 都不扫描
                //withClassAnnotation: 扫描类上的注解，参数是一个注解的反射对象
                //withMethodAnnotation   扫描方法上的注解
                .apis(RequestHandlerSelectors.basePackage("com.iscas.controller"))
                //paths 过滤什么路径
                .paths(PathSelectors.ant("/chen/**"))
                .build();   //工厂模式
    }
```

我只希望Swagger在生产环境中使用，在发布的时候不使用

- 判断是不是生产 环境			flag = false
  - 注入enable（）			

```java
//配置swagger 的Docket的bean实例；
    @Bean
    public Docket docket(Environment environment){

        // 设置要显示的swagger环境
        Profiles profiles = Profiles.of("dev","test");

        //获取项目的环境:监听是否处在自己设定的环境中。
        boolean flag = environment.acceptsProfiles(profiles);


        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .enable(flag)
                .select()
                //RequestHandlerSelectors 配置要扫描的接口的方式
                //basePackage：制订要扫描的包
                //any() : 扫描全部
                //non(): 都不扫描
                //withClassAnnotation: 扫描类上的注解，参数是一个注解的反射对象
                //withMethodAnnotation   扫描方法上的注解
                .apis(RequestHandlerSelectors.basePackage("com.iscas.controller"))
                //paths 过滤什么路径
//                .paths(PathSelectors.ant("/chen/**"))
                .build();   //工厂模式
    }
```

### 配置api的文档分组

```java
.groupName("陈彬");
```

如何配置多个分组，多个Docket实例即可

```java
 @Bean
    public Docket docket1(){
        return new Docket(DocumentationType.SWAGGER_2).groupName("A");
    }

    @Bean
    public Docket docket2(){
        return new Docket(DocumentationType.SWAGGER_2).groupName("B");
    }

    @Bean
    public Docket docket3(){
        return new Docket(DocumentationType.SWAGGER_2).groupName("C");
    }
```

实体类配置：

在controller中的配置

```java
  //Operation 不是放在类上的
    @ApiOperation("Hello控制器类")
    @GetMapping("/hello2")
    public String hello(@ApiParam("用户名") String username){
        return "hello"+username;
    }
  //添加上对应的注释
```

给pojo类添加上对应的注解

```java
@ApiModel("用户实体类")
public class User {

    @ApiModelProperty("用户名")
    private String username;
    @ApiModelProperty("密码")
    private String password;
}
```

**总结：**

1. 我们可以通过swagger给一些比较难理解的属性或者接口，增加注释信息
2. 接口文档实时更新
3. 可以在线测试

Swagger是一个优秀的工具，几乎所有大公司都有使用它

[注意点: 在正式发布的时候，关闭swagger！！ 处于安全考虑，而且节省内存]

## 任务

异步任务-

定时任务-

邮件发送-

**开启异步注解功能**

第一步:在main方法中开启注解功能：`@EnableAsync`

```java
//开启异步注解功能
@EnableAsync
@SpringBootApplication
public class WorkajaxApplication {

    public static void main(String[] args) {
        SpringApplication.run(WorkajaxApplication.class, args);
    }

}
```

在对应要开启注解的方法上添加上`@Async`注解

```java
//告诉spring这是一个异步的方法
    @Async
    public void hello(){
        try{
            Thread.sleep(3000);
        }catch (Exception e){
            e.printStackTrace();
        }

        System.out.println("数据正在处理。。。");

    }
```

邮件发送：

导入xml

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-start-mail</artifactId>
        </dependency>
```

定时器功能：

```xml
TaskScheduler   任务调度者
TaskExecutor	任务执行者

@EnableScheduling	//开启定时功能的注解
@Scheduled		//什么时候执行

cron表达式
```

## SpringBoot整合

SpringBoot操作数据：spring-data jpa jdbc mongodb redis!

SpringData也是和SpringBoot齐名的项目

> 整合测试

说明：springBoot2.x之后，原来使用jedis被替换为lettuce

jedis：采用直连，多个线程操作的话，是不安全的，如果想要避免不安全的，使用jedis pool 连接池！ BIO模式

letture：采用netty，实例可以再多个线程中进行共享，不存在线程不安全的情况！可以减少线程数量，NIO模式

1. 导入依赖

   ```xml
    <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-data-redis</artifactId>
   </dependency>
   ```

2. 配置连接

```properties
#配置redis
spring.redis.host=127.0.0.1
spring.redis.port=6379
```

3. 测试

```java
package com.iscas;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.connection.RedisConnection;
import org.springframework.data.redis.core.RedisTemplate;

@SpringBootTest
class RedisApplicationTests {

    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    void contextLoads() {
        //redisTemplate  操作不同是数据类型，api和我们的指令是一样的
        //opsForValue  操作字符串  类似String
        //opsForList   操作list  类似List
        //opsForSet
        //opsForHash
        //opsForZSet
        //opsForGeo
        //opsForHyperLogLog

        //除了基本的操作，我们常用的方法都开口哟直接通过redisTemplate操作，比如事务，和基本的CRUD
        //获取redis连接对象
//        RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
//        connection.flushDb();
//        connection.flushAll();

        redisTemplate.opsForValue().set("mykey","chenbin");
        System.out.println(redisTemplate.opsForValue().get("mykey"));
    }
}
```

![image-20200614224820281](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200614224820281.png)

在企业中，我们所有的pojo类都会序列化

编写一个直接的redisTemplate

```java
package com.iscas.config;

import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.PropertyAccessor;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/6/14 22:41
 * @Version V1.0
 **/
@Configuration
public class RedisConfig {

    //一个固定的模板，企业中直接使用就行了
    //编写我们自己的 redisTemplate
    //自己定义了一个redistemplate
    @Bean
    @SuppressWarnings("all")
    public RedisTemplate<String,Object> redisTemplate(RedisConnectionFactory factory){
        //我们为了自己开发方便，一般直接使用<String,Object>
        RedisTemplate<String,Object> template = new RedisTemplate<>();
        template.setConnectionFactory(factory);
        //json序列化配置
        Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        
        //String 的序列化
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
        //key 采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        //hash的key采用String的序列化方式
        template.setHashKeySerializer(stringRedisSerializer);
        //value 序列化方式采用jackson
        template.setValueSerializer(jackson2JsonRedisSerializer);
        //hash的value序列化方式采用jackson
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        template.afterPropertiesSet();
        //配置具体的序列化方式
        
        return template;
    }

}
```

## 分布式Doubbo + Zookeeper +SpringBoot

**RPC：**(Remote Procedure Call)：远程过程调用，是一种进程间通信方式。

- 两个核心模块: 通讯，序列化

**Dubbo**
Dubbo采用全Spring配置方式，透明化接入应用，对应用没有任何API侵入，只需要Spring加载Dubbo的配置即可，Dubbo基于Spring的Schema扩展 进行加载。

如果不想使用Spring配置，可以通过API的方式进行调用

**快速启动手册：**http://dubbo.apache.org/zh-cn/docs/user/quick-start.html

![image-20200615092342135](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200615092342135.png)

**服务提供者(Provider):** 暴露服务的服务提供方，服务提供者在启动时，向注册中心注册自己提供的服务

**服务消费者(Consumer):** 调用远程服务的服务消费方，服务消费者在启动时，向注册中心订阅自己所需的服务，服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用

**注册中心(Registry):**注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心基于长连接推送变更数据给消费者，

**监控中心(Monitor):**服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。

> zookeeper:zoo+keeper:hadoop hive~

dubbo-admin: 是一个监控管理后台  查看我们注册了那些服务，那些服务被消费了

zookeeper：注册中心

Dubbo：jar包~

### 整合项目

 导入相关依赖：两个服务器都需要导入

```xml
<!--         导入相关的依赖-->
        <dependency>
            <groupId>io.dubbo.springboot</groupId>
            <artifactId>spring-boot-starter-dubbo</artifactId>
            <version>1.0.0</version>
        </dependency>

        <dependency>
            <groupId>com.github.sgroschupf</groupId>
            <artifactId>zkclient</artifactId>
            <version>0.1</version>
        </dependency>

        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>2.12.0</version>
        </dependency>

        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.4.14</version>
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
 <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>dubbo</artifactId>
            <version>2.6.2</version>
        </dependency>
```

**提供者**

- 配置提供者的properties文件,需要设置服务注册的包

```properties
server.port=8001

#服务应用名字
dubbo.application.name=provider-server
#注册中心地址
dubbo.registry.address=zookeeper://127.0.0.1:2181
#哪些服务要被注册
dubbo.scan.base-package=com.iscas.service
```

需要在启动类上加上`@EnableDubbo` 注解

```java
package com.iscas;

import com.alibaba.dubbo.config.spring.context.annotation.EnableDubbo;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@EnableDubbo
public class ProviderServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(ProviderServerApplication.class, args);
    }

}
```

service类需要继承一个接口，方便接受方调用。并且将该类添加进容器@Component

再加上@Service注解：--->    注意该注解的包：==com.alibaba.dubbo.config.annotation.Service;==

```java
package com.iscas.service;

import com.alibaba.dubbo.config.annotation.Service;
import org.springframework.stereotype.Component;
// zookeeper:服务注册与发现
/**
 * @Author 晨边#CB
 * @Date:created in  2020/6/15 10:38
 * @Version V1.0
 **/
@Service
@Component  //使用了Dubbo后尽量不要使用@Service注解
public class TicketServiceImpl implements TicketService {

    @Override
    public String getTicket() {
        return "hello chenbin!";
    }
}
```

**接收者**

添加配置文件properties

```properties
server.port=8002

#消费者去哪里拿服务需要暴露自己的名字
dubbo.application.name = consumer-server
#注册中心的地址
dubbo.registry.address = zookeeper://127.0.0.1:2181
```

实现同样的接口：(用于接收提供者那边的接口)

```java
public interface TicketService {

    public String getTicket();

}
```

接收方的@Service是用于放入容器中的==org.springframework.stereotype.Service;==

在需要调用的提供者接口的上面，添加@Reference就可以调用接口了。

```java
package com.iscas.service;

import com.alibaba.dubbo.config.annotation.Reference;
import org.springframework.stereotype.Service;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/6/15 10:43
 * @Version V1.0
 **/
@Service    //放入到容器中
public class UserService {

    //想拿到provider-server提供的票，要去注册中心拿到服务
    @Reference  //引用, Pom坐标，可以定义路径相同的接口名
    TicketService ticketService;

    public void buyTicket(){
        String ticket = ticketService.getTicket();
        System.out.println("在注册中心拿到一张票=>"+ticket);
    }

}
```

启动zookeeper  再启动Dubbo，以及启动提供者类，启动接收者的测试类就可以调用了！

![image-20200615150538889](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200615150538889.png)



**步骤：**

前提：zookeeper服务已开启

1. 提供者提供服务
   1. 导入依赖
   2. 配置注册中心的地址，以及服务发现名，和要扫描的包
   3. 在想要被注册的服务上面增加一个注解@Service
2. 消费者如何消费
   1. 导入依赖
   2. 配置注册中心的地址，配置自己的服务名
   3. 从远程注入服务























