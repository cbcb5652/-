# Docker 20/6/2

> Docker 学习

- Docker概述
- Docker安装
- Docker命令
  - 镜像命令
  - 容器命令
  - 操作命令
- Docker镜像
- 容器数据卷
- DockerFile
- Docker网络原理
- IDEA 整合Docker
- Docker compose
- Docker Swarm

## Docker概述

一款产品: 开发--上线  ！  应用环境，应用配置！

开发  ----  运维 。 问题：在我的电脑上可以运行！版本更新 导致服务不可用！对于运维考验十分大。

开发及运维！

环境配置十分麻烦，每一个机器都要部署环境(集群Redis Es Hadoop)费时费力

发布一个项目jar (Redis Mysql jdk es)带着环境发布项目(打包!)  容易  去掉配置麻烦

Windows，最后发布到Linux！

传统: 开发jar ，运维来做！

现在：开发打包部署上线，一套流程做完！

![image-20200602014328814](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200602014328814.png)

Docker给以上的问题，提出解决方案!

![image-20200602014357330](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200602014357330.png)

Docker的思想来自于集装箱!

JRE -- 多个应用(端口冲突) -- 原来都是交叉的！

隔离: Docker核心思想! 打包装箱，每个箱子都是互相隔离的

Docker 通过隔离机制，可以将服务器利用到极致！

本质:所有的技术因为出现了一些问题，我们需要去解决。才去学习！

### Docker的历史

![image-20200602015320108](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200602015320108.png)

```shell
vm : linux centos 原生镜像(一个电脑!)  隔离：需要开启多个虚拟机！
docker: 隔离， 镜像(最核心的环境 4m+jdk+mysql) 十分小巧，运行镜像就可以了！！
```

> 聊聊Docker

Docker是基于GO语言开发的！ 开源项目!

官网：https://www.docker.com/

![image-20200602015805877](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200602015805877.png)

文档地址:https://docs.docker.com/    Docker的文档是超级详细的！！！！！

仓库地址:https://hub.docker.com/  git push pull

### Docker 能干嘛？

> 之前的虚拟机技术

![image-20200603232516672](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200603232516672.png)

虚拟化技术的缺点:

1. 资源占用十分多
2. 冗余步骤多
3. 启动很慢



> 容器化技术

==容器化技术不是模拟的一个完整的操作系统==

![image-20200603232734922](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200603232734922.png)

比较docker和虚拟机技术的不同：

- 传统虚拟机，虚拟出一条硬件，运行一整个完整的操作系统，然后在这个系统上安装和运行软件
- 容器内的运用直接运行在宿主机的内容，容器是没有自己的内核的，也没有虚拟我们的硬件，所以就轻便了。
- 每个容器间的互相隔离的，每个容器内都有一个属于自己的文件系统，互不影响

> DevOps(开发，运维)

**应用更快速的交付和部署**

传统：一堆帮助文档，安装程序

Docker：打包镜像发布测试，一键运行

**更便捷的升级和扩容**

![image-20200603233523177](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200603233523177.png)

## Docker安装

### Docker的基本组成

![image-20200603233820408](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200603233820408.png)

**镜像(image)：**

docker镜像就好比是一个模板，可以通过这个模板来创建容器服务，tomcat镜像=> run => tomcat01 容器(提供服务器)，

通过这个容器可以创建多个容器，(最终服务器运行或者项目就是在容器中的)

**容器(container):**

Docker来利用容器技术，独立运行一个或者一组应用，通过镜像来创建的。

启动，停止，删除，基本命令！

目前就可以把这个容器理解为一个简易的Linux系统

**仓库(reposity):**

仓库就是存放镜像的地方！

仓库分为公有仓库和私有仓库

Docker Hub(默认是国外的)

### 安装Docker

> 环境准备

![image-20200603234416646](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200603234416646.png)

> 环境查看

```bash
# 系统内核是3.10以上的
uname -r   #查看内核
```

![image-20200604000145295](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200604000145295.png)

> 安装

帮助文档：

```bash
# 卸载久版本
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
                  
# 2. 需要的安装包
yum install -y yum-utils

# 3.设置镜像的仓库
$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo	#默认是从国外的十分慢
    
$ sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo	#推荐使用阿里云的快！！！
    
# 更新yum软件包索引

    
#4. 安装docker相关的内容   docker-ce 社区版， ee 企业版
yum install docker-ce docker-ce-cli containerd.io

#5.启动docker
systemctl start docker

#6. 使用docker version查看是否启动成功
```

![image-20200604152916230](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200604152916230.png)

```shell
# 7. hello-world
docker run hello-world
```

![image-20200604153037468](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200604153037468.png)

```shell
# 8. 查看一下下载的这个 hello-world镜像
[root@iZ8vbcc06phmpnew8h8zvdZ /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB
```

了解：卸载docker

```shell
# 1. 卸载依赖
yum remove docker-ce docker-ce-cli containerd.io

#2. 删除资源
rm -rf /var/lib/docker

# /var/lib/docker    docker的默认工作路径!
```

### 阿里云镜像加速

1. 找到阿里云镜像服务
2. 找到镜像加速器

![image-20200604190055182](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200604190055182.png)

3. 配置使用

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://d4qzdb7d.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

![image-20200604190534233](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200604190534233.png)

### 底层原理

**Docker是怎么进行工作的？**

Docker 是一个Client-Server 结构的系统，Docker的守护进程运行在主机上。通过socket从客户端访问！

DockerServer 接收到Docker-Client的指令，就会执行这个命令！

![image-20200604191008665](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200604191008665.png)

**Docker为什么比虚拟机快？**

1. Docker有着比虚拟机更少的抽象层
2. Docker 利用的是宿主机的内核，vm需要是Guest OS。

![image-20200604191126184](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200604191126184.png)

所以说。新建一个容器的时候，docker不需要像虚拟机一样重新加载一个操作系统的内核，避免引导。虚拟机是加载Guest OS，分钟级别的。而docker是利用 宿主机的操作系统。省略了复杂的过程。秒级！

## Docker的常用命令

### 帮助命令

```shell
docker version		#显示docker的版本信息
docker info			# 显示docker的系统信息，包括镜像和容器的数量
docker 命令 --help   #万能命令 帮助命令
```

帮助文档地址：https://docs.docker.com/reference/

### 镜像命令

----------

**docker images：**  查看所有本机上的主机的镜像

```shell
[root@iZ8vbcc06phmpnew8h8zvdZ /]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB

# 解释
REPOSITORY  镜像从仓库源
TAG         镜像的标签
IMAGE ID  	镜像的ID
CREATED		镜像的创建时间
SIZE		镜像的大小

# 可选项
-a, --all             Show all images (default hides intermediate images)	#列出所有的镜像
      --digests         Show digests
  -f, --filter filter   Filter output based on conditions provided
      --format string   Pretty-print images using a Go template
      --no-trunc        Don't truncate output
  -q, --quiet           Only show numeric IDs	# 只显示镜像的id
```

docker search搜索镜像

![image-20200604192618240](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200604192618240.png)

**docker pull:**下载镜像

```shell
# 下载镜像 docker pull 镜像名：[:tag]
[root@iZ8vbcc06phmpnew8h8zvdZ /]# docker pull mysql
Using default tag: latest		#如果不写tag ，默认就是latest

[root@iZ8vbcc06phmpnew8h8zvdZ /]# docker pull mysql
Using default tag: latest
latest: Pulling from library/mysql
afb6ec6fdc1c: Pull complete # 分层下载，docker images的核心，联合文件系统
0bdc5971ba40: Pull complete 
97ae94a2c729: Pull complete 
f777521d340e: Pull complete 
1393ff7fc871: Pull complete 
a499b89994d9: Pull complete 
7ebe8eefbafe: Pull complete 
597069368ef1: Pull complete 
ce39a5501878: Pull complete 
7d545bca14bf: Pull complete 
211e5bb2ae7b: Pull complete 
5914e537c077: Pull complete 
Digest: sha256:a31a277d8d39450220c722c1302a345c84206e7fd4cdb619e7face046e89031d	#签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest		#真实地址


```

![image-20200604193101795](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200604193101795.png)

**docker rmi** 删除镜像！

```shell
 docker rmi -f a4fdfd462add     #删除指定的容器 容器id
 docker rmi -f 容器id 容器id 容器id   #删除多个容器
 docker rmi -f $(docker images -aq)	#删除全部的容器
```

### 容器命令

-----------

说明:我们有了镜像才可以创建容器。linux，下载一个centos镜像来测试学习

```shell
docker pull centos
```

**新建容器并启动**

```shell
docker run [可选参数] image

# 参数说明
--name="name"   容器名字 tomcat01  tomcat02 用来区分容器
-d 				后台方式运行 ja nohup
-it				使用交互方式运行
-p				指定容器的端口 -p:8080
	-p ip：主机端口：容器端口
	-p 主机端口：容器端口	(常用)
	-p 容器端口
	容器端口
-P				随机指定端口

# 测试。启动并进入容器
[root@iZ8vbcc06phmpnew8h8zvdZ ~]# docker run -it centos /bin/bash
[root@f0bd2c5505cc /]# 

# 查看容器内的centos，基础版本，很多命令都是不完善的
[root@f0bd2c5505cc /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

# 从容器中退回主机
[root@f0bd2c5505cc /]# exit
exit
[root@iZ8vbcc06phmpnew8h8zvdZ ~]# 
```

**列出所有运行的容器**

```shell
# docker ps命令
-a #列出当前正在运行的容器+带出历史运行过的容器
-n=？	#显示最近创建的容器
-q # 只显示容器的编号

[root@iZ8vbcc06phmpnew8h8zvdZ ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[root@iZ8vbcc06phmpnew8h8zvdZ ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS               NAMES
f0bd2c5505cc        centos              "/bin/bash"         2 minutes ago       Exited (0) 58 seconds ago                       confident_mirzakhani
8fb505492e71        bf756fb1ae65        "/hello"            9 hours ago         Exited (0) 9 hours ago                          competent_thompson
[root@iZ8vbcc06phmpnew8h8zvdZ ~]
```

**退出容器**

```shell
exit #直接从容器中退出
Ctrl+P+Q  # 容器不停止退出
```

**删除容器**

```shell
docker rm 容器id		#删除指定的容器，不能删除正在运行的容器
docker rm -f $(docker ps -aq) 	#删除所有的容器
docker ps -a -q|xargs docker rm #删除所有的容器
```

**启动和停止容器的操作**

```shell
docker start 容器id		#启动容器
docker restart 容器id		#重启容器
docker stop 容器id		#停止当前正在运行的容器
docker kill 容器id		#强制停止当前容器
```

### 常用的其他命令

------------

**后台启动容器**

```shell
# 命令 docker run -d 镜像名！
[root@iZ8vbcc06phmpnew8h8zvdZ /]# docker run -d centos

# 问题docker ps，发现 centos 停止了

# 常见的坑，docker 容器使用后台运行，就必须有一个前台进程，docker发现没有应用，就会自动停止
# nginx，容器启动后，发现自己没有启动服务，就会立刻停止，就是没有程序了
```

**查看日志**

```shell
docker logs -f -t --tail 容器没有日志

#编写一个shell脚本
docker run -d centos /bin/sh -c "while true;do echo chenbin ;sleep 1;done"

docker ps
CONTAINER ID     IMAGE
ja123hjjqw		 centos

#显示日志
-tf		#显示日志
--tail number	#要显示的日志条数
docker logs -tf --tail 10   dockerid
```

**查看容器中的进程信息**

```shell
# 命令 docker top 容器id
[root@iZ8vbcc06phmpnew8h8zvdZ /]# docker top fa0238cf8fbf
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                13242               13225               0                   01:06               pts/0               00:00:00            /bin/bash
```

**查看镜像的原数据**

```shell
# 命令
docker inspect 容器id

# 测试
[root@iZ8vbcc06phmpnew8h8zvdZ /]# docker inspect fa0238cf8fbf
[
    {
        "Id": "fa0238cf8fbf65d2da718ede2d57e6cc2147c7db6d4922228161ef3cffd8bf0f",
        "Created": "2020-06-04T17:06:04.483339392Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 13242,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-06-04T17:06:04.768231936Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:470671670cac686c7cf0081e0b37da2e9f4f768ddc5f6a26102ccd1c6954c1ee",
        "ResolvConfPath": "/var/lib/docker/containers/fa0238cf8fbf65d2da718ede2d57e6cc2147c7db6d4922228161ef3cffd8bf0f/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/fa0238cf8fbf65d2da718ede2d57e6cc2147c7db6d4922228161ef3cffd8bf0f/hostname",
        "HostsPath": "/var/lib/docker/containers/fa0238cf8fbf65d2da718ede2d57e6cc2147c7db6d4922228161ef3cffd8bf0f/hosts",
        "LogPath": "/var/lib/docker/containers/fa0238cf8fbf65d2da718ede2d57e6cc2147c7db6d4922228161ef3cffd8bf0f/fa0238cf8fbf65d2da718ede2d57e6cc2147c7db6d4922228161ef3cffd8bf0f-json.log",
        "Name": "/peaceful_williams",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Capabilities": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/f2652adbcd9dc5eeb3cf0243204ab8db240a5efb06640223107978ce6a84df69-init/diff:/var/lib/docker/overlay2/89184f1e95b52daf6bbacd5dfc6f62bc6cf4446e3fbfe8ff885e14b4a4637635/diff",
                "MergedDir": "/var/lib/docker/overlay2/f2652adbcd9dc5eeb3cf0243204ab8db240a5efb06640223107978ce6a84df69/merged",
                "UpperDir": "/var/lib/docker/overlay2/f2652adbcd9dc5eeb3cf0243204ab8db240a5efb06640223107978ce6a84df69/diff",
                "WorkDir": "/var/lib/docker/overlay2/f2652adbcd9dc5eeb3cf0243204ab8db240a5efb06640223107978ce6a84df69/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "fa0238cf8fbf",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20200114",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS",
                "org.opencontainers.image.created": "2020-01-14 00:00:00-08:00",
                "org.opencontainers.image.licenses": "GPL-2.0-only",
                "org.opencontainers.image.title": "CentOS Base Image",
                "org.opencontainers.image.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "f6a102cf6ff30e8f05a0a7aa6f2fa16f7eee97d52eaa32360b186a6b69c0de55",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/f6a102cf6ff3",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "4783137641cb13a5b27121569ec7dc08709af86a1eb5a40fa2e4220eaaae8537",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "e916c479c032403cf2a2f17cad7ab5e4819adfd989ca5816db75812f8458d430",
                    "EndpointID": "4783137641cb13a5b27121569ec7dc08709af86a1eb5a40fa2e4220eaaae8537",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

**进入当前正在运行的容器**

```shell
# 我们通常容器都是使用后台的方式运行的，需要进入容器，修改一些配置

# 命令
docker exec -it 容器id bashShell

# 测试 进入容器①
[root@iZ8vbcc06phmpnew8h8zvdZ ~]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
fa0238cf8fbf        centos              "/bin/bash"         4 days ago          Up 4 days                               peaceful_williams
[root@iZ8vbcc06phmpnew8h8zvdZ ~]# docker exec -it fa0238cf8fbf /bin/bash
[root@fa0238cf8fbf /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@fa0238cf8fbf /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 Jun04 pts/0    00:00:00 /bin/bash
root        16     0  0 05:32 pts/1    00:00:00 /bin/bash
root        30    16  0 05:32 pts/1    00:00:00 ps -ef

# 方式二进入容器
docker attach 容器id
# 测试
[root@iZ8vbcc06phmpnew8h8zvdZ ~]# docker attach  fa0238cf8fbf 
正在执行当前的代码....

# docker exec 	#进入容器后开启一个新的终端，可以在里面操作(常用)
# docker attach #容器正在执行的终端，不会启动新的进程！
```

**从容器内拷贝文件到主机上**

```shell
docker cp  容器id：容器内路径 目的的主机路径

# 查看当前主机目录下
[root@iZ8vbcc06phmpnew8h8zvdZ home]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
1244300b5050        centos              "/bin/bash"         2 minutes ago       Up 2 minutes                            brave_benz

# 进入docker容器内部
[root@iZ8vbcc06phmpnew8h8zvdZ home]# docker attach 1244300b5050
[root@1244300b5050 /]# cd /home
[root@1244300b5050 home]# ls

# 新建一个文件
[root@1244300b5050 home]# touch test.java
[root@1244300b5050 home]# ls
test.java
[root@1244300b5050 home]# exit
exit
[root@iZ8vbcc06phmpnew8h8zvdZ home]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

# 将这个文件拷贝出来到主机上
[root@iZ8vbcc06phmpnew8h8zvdZ home]# docker cp 1244300b5050:/home/test.java /home
[root@iZ8vbcc06phmpnew8h8zvdZ home]# ls
chenbin.java  test.java
[root@iZ8vbcc06phmpnew8h8zvdZ home]# 


# 拷贝是一个手动过程，未来我们使用-v 卷的技术，可以实现，自动同步， /home /home
```

### 小结

![image-20200609135151270](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200609135151270.png)

### 作业练习

> Docker 安装Nginx

```shell
# 搜索镜像 
docker search nginx   # 建议大家去dockers搜索，可以看到帮助文档

# 下载镜像 
docker pull nginx

# 运行测试
[root@iZ8vbcc06phmpnew8h8zvdZ home]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              4392e5dad77d        6 days ago          132MB
centos              latest              470671670cac        4 months ago        237MB

# -d 后台测试
# --name 给容器命名
# -p 宿主机端口：容器内部端口
[root@iZ8vbcc06phmpnew8h8zvdZ home]# docker run -d --name
flag needs an argument: --name
See 'docker run --help'.
[root@iZ8vbcc06phmpnew8h8zvdZ home]# docker run -d --name nginx01 -p 3344:80  nginx
e6f7ab8c358a89bb43a677e14d3279610a991735812a431b10a4c3bdb691dee2
[root@iZ8vbcc06phmpnew8h8zvdZ home]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
e6f7ab8c358a        nginx               "/docker-entrypoint.…"   9 seconds ago       Up 8 seconds        0.0.0.0:3344->80/tcp   nginx01
[root@iZ8vbcc06phmpnew8h8zvdZ home]# curl localhost:3344

# 进入容器
[root@iZ8vbcc06phmpnew8h8zvdZ home]# docker exec -it nginx01 /bin/bash
root@e6f7ab8c358a:/# whereis nginx 
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@e6f7ab8c358a:/# cd /etc/nginx
root@e6f7ab8c358a:/etc/nginx# ls
conf.d	fastcgi_params	koi-utf  koi-win  mime.types  modules  nginx.conf  scgi_params	uwsgi_params  win-utf
root@e6f7ab8c358a:/etc/nginx# 
```

端口暴露的概念

![image-20200609140355909](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200609140355909.png)

> docker 来装一个tomcat

```shell
# 官方的使用
docker run -it --rm tomcat:9.0

# 我们之前的启动都是后台，停止了容器之后，容器还是可以查到	docker run -it --rm tomcat:9.0 一般用来测试，用完及删

# 下载再启动
docker pull tomcat:9.0

# 启动运行
docker run -d -p 3355:8080 --name tomcat01 tomcat

# 测试访问没有问题

# 进入容器
[root@iZ8vbcc06phmpnew8h8zvdZ home]# docker exec -it tomcat01 /bin/bash

# 发现问题：1.linux命令少了。2。没有webapps。阿里云镜像的原因。默认是最小的镜像，所有不必要的都剔除掉。
# 保证最小可运行环境！ 
```

思考问题：我们以后要部署项目，如果每次都要进入容器十分麻烦！要是可以在容器外提供一个映射路径，webapps，我们在外部不放置项目，就自动同步到内部就好了！

docker容器 tomcat + 网站 docker mysql

> 部署：es+kibana

```shell
# es 暴露的端口很多！
# es 十分耗内存
# es 的数据一般需要放置到安全目录！挂载！
# --net somenetwork ? 网络配置

# 启动es
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node"
elasticsearch:7.6.2

# 启动了 linux就很卡，卡住了  docker status 查看cpu的状态

# es是十分耗内存的 1.2G
```

![image-20200609145019130](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200609145019130.png)

```shell
# 没有那么耗费内存的启动
docker run -d --name elasticsearch02 -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2

# 查看 docker stats
```

![image-20200609145924178](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200609145924178.png)

```shell
[root@iZ8vbcc06phmpnew8h8zvdZ home]# curl localhost:9200
{
  "name" : "81538287bb75",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "jKYta_j1QzqKSED7EvXggA",
  "version" : {
    "number" : "7.6.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "ef48eb35cf30adf4db14086e8aabd07ef6fb113f",
    "build_date" : "2020-03-26T06:34:37.794943Z",
    "build_snapshot" : false,
    "lucene_version" : "8.4.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

![image-20200609150115729](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200609150115729.png)

### 可视化

- portainer   (先用这个)

```shell
docker run -d -p 8088:9000 \--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

- Rancher (CI/CD再用)



**什么portainer？**

Docker图形化界面管理工具！提供一个后台面板供我们操作！

```shell
docker run -d -p 8088:9000 \--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

访问测试：外网：8088

## Docker镜像

### 镜像是什么

镜像是一种轻量级，可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码，运行时，库，环境变量和配置文件。

### Docker镜像加载原理

> Union(联合文件系统)

![image-20200609152605887](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200609152605887.png)

> Docker镜像加载原理

![image-20200609152848999](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200609152848999.png)

平时我们在虚拟机的CentOS 都是好几G，为什么Docker这里才200M？

对于一个精简的OS ，rootfs可以很小，只需要包含最基本的命令，工具和程序库就可以了，因为底层直接Host的Kernel，自己只需提供rootfs就可以了。由此可见对于不同的linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以共用bootfs。

### 分层原理

> 分层的理解

我们可以去下载一个镜像，注意观察下载的日志输出，可以看到是一层一层下载的！

![image-20200609155529686](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200609155529686.png)

```shell
"RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:ffc9b21953f4cd7956cdf532a5db04ff0a2daa7475ad796f1bad58cfbaf77a07",
                "sha256:d4e681f320297add0ede0554524eb9106d8c3eb3a43e6e99d79db6f76f020248",
                "sha256:59bd5a888296b623ae5a9efc8f18285c8ac1a8662e5d3775a0d2d736c66ba825",
                "sha256:c112794a20c5eda6a791cbec8700fb98eab30671a2248ac7e2059b475c46c45f",
                "sha256:bf8b736583f08c02b92f8a75ac5ea181e4d74107876177caa80ddad8b6b57a72",
                "sha256:6ef422d19214800243b28017d346c7ab9bfe63cb198a39312d1714394b232449"
            ]
        },

```

**理解:**

所有的Docker镜像都起始于一个基础镜像层，当进行修改或增加新的内容时，就会在当前镜像层之上，创建新的镜像层，

举一个简单的例子，假如基于Ubuntu Linux16.04 创建一个新的镜像，这就是镜像的第一层；如果在改镜像中田间python包，改镜像当前已经包含了3个镜像层，如下图所示(这只是一个用于演示的简单例子)

![image-20200609160037393](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200609160037393.png)

在添加额外的镜像层的同时，镜像始终是当前所有镜像的组合，理解这一点非常重要。下图举一个简单的例子。每个镜像层包含3个文件，而镜像包含了来自两个镜像层的6个文件。

![image-20200609160216783](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200609160216783.png)

上图中的镜像层跟图中的略有区别，主要目的是便于展示文件

下图中展示了一个稍微复杂的三层镜像，在外部看来整个镜像只有6个文件，这是因为最上层的文件7 是文件5的一个更新版本。

![image-20200609160433653](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200609160433653.png)

![image-20200609160449608](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200609160449608.png)

> 特点：

Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部！

这一层就是我们通常说的容器层，容器之下的都叫镜像层！

### commit镜像

```shell
docker commit 提交容器成为一个新的副本

# 命令和git原理建议
docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名:[TAG]
```

实战测试

```shell
#1 启动一个默认的tomcat

#2 发现这个默认的tomcat 是没有webapps应用，镜像的原因，官方的镜像默认 webapps下面是没有文件的！

#3 我自己拷贝进去了基本的文件

#4 将我们操作过的容器通过commit提交为一个镜像!我们以后就使用我们修改过的镜像即可，这就是我们自己的一个修改的镜像	
```

![image-20200609162806830](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200609162806830.png)

## 容器数据卷

### 什么是容器数据卷

==需求：数据可以持久化==

容器之间有一个数据共享的技术！Docker容器中产生的数据，同步到本地！

这就是卷技术！(目录的挂载，将容器的目录，挂载到linux上面！)

![image-20200609163713614](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200609163713614.png)

**总结一句话：容器的持久化和同步操作！容器间也是可以数据共享**

### 使用数据卷

> 方式一：直接使用命令来挂载 -v

```shell
docker run -it -v 主机目录：容器目录

#  测试
[root@iZ8vbcc06phmpnew8h8zvdZ ~]# docker run -it -v /home/ceshi:/home centos /bin/bash

# 启动起来的时候我们可以通过 docker inspect	容器id
```

![image-20200609164435800]https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200609164435800.png)

测试文件的同步

![image-20200609164602132](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200609164602132.png)

再来测试！

1. 停止容器
2. 宿主机上修改文件
3. 启动容器
4. 容器内的数据依旧是同步的！

![image-20200609164821820](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200609164821820.png)

好处:我们以后修改只需要再本地修改即可，容器内会自动同步！

### 实战：安装MySQL

思考：MySQL的数据持久化的问题

```shell
# 获取镜像
[root@iZ8vbcc06phmpnew8h8zvdZ ~]#  docker pull mysql:5.7

# 运行容器，需要做数据挂载		# 安装启动MySQL一定有密码
# 官方测试:docker run -name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

# 启动我们的mysql
-d 后台运行
-p 端口映射
-v 卷挂载
-e 环境配置
--name 容器名字
[root@iZ8vbcc06phmpnew8h8zvdZ ~]# docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7
```

![image-20200609165848497](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200609165848497.png)

### 具名和匿名挂载

```shell
# 匿名挂载
-v 容器内路径
docker run -d -p --name nginx01 -v /etc/nginx nginx

# 查看所有的卷的情况
docker volume ls
local das2112dass12d2112dsadasd1

 # 这里发现，这种就行匿名挂载。我们在 -v 只写了容器的内路径，没有写容器外的路径！
 
 #具名字挂载
 [root@iZ8vbcc06phmpnew8h8zvdZ ~]# docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx
bac44f8a55d1bb5f6436b3f227a8950cb6d756ecb75ef46f735f6329897eff2d
[root@iZ8vbcc06phmpnew8h8zvdZ ~]# docker volume ls
DRIVER              VOLUME NAME
local               7600b6aa549f4a32108dbc31fc688da76c11b4c0b004ba66b7cd5c2473b5920e
local               juming-nginx

# 通过-v 卷名：容器内路径
# 查看一下这个卷
```

![image-20200609170640314](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200609170640314.png)

所有的docker容器内的卷，没有指定目录的情况下都是在`/var/lib/docker/volumes/xxxx/_data`

我们可以通过具名字挂载可以方便的找到我们的一个卷，大多数情况在使用`具名挂载`

```shell
# 如何确定是具名挂载还是匿名挂载，还是指定路径挂载！
-v 容器内路径  #匿名挂载
-v 卷名:容器内路径   # 具名字挂载
-v /宿主机路径::容器内路径  # 指定路径挂载 ！
```

**扩展**

 ```shell
#  通过 -v 容器内路径：ro rw 改变读写权限
ro		readonly	#只读
rw		readwrite	#可读可写

# 一旦设置了这个容器权限 容器对于我们挂载出来的内容就有限定了！
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:rw nginx

#ro 只要看到了ro就说明这个路径只能通过宿主机来操作，容器内部是无法操作的！
 ```

### 初识Dockerfile

Dockerfile 就是用来构建docker镜像的构建文件！命令脚本！

通过脚本可以生成一个镜像，镜像是一层一层的，脚本就是一个个的命令

```shell
# 创建一个dockerfile文件 ，名字可以随机，建议Dockerfile
# 文件中的内容 指令(大写的) 参数
FROM centos

VOLUME["volume01","volume02"]

CMD echo "------end-----"
CMD /bin/bash

# 这里的每个命令，就是镜像的一层！
```

![image-20200610091729392](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200610091729392.png)

```shell
# 启动自己生成的容器
```

![image-20200610092913306](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200610092913306.png)

这个卷 一定和外部有一个同步的目录！

![image-20200610092955925](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200610092955925.png)

查看一下卷挂载的路径

![image-20200610093051811](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200610093051811.png)



## 数据卷容器

多个mysql 同步数据！

![image-20200610093428575](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200610093428575.png)

```shell
# 启动3个容器，通过我们刚才自己写的镜像启动
```

![image-20200610093705755](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200610093705755.png)

![image-20200610094330620](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200610094330620.png)

```shell
#测试:可以删除docker01，查看一下docker02和docker03是否还可以访问这个文件
#测试依旧可以访问
```

多个mysql实现数据共享

![image-20200610094734015](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200610094734015.png)

**结论:** 

容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用为止。

但是一旦你持久化到了本地，这个时候，本地的数据是不会删除的！

## Dockerfile

dockerfile 是用来构建docker镜像的文件！命令参数脚本!

构建步骤:

1. 编写一个dockerfile 文件
2. docker build 构建成为一个镜像
3. docker run运行镜像
4. docker push 发布镜像(DockerHub ,阿里云镜像仓库!)

github地址上的Dockerfile镜像

![image-20200610095734125](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200610095734125.png)

很多官方镜像都是基础包，很多功能没有，我们通常会自己搭建自己的镜像

官方既然可以制作镜像，那我们也可以！

### Dockerfile构建过程

**基础知识**

1. 每个保留关键字(指令) 都必须是大写字母
2. 执行从上到下顺序执行
3.  #  表示注释

4. 每一个指令都会创建提交一个新的镜像层，并提交！

![image-20200610100406666](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200610100406666.png)

dockerfile是面向开发的，我们以后要发布项目，做镜像，就需要编写dockerfile文件，镜像十分简单!

Docker镜像 逐渐成为了企业交付的标准，必须要掌握！

步骤：开发，部署，运维。。。缺一不可！

DockerFile: 构建文件，定义了一切的步骤 ，源代码

DockerImages:通过DockerFile构建生成的镜像，最终发布和运行的产品，原来是jar war

Docker容器：容器就是镜像运行起来提供服务器

### DockerFile的指令

```shell
FROM			#基础镜像，一切从这里开始构建
MAINTAINER		#镜像是谁写的，姓名+邮箱
RUN				#镜像构建的时候需要运行的命令
ADD				#步骤: tomcat镜像，这个tomcat压缩包！添加内容
WORKDIR			#镜像的工作目录	
VOLUME			#挂载的目录
EXPOST			#暴露端口配置
CMD				#指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT		#指定这个容器启动的时候要运行的命令，可以追加命令
ONBUILD			#当构建一个被继承 DockerFile 这个时候就会运行ONBUILD 的指令。触发命令。
COPY			#类似ADD，将我们的文件拷贝到镜像中
ENV				#构建的时候设置环境变量！
```

![image-20200610102509608](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200610102509608.png)

### 实战测试

Docker Hub中99%镜像都是从这个基础镜像过来的 FROM scratch，然后配置需要的软件和配置来进行的构建

![image-20200610102735632](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200610102735632.png)

> 创建一个自己的centos

```shell
# 1 编写Dockerfile的文件
[root@iZ8vbcc06phmpnew8h8zvdZ dockerfile]# cat mydocker 
FROM centos
MAINTAINER chenbin<1501695397@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "----end----"
CMD /bin/bash

# 2 通过这个文件构建镜像
# 命令 docker build -f dockerfile文件路径 -t 镜像
Successfully built 66f2392ef209
Successfully tagged mycentos:0.1

# 3 测试运行
```

对比：之前的原生的centos

![image-20200610105023703](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200610105023703.png)

我们增加之后的镜像

![image-20200610105055113](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200610105055113.png)

我们可以列出本地进行的变更历史

![image-20200610105216680](https://gitee.com/ChenbinRR/images/raw/master/typora-user-images/image-20200610105216680.png)

我们平时拿到一个镜像，可以研究一下它是怎么做的了！

> CMD 和 ENTRYPOINT 区别

```shell
CMD					# 指定的容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT			# 指定这个容器启动的时候要运行的命令，可以追加命令
```

测试cmd

```shell
[root@iZ8vbcc06phmpnew8h8zvdZ dockerfile]# vi dockerfile-cmd-test
FROM centos
CMD ["ls","-a"]

# 构建镜像
[root@iZ8vbcc06phmpnew8h8zvdZ dockerfile]# docker build -f dockerfile-cmd-test -t cmdtest .

# run运行，发现我们的ls -a 命令生效
[root@iZ8vbcc06phmpnew8h8zvdZ dockerfile]# docker run d3447e9d9501
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found
...

# cmd的请理一下 -l 替换了CMD ["ls","-a"] 命令，-l 不是命令所以报错！
```

测试ENTRYPOINT

```shell
[root@iZ8vbcc06phmpnew8h8zvdZ dockerfile]# vim dockerfile-cmd-entrypoint
[root@iZ8vbcc06phmpnew8h8zvdZ dockerfile]# docker build -f dockerfile-cmd-entrypoint -t entorypoint-test .
Sending build context to Docker daemon  4.096kB
Step 1/2 : FROM centos
 ---> 470671670cac
Step 2/2 : ENTRYPOINT ["ls","-a"]
 ---> Running in c6b283622b23
Removing intermediate container c6b283622b23
 ---> f3f933e23fe9
Successfully built f3f933e23fe9
Successfully tagged entorypoint-test:latest
[root@iZ8vbcc06phmpnew8h8zvdZ dockerfile]# docker run f3f933e23fe9
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
[root@iZ8vbcc06phmpnew8h8zvdZ dockerfile]# docker run f3f933e23fe9 -l
total 56
drwxr-xr-x  1 root root 4096 Jun 10 03:09 .
drwxr-xr-x  1 root root 4096 Jun 10 03:09 ..
-rwxr-xr-x  1 root root    0 Jun 10 03:09 .dockerenv
lrwxrwxrwx  1 root root    7 May 11  2019 bin -> usr/bin
drwxr-xr-x  5 root root  340 Jun 10 03:09 dev
drwxr-xr-x  1 root root 4096 Jun 10 03:09 etc
drwxr-xr-x  2 root root 4096 May 11  2019 home
lrwxrwxrwx  1 root root    7 May 11  2019 lib -> usr/lib
lrwxrwxrwx  1 root root    9 May 11  2019 lib64 -> usr/lib64
drwx------  2 root root 4096 Jan 13 21:48 lost+found
drwxr-xr-x  2 root root 4096 May 11  2019 media
drwxr-xr-x  2 root root 4096 May 11  2019 mnt
drwxr-xr-x  2 root root 4096 May 11  2019 opt
dr-xr-xr-x 96 root root    0 Jun 10 03:09 proc
dr-xr-x---  2 root root 4096 Jan 13 21:49 root
drwxr-xr-x 11 root root 4096 Jan 13 21:49 run
lrwxrwxrwx  1 root root    8 May 11  2019 sbin -> usr/sbin
drwxr-xr-x  2 root root 4096 May 11  2019 srv
dr-xr-xr-x 13 root root    0 Jun 10 01:21 sys
drwxrwxrwt  7 root root 4096 Jan 13 21:49 tmp
drwxr-xr-x 12 root root 4096 Jan 13 21:49 usr
drwxr-xr-x 20 root root 4096 Jan 13 21:49 var
```

Dockerfile 中很多的命令都十分的相似，我们需要了解它们的区别，我们最好的学习就是对比它们然后测试！





## Docker网络

























