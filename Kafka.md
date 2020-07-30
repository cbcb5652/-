# Kafka

> kafka的概述

​	是一个分布式的基于发布/订阅的消息队列(Message Queue).主要应用于大数据实时处理领域。

 修改server.properties文件

![image-20200713172808155](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200713172808155.png)

![image-20200713173125582](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200713173125582.png)

![image-20200713173348414](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200713173348414.png)

总结

![image-20200713173419365](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200713173419365.png)

启动kafka：

```shell
[root@node01 bin]# kafka-server-start.sh -daemon  ../config/server.properties 
```

配置多台机器启动停止 kafka脚本：

```shell
case $1 in
"start"){
	
	for i in node01 node02 node03
	do
		echo "******************$i***********************"
		ssh $i "/export/servers/kafka/bin/kafka-server-start.sh -daemon /export/servers/kafka/config/server.properties"
	done

};;

"stop"){

        for i in node01 node02 node03 
        do
                echo "******************$i***********************"
                ssh $i "/export/servers/kafka/bin/kafka-server-stop.sh  /export/servers/kafka/config/server.properties"
        done
};;
esac
```

查看多台机器jps脚本

```shell
#!/bin/bash

case $1 in
"jps"){
	for i in node01 node02 node03
	# 或者 for i in 172.16.29.94 172.16.29.95 172.16.29.96
	do
		echo "**********$i**********"
		ssh $i "/export/servers/jdk1.8.0_60/bin/jps"
	done
};;
esac
```

## Kafka命令行操作

1.  查看当前服务器中所有的topic

```shell
bin/kafka-topics.sh --zookeeper node01:2181 --list
```

2. 创建topic

```shell
bin/kafka-topics.sh --create --zookeeper node01:2181 --topic first --partitions 2 --replication-factor 2
```

![image-20200713231439428](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200713231439428.png)

![image-20200713231504127](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200713231504127.png)

选项说明：

​	--topic 定义topic名

​	--replication-factor   定义副本参数

​	--partitions  定义分区数

查看logs里面的

![image-20200713231644015](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200713231644015.png)

==分区号==

查看描述

```shell
bin/kafka-topics.sh --describe --topic first --zookeeper node01:2181
```

![image-20200713232953066](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200713232953066.png)

3. 删除topic

```shell
bin/kafka-topics.sh --delete --zookeeper node01:2181 --topic first
```

​	 需要server .properties 中设置 delete.topic.enable = true 否则只是标记删除。

4. 发送消息

```shell
bin/kafka-console-producer.sh --topic first --broker-list node01:9092
```

5. 消费消息

```shell
bin/kafka-console-consumer.sh --zookeeper hadoop102:2181 --topic first

bin/kafka-console-consumer.sh --topic first --bootstrap-server node01:9092

bin/kafka-console-consumer.sh --topic first --zookeeper node01:2181 --from-beginning  #七天之内
```

6. 查看某个Topic 的详情

```shell
bin/kafka-topics.sh --zookeeper hadoop102:2181 --alter --topic first --partitions 
```

![image-20200714002658515](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200714002658515.png)

https://www.bilibili.com/video/BV1a4411B7V9?p=11































