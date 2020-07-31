# 集群Linux环境搭建

## 注意事项

- **确保任务管理器中的服务vmware开启状态**

![image-20200621222723567](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200621222723567.png)

- **确认好VmWare生成的网关地址**

![image-20200621222905879](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200621222905879.png)

- **确认VmNet8网卡已经配置好的IP地址和DNS**

![image-20200621223226962](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200621223226962.png)

- **在Vmware中安装虚拟机并将虚拟机复制三份，放到大小至少有100G的硬盘中。并在虚拟机中打开**

![image-20200621233611733](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200621233611733.png)

安装好虚拟机(**桥接模式**)

复制三个虚拟机

![image-20200621233707817](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200621233707817.png)

![image-20200621233754599](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200621233754599.png)

- **分别给三台虚拟机设置内存**

==每台机器的内存：===(总内存-2) %3

​	![image-20200621234345369](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200621234345369.png)

- **每台虚拟机更改mac地址**

c

![image-20200621235858627](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200621235858627.png)

以上设置三个mac

vi /etc/sysconfig/network-scripts/ifcfg-ens33

![image-20200622002731237](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200622002731237.png)

vi /etc/sysconfig/network

修改主机名

![image-20200622092138364](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200622092138364.png)

配置node dns  --->   vi /etc/hosts![image-20200622095043957](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200622095043957.png)

- **关闭防火墙**

1. systemctl stop firewalld.service #停止baifirewall
2. systemctl disable firewalld.service #禁止firewall开机启du动
3. firewall-cmd --state #查看默认防火墙状态（关zhi闭后显示notrunning，开启后显示running）

**关闭selinux**

![image-20200622100209035](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200622100209035.png)

进入：vi   /etc/selinux/config

![image-20200622100355865](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200622100355865.png)

## 免密登录

1. ssh-keygen -t rsa   ---->    三台机子都做以下的操作

![image-20200622100918932](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200622100918932.png)

2. 拷贝公钥到同一台机器

三台机子将拷贝公钥到同一台机器

ssh-copy-id node01

3. 复制第一台机器的认证到其他的机器

scp /root/.ssh/authorized_keys node02:/root/.ssh

scp /root/.ssh/authorized_keys node03:/root/.ssh

> 相互登录之间的命令可以为: ssh node01     |   ssh node02    |    ssh  node03
>
> ==切记  配置ip的之后不要多个空格！！！我靠！！！==

## 时钟同步

1. yun install -y ntp
2. crontab -e
3. */1 * * * * /usr/sbin/ntpdate ntp4.aliyun.com;  
4. 以上命令在三个文件中都执行

## 安装jdk

查看关于java的组件

>  rpm -qa | grep java

删除查到的组件 关于java

> rpm -e java-1.8.0-openjdk-1.8.0.131-11.b12.el7.x86_64 python-javapackages-3.4.1-11.el7.noarch --nodeps

创建以下两个目录

```shell
[root@localhost ~]# mkdir -p /export/softwares
[root@localhost ~]# mkdir -p /export/servers
```

上传并解压

配置java环境

![image-20200622112931974](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200622112931974.png)

解压并配置环境

进入vi /etc/profile

```shell
export JAVA_HOME=/export/servers/jdk1.8.0_60
export PATH=:$JAVA_HOME/bin:$PATH
```

让文件生效：source /etc/profile

scp -r jdk1.8.0_60/ node02:$PWD( 当前目录)   ---->    发送给另外的主机

## 安装Mysql

1. 安装mysql ：yum install mysql mysql-server mysql-devel

如果安装失败：

```shell
1: wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
 
2: rpm -ivh mysql-community-release-el7-5.noarch.rpm
```

执行完成以上命令后再去安装mysql

2. 启动mysql服务:/etc/init.d/mysqld start
3. 通过mysql安装自带脚本进行设置

```shell
/usr/bin/mysql_secure_installation
```

4. 进入mysql的客户端然后进行授权

启动mysql：/usr/local/mysql/support-files/mysql.server start

## zookeeper

![image-20200622175310567](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200622175310567.png)



![image-20200622175319781](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200622175319781.png)

- 下载并解压zookeeper文件

1. 第一台机器修改配置文件

```shell
cd /export/servers/zooker-3...

cp zoo_sample.cfg  zoo.cfg

mkdir -p /export/servers/zooker-3...
```

vi zoo.cfg

```shell
# 保留多少个快照
autopurge.snapRetainCount=3
# 日志多少个小时请理一次
autopurge.purgeInterval=1
# 集群中服务器地址
server.1=node01:2888:3888
server.2=node02:2888:3888
server.3=node03:2888:3888

dataDir=/export/servers/zookeeper-3.4.14/zkdatas
```

在这个目录：/export/servers/zookeeper-3.4.14/  下创建一个文件夹zkdatas

在里面建一个文件叫做myid     内容为

```shell
1
```

将内容拷贝到其他的地址 拷贝两次

```shell
scp -r /export/servers/zookeeper-3.4.14/ node02:/export/servers/
```

并进到各自的myid里面 修改id值

启动zookeeper

```shell
bin/zkServer.sh start
```

jps命令查看进程 

bin/zkServer.sh status    可以查看当前服务器的状态  是follow 还是leader

> Znode节点类型

- `临时节点:`该节点的生命周期依赖于创建他们的会话，一旦会话结束，临时节点将被自动删除，当然也可以手动删除，临时节点不允许拥有子节点
- `永久节点:`该节点的生命周期不依赖于会话，并且只有在客户端显示执行删除操作的时候，它们才能被删除

连接zookeeper并启动客户端

bin/zkCli.sh -server node01:2181

![image-20200622220021010](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200622220021010.png)

操作实例：

**列出path下的所有Znode**

ls  / 

**创建永久节点**

create /hello world

**创建临时节点(随着会话结束而结束)**

create -e /abc 123

![image-20200622220836691](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200622220836691.png)

**Zookeeper的watch机制**

- 通知类似于数据库中的触发器，对某个Znode设置Watcher，当Znode发生变化的时候，WatchManager 会调用Watcher
- 当Znode发生删除，修改，创建，子节点修改的时候，对于Watcher会得到通知
- Watcher的特点
  - 一次性触发一个Watcher只会触发一次，如果需要继续监听，则需要再次添加Watcher
  - 事件封装：Watcher 得到的事件是被封装过的，包括三个内容：keeperState，eventType，path

![image-20200622222641789](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200622222641789.png)

在一个服务器上添加watch :  get /hello watch

在另一个服务器上修改/hello节点 :   set /hello abc

会看到添加了watch的服务器监听到了

==WatchedEvent state:SyncConnected type:NodeDataChanged path:/hello==

![1923052eb5d0d50bb5c807d664b31ca](C:\Users\晨边\AppData\Local\Temp\WeChat Files\1923052eb5d0d50bb5c807d664b31ca.jpg)

JavaApi调用zookeeper的框架Curator

导入几个包:

- curator-framework:zookeeper的底层api的一些封装 
- curator-recipes：封装了一些高级特性，如：cache事件监听，选举，分布式锁，分布式计算器等

curator版本：2.12.0     Zookeeper版本：3.4.x

导入对应pom文件

```pom
 <dependencies>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>2.12.0</version>
        </dependency>

        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-recipes</artifactId>
            <version>2.12.0</version>
        </dependency>

        <dependency>
            <groupId>com.google.collections</groupId>
            <artifactId>google-collections</artifactId>
            <version>1.0</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-simple</artifactId>
            <version>1.7.25</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.2</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

```java
package cn.isacas;

import org.apache.curator.RetryPolicy;
import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.framework.api.CreateModable;
import org.apache.curator.framework.recipes.cache.ChildData;
import org.apache.curator.framework.recipes.cache.TreeCache;
import org.apache.curator.framework.recipes.cache.TreeCacheEvent;
import org.apache.curator.framework.recipes.cache.TreeCacheListener;
import org.apache.curator.retry.ExponentialBackoffRetry;
import org.apache.zookeeper.CreateMode;
import org.junit.Test;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/6/22 22:46
 * @Version V1.0
 **/

public class zookeeper_api {

    /**
     * 节点的watch机制
     */
    @Test
    public void watchZnode() throws Exception {
        //1.定制一个重试策略
        ExponentialBackoffRetry retrypolicy = new ExponentialBackoffRetry(1000, 1);
        //2.获取客户端
        String conectionstr = "192.168.169.110:2181,192.168.169.120:2181,192.168.169.130:2181";
        CuratorFramework clilent = CuratorFrameworkFactory.newClient(conectionstr, 8000, 8000, retrypolicy);
        //3.启动客户端
        clilent.start();
        //4.创建一个Treecache对象，指定要监控的节点路径
        TreeCache treeCache = new TreeCache(clilent, "/hello2");
        //5.自定义一个监听器
        treeCache.getListenable().addListener(new TreeCacheListener() {
            @Override
            public void childEvent(CuratorFramework curatorFramework, TreeCacheEvent treeCacheEvent) throws Exception {
                //自定义监听器
                ChildData data = treeCacheEvent.getData();
                if (data!=null){
                    switch (treeCacheEvent.getType()){
                        case NODE_ADDED:
                            System.out.println("监控到有新增节点");
                            break;
                        case NODE_REMOVED:
                            System.out.println("监控到有节点被移除");
                            break;
                        case NODE_UPDATED:
                            System.out.println("有监控的节点被更新");
                            break;
                        default:
                            break;
                    }
                }
            }
        });

        //开启监听
        treeCache.start();

        //停止监听
        Thread.sleep(100000000);

    }


    /**
     * 获取节点数
     */
    @Test
    public void getZoneData() throws Exception {
        //1.定制一个重试策略
        ExponentialBackoffRetry retryPolicy = new ExponentialBackoffRetry(1000, 1);
        //2.获取客户端
        String conectionstr = "192.168.169.110:2181,192.168.169.120:2181,192.168.169.130:2181";
        CuratorFramework clilent = CuratorFrameworkFactory.newClient(conectionstr, 8000, 8000, retryPolicy);
        //3.启动客户端
        clilent.start();
        //4.获取节点数据
        byte[] bytes = clilent.getData().forPath("/hello2");
        System.out.println(new String(bytes));
        clilent.close();
    }


    /**
     * 设置节点数据
     */
    @Test
    public void setZnodeData() throws Exception {
        //1.定制一个重试策略
        ExponentialBackoffRetry retryPolicy = new ExponentialBackoffRetry(1000, 1);
        //2.获取客户端
        String conectionstr = "192.168.169.110:2181,192.168.169.120:2181,192.168.169.130:2181";
        CuratorFramework clilent = CuratorFrameworkFactory.newClient(conectionstr, 8000, 8000, retryPolicy);
        //3.启动客户吨
        clilent.start();
        //4.修改节点数据
        clilent.setData().forPath("/hello2","zookeeper".getBytes());
        //5.关闭客户端
        clilent.close();

    }


    /**
     * 创建临时节点
     */
    @Test
    public void createTmpZnode() throws Exception {
        //1.定制一个重试策略
        /**
         * param1：重试的间隔时间
         * param2：重试的最大次数
         */
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000,1);
        //2.获取一个客户端
        /**
         * param1：要连接的zookeeper服务器列表
         * param2:会话超时时间
         * param3：连接超时时间
         * param4:重试策略
         */
        String connectionStr = "192.168.169.110:2181,192.168.169.120:2181,192.168.169.130:2181";
        CuratorFramework client = CuratorFrameworkFactory.newClient(connectionStr, 8000, 8000, retryPolicy);
        //3.开启客户端
        client.start();
        //4.创建节点
        client.create().creatingParentContainersIfNeeded().
                withMode(CreateMode.EPHEMERAL).forPath("/hello3/abc","world".getBytes());

        //5.关闭客户端
        client.close();
    }


    /**
     * 创建永久节点
     * @throws Exception
     */
    @Test
    public void createZnode() throws Exception {
        //1.定制一个重试策略
        /**
         * param1：重试的间隔时间
         * param2：重试的最大次数
         */
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000,1);
        //2.获取一个客户端
        /**
         * param1：要连接的zookeeper服务器列表
         * param2:会话超时时间
         * param3：连接超时时间
         * param4:重试策略
         */
        String connectionStr = "192.168.169.110:2181,192.168.169.120:2181,192.168.169.130:2181";
        CuratorFramework client = CuratorFrameworkFactory.newClient(connectionStr, 8000, 8000, retryPolicy);
        //3.开启客户端
        client.start();
        //4.创建节点
        client.create().creatingParentContainersIfNeeded().
                withMode(CreateMode.PERSISTENT).forPath("/hello2/abc","world".getBytes());

        Thread.sleep(5000);
        //5.关闭客户端
        client.close();
    }
}
```

## Hadoop

从狭义来讲的话Hadoop包括了以下软件

- HDFS：分布式文件系统
- MapReduce：分布式计算系统
- Yam：分布式集群资源管理

广义来说Hadoop指的是一个生态圈

![image-20200623012729473](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200623012729473.png)

### 编译环境的准备

1. 关闭防火墙
2. 关闭selinux
   1. vim /etc/selinux/config
   2. SELINUX=disabled
3. 安装jdk1.7(不能使用1.8)
4. 下载maven 的环境

```shell
export MAVEN_HOME=/export/servers/apache-maven-3.0.5
export MAVEN_OPTS="-Xms4096m -Xmx4096m"
export PATH=:$MAVEN_HOME/bin:$PATH
```

![image-20200624183648039](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200624183648039.png)

 [apache-maven-3.5.4-bin.tar.gz](F:\Desktop\apache-maven-3.5.4-bin.tar.gz) 

 [findbugs-1.3.9.tar.gz](F:\Desktop\findbugs-1.3.9.tar.gz) 

![image-20200624183819474](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200624183819474.png)

# Hadoop集群搭建

集群规划

| 服务器IP          | 192.168.169.110 | 192.168.169.120 | 192.168.169.130 |
| ----------------- | --------------- | --------------- | --------------- |
| 主机名            | node01          | node02          | node03          |
| NameNode          | 是              | 否              | 否              |
| SecondaryNameNode | 是              | 否              | 否              |
| dataNode          | 是              | 是              | 是              |
| ResourceManager   | 是              | 否              | 否              |
| NodeManager       | 是              | 是              | 是              |

第一步：上传apache hadoop包并解压(安装包在/export/servers/hadoop-2.7.5-src/hadoop-dist/target下)

解压命令

```shell
cd /export/softwaresdas
tar -zxvf hadoop-2.7.5.tar.gz -C ../servers/
```

第二步：修改配置文件

修改core-site.xml   (/export/servers/hadoop-2.7.5/etc/hadoop)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->

<configuration>
	<property>
		<name>fs.default.name</name>
		<value>hdfs://192.168.169.110:8020</value>
	</property>	
	<property>
		<name>hadoop.tmp.dir</name>
		<value>/export/servers/hadoop-2.7.5/hadoopDatas/tempDatas</value>
	</property>
	<property>
		<name>io.file.buffer.size</name>
		<value>4096</value>
	</property>
	<property>
		<name>fs.trash.interval</name>
		<value>10080</value>
	</property>
</configuration>
```

hdfs-site.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->

<configuration>
	<property>
		<name>dfs.namenode.secondary.http-address</name>
		<value>node01:50090</value>
	</property>
	<property>
		<name>dfs.namenode.http-address</name>
		<value>node01:50070</value>
	</property>
	<property>
		<name>dfs.namenode.name.dir</name>
		<value>file:///export/servers/hadoop-2.7.5/hadoopDatas/namenodeDatas,file:////export/servers/hadoop-2.7.5/hadoopDatas/namenodeDatas2</value>
	</property>
	<property>
		<name>dfs.namenode.data.dir</name>
		<value>file:///export/servers/hadoop-2.7.5/hadoopDatas/datanodeDatas,file:///export/servers/hadoop-2.7.5/hadoopDatas/datanodeDatas2</value>
	</property>
	<property>
		<name>dfs.namenode.edits.dir</name>
		<value>file:///export/servers/hadoop-2.7.5/hadoopDatas/nn/edits</value>
	</property>
	<property>
		<name>dfs.namenode.checkpoint.dir</name>
		<value>file:///export/servers/hadoop-2.7.5/hadoopDatas/nn/name</value>
	</property>
	<property>
		<name>dfs.namenode.checkpoint.edits.dir</name>
		<value>file:///export/servers/hadoop-2.7.5/hadoopDatas/dfs/snn/edits</value>
	</property>
	<property>
		<name>dfs.repilcation</name>
		<value>2</value>
	</property>
	
	<property>
		<name>dfs.permissions</name>
		<value>false</value>
	</property>
	<property>
		<name>dfs.blocksize</name>
		<value>134217728</value>
	</property>


</configuration>
```

配置hadoop-env.sh

export JAVA_HOME=/export/servers/jdk1.7.0_79   ==配置对应的jdk==

修改mapred-site.xml

```xml
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->

<configuration>
	<property>
		<name>mapreduce.job.ubertask.enable</name>
		<value>true</value>
	</property>
	<property>
		<name>mapreduce.jobhistory.address</name>
		<value>node01:10020</value>
	</property>
	<property>
		<name>mapreduce.jobhistory.webapp.address</name>
		<value>node01:19888</value>
	</property>
</configuration>
```

yarn-site.xml

```xml
<?xml version="1.0"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->
<configuration>
	<property>
		<name>yarn.resourcemanager.hostname</name>
		<value>node01</value>
	</property>
	<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
	</property>
	<property>
		<name>yarn.log-aggregation-enable</name>
		<value>true</value>
	</property>
	<property>
		<name>yarn.log-aggregation.retain-seconds</name>
		<value>604800</value>
	</property>
	<property>
		<name>yarn.nodemanager.resouce.memory-mb</name>
		<value>20480</value>
	</property>

	<property>
		<name>yarn.scheduler.minimum-allocation-mb</name>
		<value>20480</value>
	</property>
	<property>
		<name>yarn.nodemanager.vmem-pmem-ratio</name>
		<value>2.1</value>
	</property>
	
</configuration>
```

mapred-env.sh

```xml
export JAVA_HOME=/export/servers/jdk1.7.0_79
```

**修改slaves**

修改slaves文件，然后将安装包发送到其他机器，重写启动集群即可

第一台机器执行以下命令

```shel
cd /export/servers/hadoop-2.7.5/etc/hadoop
vim slaves
```

```
node01
node02
node03
```

==执行以下命令创建文件夹==

```shell
[root@node01 servers]# mkdir -p /export/servers/hadoop-2.7.5/hadoopDatas/namenodeDatas
[root@node01 servers]# mkdir -p /export/servers/hadoop-2.7.5/hadoopDatas/namenodeDatas2
[root@node01 servers]# mkdir -p /export/servers/hadoop-2.7.5/hadoopDatas/datanodeDatas
[root@node01 servers]# mkdir -p /export/servers/hadoop-2.7.5/hadoopDatas/datanodeDatas2
[root@node01 servers]# mkdir -p /export/servers/hadoop-2.7.5/nn/edits
[root@node01 servers]# mkdir -p /export/servers/hadoop-2.7.5/snn/name
[root@node01 servers]# mkdir -p /export/servers/hadoop-2.7.5/dfs/snn/edits
```

安装包的分发

第一台机器执行以下命令

```shell
cd /exoprt/servers/
scp -r hadoop-2.7.5 node02:$PWD
scp -r hadoop-2.7.5 node03:$PWD
```

第一台机器进入 /etc/profile（添加以下配置）

```shell
export HADOOP_HOME=/export/servers/hadoop-2.7.5
export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH

source /etc/profile
```

**第四步:启动集群**

要启动hadoop集群，需要启动HDFS和YARN两个模块，注意：首次启动HDFS时，必须对其进行格式化操作。本质上是一些请理和准备工作，因为此时的HDFS在物理上还是不存在的

第一台机器执行以下命令：

```shell
cd /export/servers/hadoop-2.7.5/
bin/hdfs namenode -format
sbin/start-dfs.sh
sbin/start-yarn.sh
sbin/mr-jobhistory-daemon.sh start historyserver
```



**可以访问以下几个节点:**

> http://192.168.169.110:50070	查看hdfs
>
> http://192.168.169:8088	查看yarn集群
>
> http://192.168.169:19888	查看历史完成的任务

# HDFS

> HDFS由四部分组成

- HDFS Client
- NameNode
- DataNode
- Secondary NameNode

![image-20200628093411519](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200628093411519.png)



![image-20200628093526060](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200628093526060.png)

1. Client 就是客户端

- 文件切分，文件上传HDFS的时候，Client将文件切分成一个个的Block，然后进行存储
- 与NameNode交互，获取文件的位置信息
- 与DataNode交互，读取或者写入数据
- Client提供一些命令来管理和访问HDFS，比如启动或者关闭HDFS

2. NameNode：就是master，它是一个主管，管理者

- 管理HDFS的名称空间
- 管理数据块(Block)映射信息
- 配置副本策略
- 处理客户端读写请求

3. DataNode：就是Slave。NameNode下达命令，DataNode执行实际的操作

- 存储实际的数据块
- 执行数据块的读写操作

4. Sencondary NameNode：并非NameNode的热备，当NameNode挂掉的时候，它并不能马上替换NameNode并提供服务。

- 辅助NameNode，分担其工作量
- 定期合并fsimage和sedits，并推送给NameNode
- 在紧急情况下，可辅助恢复NameNode

**NameNode**

![image-20200628095233870](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200628095233870.png)

**hdfs的命令**

ls

```shell
格式：hdfs dfs -ls URI
作用：类似于Linux的ls命令，显示文件列表

hdfs dfs -ls /
```

lsr

```shell
格式：hdfs dfs -lsr URI
作用：在整个目录下递归执行ls，与UNIX中的ls-R类似

hdfs dfs -ls -R /
```

mkdir

```shell
格式：hdfs dfs[-p] -mkdir <paths>
作用：以<paths>中的URI作为参数，创建目录，使用-p参数可以递归创建目录
```

put

```shell
格式：hdfs dfs -put<localsrc> ... <dst>
作用：将单个的源文件src或者多个源文件srcs从本地文件系统拷贝到目标文件系统中(<dst>对应的路径)，也可以从标准输入中读取输入，写入目标文件系统中

hdfs dfs -put /root/a.txt /dirt
```

moveFromLocal

```shell
格式：hdfs dfs -moveFromLocal <localsrc> <dst>
作用：和put命令类似，但是源文件localsrc拷贝之后自身被删除

hdfs dfs -moveFromLocal /root/install.log  /
```

moveToLocal

```shell
为实现
```

get

```shell
格式：hdfs dfs -get [-ignorecrc] [-crc] <src> <localdst>
作用：将文件拷贝到本地文件系统。CRC校验失败的文件通过-ignorecrc选项拷贝，文件和CRC校验和可以通过-CRC选项拷贝

hdfs dfs -get /install.log /export/servers
```

mv

```shell
格式：hdfs dfs -mv URI <dest>
作用：将hdfs上的文件从原路径移动到目标路径(移动之后文件删除)，改命令不能夸文件系统

hdfs dfs -mv /dir1/a.txt /dir2
```

rm

```shell
格式：hdfs dfs -rm[-r] [-skipTrash] URI 
作用：删除参数指定的文件，参数可以有多个，此命令只删除文件和非空目录
如果指定-skipTrash选项，那么在回收站可用的情况下，改选项将跳过回收站而直接删除文件；
否则，在回收站可用时，在HDFS Shell 中执行此命令，会将文件暂时放到回收站中

hdfs dfs -rm -r /dir1
```

cp

```shell
格式：hdfs dfs -cp URI[URI...]<dest>
作用：将文件拷贝到目标路径中，如果<dest> 为目录的话，可以将多个文件拷贝到改目录下
-f
选项将覆盖目标，如果它已经存在。
-p
选项将保留文件属性(时间戳，所有权，许可，ACL，XAttr)

hdfs dfs -cp /dir1/a.txt /dir2
```

cat

```shell
hdfs dfs -cat URI[uri...]
作用：将参数所指示的文件内容输出到stdout

hdfs dfs -cat /install.log
```

chomd

```shell
格式：hdfs dfs -chomd [-R] URI[URI...]
作用：改变文件的权限，如果使用-R选项，则对整个目录有效递归执行，使用这一命令的用户必须是文件的所属用户，或者超级用户

hdfs dfs -chomd -R 777 /install.log
```

chown

```shell
格式： hdfs dfs -chown [-R] URI[URI...]
作用：改变文件的所属用户和用户组，如果使用 -R 选项，则对整个目录有效递归执行，使用这一命令的用户必须是文件的所属用户，或者超级用户

hdfs dfs -chown -R hadoop:hadoop /install.log
```

appendToFile

```shell
格式：hdfs dfs -appendToFile <localsrc>...<dst>
作用：追加一个或者多个文件到hdfs指定文件中，也可以从命令行读取输入

hdsf dfs -appendToFile a.xml b.xml /big.xml
```

> 高级命令

1. 文件限额配置

在多人共用HDFS的环境下，配置设置非常重要。特别是在Hadoop处理大量资料的环境，如果没有配置限额管理，很容易把所有的空间用完造成别人无法存取，hdfs的配额设定是针对目录而不是针对账号，可以让每个账号仅操作某一个目录，然后对目录设置配置。

hdfs文件的限额配置允许我们以文件个数，或者文件大小来限制我们在某个目录下上传的文件数量或者文件内容总量，以便达到我们类似百度网盘等限制每个用户允许上传的最大的文件的量

```shell
hdfs dfs -count -q -h /user/root/dir1  #查看配额信息
```

- **数量限额**

```shell
hdfs dfs -mkdir -p /user/root/dir   #创建hdfs文件夹
hdfs dfsadmin -setQuota 2 dir 		#给该文件夹下面设置最多上传两个文件，发现只能上传一个文件

hdfs dfsadmin -clrQuota /user/root/dir  #清楚文件数量限制
```

![image-20200628110326000](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200628110326000.png)

超过限额上传失败

![image-20200628110518422](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200628110518422.png)

- **空间大小限额**

在设置空间限额时，设置的空间至少是block_size*3大小

```shell
hdfs dfsadmin -setSpacheQuota 4k /user/root/dir   #设置空间大小4KB
hdfs dfs -put /root/a.txt /user/root/dir
```

生成任意大小文件的命令

```shell
dd if=/dev/zero of=1.txt bs=1M count=2   #生成2M的文件
```

清楚空间大小限额配置

```shell
hdfs dfsadmin -clrSpaceQuota dir2
```

- **设置hdfs的安全模式**

安全模式的操作命令

```shell
hdfs dfsadmin -safemode get		#查看安全模式状态
hdfs dfsadmin -safemode enter	#进入安全模式
hdfs dfsadmin -safemode leave	#离开安全模式
```

- **HDFS基准测试**

实际生产环境当中，hadoop的环境搭建完成之后，第一件事情就是进行压力测试，测试我们的集群的读取和写入速度，测试我们的网络带宽是否足够等一些基准测试

1. **测试写入速度**

向hdfs文件系统中写入数据，10个文件，每个文件10MB，文件存放到/benchmarks/TestDFSIO中

```shell
hadoop jar /export/servers/hadoop-2.7.5/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-2.7.5.jar TestDFSIO -write -nrFiles 10 -fileSize 100MB
```

![image-20200628112614380](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200628112614380.png)

![image-20200628112943924](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200628112943924.png)

执行完成之后会在跟目录下生产一个文件

![image-20200628113136360](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200628113136360.png)

而且会当运行的当前目录下生成一个文件log

![image-20200628113330499](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200628113330499.png)

完成之后查看写入速度结果

```shell
hdfs dfs -text /benchmarks/TestDFSIO/io_write/part-00000
```

2. **测试读取速度**

```shell
hadoop jar /export/servers/hadoop-2.7.5/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-2.7.5.jar TestDFSIO -read -nrFiles 10 -fileSize 10MB
```

3. **清楚测试数据**

```shell
hadoop jar /export/servers/hadoop-2.7.5/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-2.7.5.jar TestDFSIO -clean
```

**hdfs的写入过程:**

![image-20200628114731547](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200628114731547.png)



![image-20200628135113032](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200628135113032.png)



# windows下安装hadoop

以下连接配置：https://blog.csdn.net/tswc_byy/article/details/84133431

## 使用url方式访问数据(了解)

导入对于的maven依赖

```xml
<dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-common</artifactId>
            <version>2.6.5</version>
        </dependency>

        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.6.5</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
            <version>2.6.5</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-mapreduce-client-core</artifactId>
            <version>2.6.5</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.9</version>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.2.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.9</version>
        </dependency>
    </dependencies>
```

编写相应的api

```java
package com.iscas.hdfs_api;

import org.apache.commons.io.IOUtils;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.*;
import org.junit.Test;

import java.io.*;
import java.net.MalformedURLException;
import java.net.URI;
import java.net.URISyntaxException;
import java.net.URL;

/**
 * @Author 晨边#CB
 * @Date:created in  2020/6/28 14:53
 * @Version V1.0
 **/

public class HdfsApiDemo {

    @Test
    public void lHdfs() throws IOException {
        //1.注册url
        URL.setURLStreamHandlerFactory(new FsUrlStreamHandlerFactory());
        //2.获取hdfs文件的输入流
        InputStream inputStream = new URL("hdfs://192.168.169.110:8020/a.txt").openStream();
        //3.获取本地文件的输出流
        FileOutputStream outputStream = new FileOutputStream(new File("D:\\hello.txt"));
        //4.实现文件的拷贝
        IOUtils.copy(inputStream,outputStream);
        //5.关流
        IOUtils.closeQuietly(inputStream);
        IOUtils.closeQuietly(outputStream);
    }

    /**
     * 获取FileSystem
     */
    @Test
    public void getFileSystem1() throws IOException {
        //1:创建Configuration
        Configuration configuration = new Configuration();
        //2.设置文件系统的类型
        configuration.set("fs.defaultFS","hdfs://192.168.169.110:8020");
        //3.获取指定的文件系统
        FileSystem fileSystem = FileSystem.get(configuration);
        //4.输出
        System.out.println(fileSystem);
    }

    /**
     * 方式2
     */
    @Test
    public void getFileSystem2() throws URISyntaxException, IOException {
        FileSystem fileSystem = FileSystem.get(new URI("hdfs://192.168.169.110:8020"), new Configuration());
        System.out.println(fileSystem);
    }

    /**
     * 方式3
     */
    @Test
    public void getFileSyste3() throws IOException {
        Configuration configuration = new Configuration();
        //指定文件系统类型
        configuration.set("fs.defaultFS","hdfs://192.168.169.110:8020");
        //获取指定的文件系统
        FileSystem fileSystem = FileSystem.newInstance(configuration);
        System.out.println(fileSystem);
    }

    /**
     * 第四种方式
     */
    @Test
    public void getFileSystem4() throws URISyntaxException, IOException {
        FileSystem fileSystem = FileSystem.newInstance(new URI("hdfs://192.168.169.110:8020"), new Configuration());

        System.out.println(fileSystem);
    }

    /**
     * 使用API遍历hdfs中所有文件
     */
    @Test
    public void listMyFile() throws Exception{
        //1.获取filesystem
        FileSystem fileSystem = FileSystem.get(new URI("hdfs://192.168.169.110:8020"), new Configuration());
        //2.调用方法listFiles 获取跟目录下所有文件的信息
        RemoteIterator<LocatedFileStatus> iterator = fileSystem.listFiles(new Path("/"), true);
        //3.遍历迭代器
        while (iterator.hasNext()){
            LocatedFileStatus fileStatus = iterator.next();

            //获取文件的绝对路径:hdfs://node01:8020/xxx
            System.out.println(fileStatus.getPath()+"--------"+fileStatus.getPath().getName());

            //获取文件的Block信息
            BlockLocation[] blockLocations = fileStatus.getBlockLocations();
            System.out.println("blockLocations.length:"+blockLocations.length);
        }
    }

    /**
     * hdfs上创建文件夹
     */
    @Test
    public void mkdirsTest() throws URISyntaxException, IOException {
        //1.获取filesystem实例
        FileSystem fileSystem = FileSystem.get(new URI("hdfs://192.168.169.110:8020"), new Configuration());
        //2.创建文件夹
        boolean flag = fileSystem.mkdirs(new Path("/aaa/bbb/ccc"));
        //指定创建文件
        fileSystem.create(new Path("/aaa/bbb/ccc/a.txt"));
        System.out.println(flag);

        //3.关闭filesystem
        fileSystem.close();
    }

    /**
     * hdfs文件下载
     */
    @Test
    public void downLoadFile()throws Exception{
        //1。获取FileSystem
        FileSystem fileSystem = FileSystem.get(new URI("hdfs://192.168.169.110:8020"),new Configuration());
        //2.获取hdfs的输入流
        FSDataInputStream inputStream = fileSystem.open(new Path("/dir1/a.txt"));
        //3.获取本地路径的输出流
        FileOutputStream outputStream = new FileOutputStream("D://a.txt");
        //4.文件的拷贝
        IOUtils.copy(inputStream,outputStream);
        IOUtils.closeQuietly(inputStream);
        IOUtils.closeQuietly(outputStream);
        fileSystem.close();
    }

    /**
     * 方式2：实现文件下载
     */
    @Test
    public void downLoadFile2()throws Exception{
        //1.获取FileSystem
        FileSystem fileSystem = FileSystem.get(new URI("hdfs://192.168.169.110:8020"),new Configuration());
        //2.调用方法，实现文件的下载
        fileSystem.copyToLocalFile(new Path("/dir1/a.txt"),new Path("D://a.txt"));
        //3.关闭fileSystem
        fileSystem.close();
    }

    /**
     * 文件上传
     */
    @Test
    public void UpLoadFile()throws Exception{
        //1.获取FileSystem
        FileSystem fileSystem = FileSystem.get(new URI("hdfs://192.168.169.110:8020"),new Configuration());

        //2.调用方法，实现上传
        fileSystem.copyFromLocalFile(new Path("D:\\a.txt"),new Path("/"));
        //3.关闭fileSystem
        fileSystem.close();
    }

    /**
     * 合并文件
     */
    @Test
    public void mergetFile()throws  Exception{
        //1.获取FileSystem(分布式文件系统)
        FileSystem fileSystem = FileSystem.get(new URI("hdfs://192.168.169.110:8020"), new Configuration(),"root");
        //2.获取hdfs大文件的输出流
        FSDataOutputStream fsDataOutputStream = fileSystem.create(new Path("/big_text.txt"));
        //3.获取本地文件系统
        LocalFileSystem localFileSystem = FileSystem.getLocal(new Configuration());
        //4.获取本地文件夹下所有文件的详情
        FileStatus[] fileStatuses = localFileSystem.listStatus(new Path("D:\\input"));
        //5.遍历每个文件，获取每个文件的输入流
        for (FileStatus fileStatus : fileStatuses) {
            FSDataInputStream inputStream = localFileSystem.open(fileStatus.getPath());
            //6.将小文件的数据复制到大文件
            IOUtils.copy(inputStream,fsDataOutputStream);
            IOUtils.closeQuietly(inputStream);
        }
        //7.关闭流
        IOUtils.closeQuietly(fsDataOutputStream);
        localFileSystem.close();
        fileSystem.close();
    }
}
```

# MapReduce分区-代码编写步骤

- **定义Mapper**

这个Mapper程序不做任何逻辑，也不对Key-Value做任何改变，只是接收数据，然后往下发送



- **定义Partitioner**

主要的逻辑就在这里，这也是这个案例的意义，通过Partitioner将数据分发给不同的Reducer



- **定义Reducer**

这个Reducer也不做任何处理，将数据原封不动的输出即可



- **主类中设置分区类和ReduceTask个数**









































