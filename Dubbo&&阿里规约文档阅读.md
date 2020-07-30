# Dubbo文档阅读

Dubbo 社区目前主力维护的有 2.6.x 和 2.7.x 两大版本

**Dubbo解决的方面:**

![image](http://dubbo.apache.org/docs/zh-cn/user/sources/images/dubbo-service-governance.jpg)

- **当服务越来越多时，服务 URL 配置管理变得非常困难，F5 硬件负载均衡器的单点压力也越来越大。**
- **当进一步发展，服务间依赖关系变得错踪复杂，甚至分不清哪个应用要在哪个应用之前启动，架构师都不能完整的描述应用的架构关系。**
- **服务的调用量越来越大，服务的容量问题就暴露出来，这个服务需要多少机器支撑？什么时候该加机器？**

**RPC架构**

![dubbo-architucture](http://dubbo.apache.org/docs/zh-cn/user/sources/images/dubbo-architecture.jpg)

| 节点        | 角色说明                               |
| ----------- | -------------------------------------- |
| `Provider`  | 暴露服务的服务提供方                   |
| `Consumer`  | 调用远程服务的服务消费方               |
| `Registry`  | 服务注册与发现的注册中心               |
| `Monitor`   | 统计服务的调用次数和调用时间的监控中心 |
| `Container` | 服务运行容器                           |

## 快速构建

### 提供者

**定义服务接口**

DemoService.java

```java
package org.apache.dubbo.demo;

public interface DemoService {
    String sayHello(String name);
}
```

**在服务提供方实现接口**

DemoServiceImpl.java

```java
package org.apache.dubbo.demo.provider;
 
import org.apache.dubbo.demo.DemoService;
 
public class DemoServiceImpl implements DemoService {
    public String sayHello(String name) {
        return "Hello " + name;
    }
}
```

**用Spring配置声明暴露服务(如果是springboot可以在properties文件里面设置**

provider.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
    xsi:schemaLocation="http://www.springframework.org/schema/beans        http://www.springframework.org/schema/beans/spring-beans-4.3.xsd        http://dubbo.apache.org/schema/dubbo        http://dubbo.apache.org/schema/dubbo/dubbo.xsd">
 
    <!-- 提供方应用信息，用于计算依赖关系 -->
    <dubbo:application name="hello-world-app"  />
 
    <!-- 使用multicast广播注册中心暴露服务地址 -->
    <dubbo:registry address="multicast://224.5.6.7:1234" />
 
    <!-- 用dubbo协议在20880端口暴露服务 -->
    <dubbo:protocol name="dubbo" port="20880" />
 
    <!-- 声明需要暴露的服务接口 -->
    <dubbo:service interface="org.apache.dubbo.demo.DemoService" ref="demoService" />
 
    <!-- 和本地bean一样实现服务 -->
    <bean id="demoService" class="org.apache.dubbo.demo.provider.DemoServiceImpl" />
</beans>
```

**加载Spring配置**

Provider.java

```java
import org.springframework.context.support.ClassPathXmlApplicationContext;
 
public class Provider {
    public static void main(String[] args) throws Exception {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(new String[] {"http://10.20.160.198/wiki/display/dubbo/provider.xml"});
        context.start();
        System.in.read(); // 按任意键退出
    }
}
```

如果是springboot项目可以使用@EnabelDubbo注解来加载

### 消费者

**通过Spring配置远程引用远程服务**

consumer.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
    xsi:schemaLocation="http://www.springframework.org/schema/beans        http://www.springframework.org/schema/beans/spring-beans-4.3.xsd        http://dubbo.apache.org/schema/dubbo        http://dubbo.apache.org/schema/dubbo/dubbo.xsd">
 
    <!-- 消费方应用名，用于计算依赖关系，不是匹配条件，不要与提供方一样 -->
    <dubbo:application name="consumer-of-helloworld-app"  />
 
    <!-- 使用multicast广播注册中心暴露发现服务地址 -->
    <dubbo:registry address="multicast://224.5.6.7:1234" />
 
    <!-- 生成远程服务代理，可以和本地bean一样使用demoService -->
    <dubbo:reference id="demoService" interface="org.apache.dubbo.demo.DemoService" />
</beans>
```

**加载Spring配置，并调用远程服务**

Consumer.java

```java
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.apache.dubbo.demo.DemoService;
 
public class Consumer {
    public static void main(String[] args) throws Exception {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(new String[] {"http://10.20.160.198/wiki/display/dubbo/consumer.xml"});
        context.start();
        DemoService demoService = (DemoService)context.getBean("demoService"); // 获取远程服务代理
        String hello = demoService.sayHello("world"); // 执行远程方法
        System.out.println( hello ); // 显示调用结果
    }
}
```

**需要用到依赖**

- netty-all 4.0.35.Final
- mina: 1.1.7
- grizzly: 2.1.4
- httpclient: 4.5.3
- hessian_lite: 3.2.1-fixed
- fastjson: 1.2.31
- zookeeper: 3.4.9
- jedis: 2.9.0
- xmemcached: 1.3.6
- hessian: 4.0.38
- jetty: 6.1.26
- hibernate-validator: 5.4.1.Final
- zkclient: 0.2
- curator: 2.12.0
- cxf: 3.0.14
- thrift: 0.8.0
- servlet: 3.0 [[5\]](http://dubbo.apache.org/zh-cn/docs/user/dependencies.html#fn5)
- validation-api: [1.1.0.GA](http://1.1.0.ga/) [[5:1\]](http://dubbo.apache.org/zh-cn/docs/user/dependencies.html#fn5)
- jcache: 1.0.0 [[5:2\]](http://dubbo.apache.org/zh-cn/docs/user/dependencies.html#fn5)
- javax.el: 3.0.1-b08 [[5:3\]](http://dubbo.apache.org/zh-cn/docs/user/dependencies.html#fn5)
- kryo: 4.0.1
- kryo-serializers: 0.42
- fst: 2.48-jdk-6
- resteasy: 3.0.19.Final
- tomcat-embed-core: 8.0.11
- slf4j: 1.7.25
- log4j: 1.2.16

## 注解配置

### 提供方服务

**service注解暴露服务**

```java
@Service
public class AnnotationServiceImpl implements AnnotationService {
    @Override
    public String sayHello(String name) {
        return "annotation: hello, " + name;
    }
}
```

**增加应用共享配置**

```properties
# dubbo-provider.properties
dubbo.application.name=annotation-provider
dubbo.registry.address=zookeeper://127.0.0.1:2181
dubbo.protocol.name=dubbo
dubbo.protocol.port=20880
```

**指定Spring扫描路径**

```java
@Configuration
@EnableDubbo(scanBasePackages = "org.apache.dubbo.samples.simple.annotation.impl")
@PropertySource("classpath:/spring/dubbo-provider.properties")
static public class ProviderConfiguration {
       
}
```

### 消费方服务

**`Reference`注解引用服务**

```java
@Component("annotationAction")
public class AnnotationAction {

    @Reference
    private AnnotationService annotationService;
    
    public String doSayHello(String name) {
        return annotationService.sayHello(name);
    }
}
```

**增加应用共享配置**

```properties
# dubbo-consumer.properties
dubbo.application.name=annotation-consumer
dubbo.registry.address=zookeeper://127.0.0.1:2181
dubbo.consumer.timeout=3000
```

**制订Spring扫描路径**

```java
@Configuration
@EnableDubbo(scanBasePackages = "org.apache.dubbo.samples.simple.annotation.action")
@PropertySource("classpath:/spring/dubbo-consumer.properties")
@ComponentScan(value = {"org.apache.dubbo.samples.simple.annotation.action"})
static public class ConsumerConfiguration {

}
```

**调用服务**

```java
public static void main(String[] args) throws Exception {
    AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(ConsumerConfiguration.class);
    context.start();
    final AnnotationAction annotationAction = (AnnotationAction) context.getBean("annotationAction");
    String hello = annotationAction.doSayHello("world");
}
```

## 推荐用法

### Provider端

在 Provider 端尽量多配置 Consumer 端属性

- 作服务的提供方，比服务消费方更清楚服务的性能参数，如调用的超时时间、合理的重试次数等
- 在 Provider 端配置后，Consumer 端不配置则会使用 Provider 端的配置，即 Provider 端的配置可以作为 Consumer 的缺省值 [[1\]](http://dubbo.apache.org/zh-cn/docs/user/recommend.html#fn1)。否则，Consumer 会使用 Consumer 端的全局设置，这对于 Provider 是不可控的，并且往往是不合理的

示例：

```xml
<dubbo:service interface="com.alibaba.hello.api.HelloService" version="1.0.0" ref="helloService"
    timeout="300" retries="2" loadbalance="random" actives="0" />
 
<dubbo:service interface="com.alibaba.hello.api.WorldService" version="1.0.0" ref="helloService"
    timeout="300" retries="2" loadbalance="random" actives="0" >
    <dubbo:method name="findAllPerson" timeout="10000" retries="9" loadbalance="leastactive" actives="5" />
<dubbo:service/>
```

建议在Provider 端 配置的Consumer端属性有：

1. `timeout`：方法调用的超时时间
2. `retries`：失败重试次数，缺省是 2 [[2\]](http://dubbo.apache.org/zh-cn/docs/user/recommend.html#fn2)
3. `loadbalance`：负载均衡算法 [[3\]](http://dubbo.apache.org/zh-cn/docs/user/recommend.html#fn3)，缺省是随机 `random`。还可以配置轮询 `roundrobin`、最不活跃优先 [[4\]](http://dubbo.apache.org/zh-cn/docs/user/recommend.html#fn4) `leastactive` 和一致性哈希 `consistenthash` 等
4. `actives`：消费者端的最大并发调用限制，即当 Consumer 对一个服务的并发调用到上限后，新调用会阻塞直到超时，在方法上配置 `dubbo:method` 则针对该方法进行并发限制，在接口上配置 `dubbo:service`，则针对该服务进行并发限制



```xml
<dubbo:protocol threads="200" /> 
<dubbo:service interface="com.alibaba.hello.api.HelloService" version="1.0.0" ref="helloService"
    executes="200" >
    <dubbo:method name="findAllPerson" executes="50" />
</dubbo:service>
```

### 配置管理信息

目前有负责人信息和组织信息用于区分站点。以便于在发现问题时找到服务对应负责人，建议至少配置两个人以便备份。负责人和组织信息可以在运维平台 (Dubbo Ops) 上看到。

**在应用层面配置负责人、组织信息：**

```xml
<dubbo:application owner=”ding.lid,william.liangf” organization=”intl” />
```

**在服务层面（服务端）配置负责人：**

```xml
<dubbo:service owner=”ding.lid,william.liangf” />
```

**在服务层面（消费端）配置负责人：**

```xml
<dubbo:reference owner=”ding.lid,william.liangf” />
```

==使用xml配置信息==

```xml
#应用名 dubbo.application.name
<dubbo:application name="myalibaba" >
    
#注册中心地址 dubbo.registry.address
<dubbo:registry address="11.22.33.44:9090" >
    
#调用超时 dubbo.service.*.timeout
#可以在多个配置项设置超时 timeout，由上至下覆盖（即上面的优先）[5]，其它的参数（retries、loadbalance、actives等）的覆盖策略与 timeout 相同。示例如下：
#提供者端特定方法的配置
<dubbo:service interface="com.alibaba.xxx.XxxService" >
    <dubbo:method name="findPerson" timeout="1000" />
</dubbo:service>
    
#提供者端特定接口的配置
<dubbo:service interface="com.alibaba.xxx.XxxService" timeout="200" />
    
#服务提供者协议 dubbo.service.protocol、服务的监听端口 dubbo.service.server.port
<dubbo:protocol name="dubbo" port="20880" />
    
#服务线程池大小 dubbo.service.max.thread.threads.size
<dubbo:protocol threads="100" />
    
#消费者启动时，没有提供者是否抛异常 alibaba.intl.commons.dubbo.service.allow.no.provider
<dubbo:reference interface="com.alibaba.xxx.XxxService" check="false" />
```

# 阿里开发手册

命名风格

- 类名使用UpperCamelCase 风格：

  `正例`：ForceCode / UserDO / HtmlDTO / XmlService / TcpUdpDeal / TaPromotion

- 方法名、参数名、成员变量、局部变量都统一使用 lowerCamelCase 风格。

  `正例`： localValue / getHttpMessage() / 

- 包名统一使用小写；

可变参数：

```java
public class test {
    public static void main(String[] args) {
        String[] a = {"aa","bb"};
        haha(a);
        System.out.println("------------------------");
        haha("aa","aaa","aaaa");
    }


    public static void  haha(String... a){
        for (String s : a) {
            System.out.println(s);
        }
    }
}
```

表示日期和时间的格式如下所示：

new SimpleDateFormat("yyyy-MM-dd HH:mm:ss")

**foreach更改list的第一个值**

```java
 List<String> list = new ArrayList<>();
        list.add("1");
        list.add("2");
        list.add("3");

        for (String s : list) {
            if ("1".equals(s)){
                list.remove(s);
            }
        }
```

解决方案:

```java
List<String> list = new ArrayList<>();
list.add("1");
list.add("2");
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
String item = iterator.next();
if (删除元素的条件) {
iterator.remove();
}
}
```

高度注意 Map 类集合 K/V 能不能存储null 值的情况，如下表格：

![image-20200615224957123](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200615224957123.png)

**集合的有序性和稳定性**

ArrayList 是 order/unsort；HashMap 是 unorder/unsort；TreeSet 是 order/sort。



















