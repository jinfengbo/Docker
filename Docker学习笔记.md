# Docker安装

## Docker的基本组成

![image-20210602155109081](https://github.com/jinfengbo/Docker/blob/main/images/image-20210602155109081.png)

**镜像(image)：**

Docker镜像就好比时一个模板，可以通过这个模板来厂家容器服务，tomcat镜像==>run==> tomcat1容器（提供服务器），通过这个镜像可以创建多个容器(最终服务运行或者项目运行，就是在容器中的)

**容器(container)：**

Docker利用容器技术，独立运行一个或者一组应用，通过镜像来创建的。

启动、停止、删除，基本命令！

目前可以把容器理解为一个简易的Linux系统

**仓库(repository)：**

仓库时存放镜像的地方

仓库分为公有仓库和私有仓库

公有仓库有  Docker Hub(默认是国外)





## 安装Docker

> 环境准备

1. 需要Linux的基础
2. CentOS 7
3. 我们使用Xshell连接远程服务器进行操作

> 环境查看

```shell
#系统内核
[root@web ~]# uname -r
3.10.0-862.el7.x86_64
```

```shell
#系统版本
[root@web ~]# cat /etc/os-release
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

> 安装

帮助文档：

```shell
# 1、卸载旧版本
	 sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
# 2、需要安装包
	 sudo yum install -y yum-utils
# 3、设置镜像仓库
	sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo（默认国外）
    
    sudo yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo（阿里云）

# 4、更新
	yum makecache fast
	
# 5、安装Docker相关 docker-ce 社区  ee企业版
	yum install docker-ce docker-ce-cli containerd.io
	
# 6、启动Docker
	systemctl start docker  启动docker
	systemctl enable docker 允许docker跟随系统启动
	
# 7、查看
	docker version
	
# 8、查看镜像
	docker images
```

## 卸载Docker

```shell
# 1、卸载依赖
	yum remove docker-ce docker-ce-cli containerd.io

# 2、删除资源
	rm -rf /var/lib/docker
# /var/lib/docker docker 的默认工作目录
```



# Docker的常用命令

## 帮助命令

```shell
docker version 		#显示docket的版本信息
docker info 		#显示docker的系统信息，包括镜像和容器的数量
docker 命令--help	   #帮助命令
```

帮助文档的地址：https://docs.docker.com/reference/



## 镜像命令

Docker images 查看所有主机镜像

```shell
#解释
REPOSITORY 	镜像仓库源
IMAGE ID 	镜像的ID
CREATED		镜像的创建时间
SIZE		镜像大小

#可选项
	-a, --all           #列出所有镜像
   	-q, --quiet         #只显示镜像ID
```

docker search 搜索镜像

```shell
[root@web ~]# docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   10944     [OK]
mariadb                           MariaDB Server is a high performing open sou…   4137      [OK]
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   812                  [OK]

#可选项，通过搜索过滤
--filter == STARS =3000  搜索出来的镜像STARS 大于3000
```

docker pull 下载镜像

```shell
#下载镜像docker pull 镜像名[:tag]
[root@web ~]#docker pull mysql
#如果不写tag 默认latest
```

docker rmi 删除镜像

```shell
[root@web ~]#docker rmi -f 镜像id   #删除指定镜像
[root@web ~]#docker rmi -f 镜像id  镜像id  镜像id   #删除多个镜像
[root@web ~]#docker rmi -f $(docker images -aq)   #删除全部镜像
```



## 容器命令

**说明：有了镜像才可以创建容器**

新建容器并启用

```shell
docker run  [可选参数] image

#参数说明
--name="Name"  容器名字  用来区分容器
-d		后台允许
-i -t	使用交互方式允许，进入容器查看内容
-P		指定容器端口  -p 8080:8080
	-p  主机端口：容器端口(常用)
	-p	容器端口
	-p	ip:主机端口:容器端口
-p		随机指定端口

#测试、启动并进入
[root@web ~]# docker run -it centos /bin/bash
[root@9ababc950840 /]# 

#查看容器内部的系统  内部centos命令不完善
[root@9ababc950840 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

#从容器中退出到主机
[root@9ababc950840 /]# exit
```

列出所有运行的容器

```shell
#docker ps 命令
	-a 列出历史运行过的容器
		n=? 列出最近创造的容器
	-q  列出容器id
[root@web ~]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@web ~]# docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                        PORTS     NAMES
9ababc950840   centos    "/bin/bash"   17 minutes ago   Exited (130) 16 seconds ago             festive_wozniak
[root@web ~]#
```

**退出容器**

```shell
exit  #退出容器并停止容器
ctrl+p+q  #退出容器不停止容器
```

**删除容器**

```shell
docker rm 容器ID				#删除指定容器，不能强制删除正在运行的容器
docker rm -f $(docker ps -aq)  #删除所有容器
docker ps -a -q |xargs docker rm #删除所有容器
```

**启动和停止容器**

```shell
docker start 容器ID     	#启动容器
docker restart 容器ID 	#重启容器
docker stop 容器ID		#停止当前正在运行容器
docker kill 容器ID		#强制停止当前容器
```



## 常用其他命令

```shell
#命令docker run -d 镜像名
[root@web ~]# docker run -d centos
#容器docker ps 发现容器停止
#常见问题：docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，自动停止。
#nginx容器启动后，发现自己没有提供服务，就会立刻停止就是没有程序了。
```

**查看日志**

```shell
docker logs -f -t --tail 日志条数 容器ID
[root@web ~]# docker logs --help

Usage:  docker logs [OPTIONS] CONTAINER

Fetch the logs of a container

Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --since string   Show logs since timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)
  -n, --tail string    Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     Show timestamps
      --until string   Show logs before a timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)


#自己编写一段shell脚本
[root@web ~]# docker run -d centos /bin/sh -c "while true;do echo kuangshen;sleep 1;done"
[root@web ~]# docker ps

#显示日志
-tf #显示格式、时间戳
--tail number  #显示日志条数
```

**查看容器中进程信息**

```shell
#命令 docker top 容器ID
[root@web ~]# docker top ad4b98dd559c
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                19739               19718               0                   14:46               pts/0               00:00:00            /bin/bash
```

**查看镜像元数据**

```shell
# 命令docker inspect 容器ID
[root@web ~]# docker inspect ad4b98dd559c
[
    {
        "Id": "ad4b98dd559c94eb78d6f41315590eaedf2c846f01ef54837af1690daa4cfcc1",     #容器ID
        "Created": "2021-06-03T06:46:48.941920427Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 19739,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2021-06-03T06:46:49.485244184Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:300e315adb2f96afe5f0b2780b87f28ae95231fe3bdd1e16b9ba606307728f55",
        "ResolvConfPath": "/var/lib/docker/containers/ad4b98dd559c94eb78d6f41315590eaedf2c846f01ef54837af1690daa4cfcc1/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/ad4b98dd559c94eb78d6f41315590eaedf2c846f01ef54837af1690daa4cfcc1/hostname",
        "HostsPath": "/var/lib/docker/containers/ad4b98dd559c94eb78d6f41315590eaedf2c846f01ef54837af1690daa4cfcc1/hosts",
        "LogPath": "/var/lib/docker/containers/ad4b98dd559c94eb78d6f41315590eaedf2c846f01ef54837af1690daa4cfcc1/ad4b98dd559c94eb78d6f41315590eaedf2c846f01ef54837af1690daa4cfcc1-json.log",
        "Name": "/serene_wescoff",
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
            "CgroupnsMode": "host",
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
                "LowerDir": "/var/lib/docker/overlay2/f28f38ffb254d05b17d2c2960535270e95fccfad7d21321983f685a1c82ff363-init/diff:/var/lib/docker/overlay2/036a776add56ed5664c993b2cb1ca9d9a479c9b29c5bbe9ca9d1e5f0d524bf08/diff",
                "MergedDir": "/var/lib/docker/overlay2/f28f38ffb254d05b17d2c2960535270e95fccfad7d21321983f685a1c82ff363/merged",
                "UpperDir": "/var/lib/docker/overlay2/f28f38ffb254d05b17d2c2960535270e95fccfad7d21321983f685a1c82ff363/diff",
                "WorkDir": "/var/lib/docker/overlay2/f28f38ffb254d05b17d2c2960535270e95fccfad7d21321983f685a1c82ff363/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "ad4b98dd559c",
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
                "org.label-schema.build-date": "20201204",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "324549daa56ac2312ffd1e5b1d0c9632b5c3b42584fb753e47349285889d9cf4",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/324549daa56a",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "5f39ae5affec634f791411db5f2bc4d9b56f1293f9544ff8af7dea7d3168b24c",
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
                    "NetworkID": "5451f1dcd48bbfea58e3ed37f97b09e1ba370e448a55ea2060083f6ce35882c7",
                    "EndpointID": "5f39ae5affec634f791411db5f2bc4d9b56f1293f9544ff8af7dea7d3168b24c",
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
#命令一
docker exec -it 容器id /bin/bash

[root@web ~]# docker exec -it ad4b98dd559c /bin/bash
[root@ad4b98dd559c /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@ad4b98dd559c /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 06:46 pts/0    00:00:00 /bin/bash
root        15     0  0 07:28 pts/1    00:00:00 /bin/bash
root        30    15  0 07:28 pts/1    00:00:00 ps -ef


#命令二
docker attach 容器ID
[root@web ~]# docker attach ad4b98dd559c

#docker exec #进入容器后开启一个新的终端，可以在里面操作（常用）
#docker attach #进入容器正在执行的终端，不会启动新的进程
```

**从容器内拷贝文件到主机**

```shell
#命令
docker cp 容器ID：容器路径  目的主机路径
[root@web ~]# docker cp ad4b98dd559c:/home/text.txt /home
```

## 命令小结

```shell
attach			#当前 shell 下 attach 连接指定运行镜像
build			#通过 Dockerfile 定制镜像
commit			#提交当前容器为新的镜像
cp				#从容器中拷贝指定文件或目录到宿主机中
create			#创建一个新的容器，同run，但不启动容器
diff			#查看docker容器变化
events			#从docker服务获取容器实时事件
exec			#在已存在的容器上运行命令
export			#导出容器的内容流作为一个tar归档文件（对应import）
history			#展示一个镜像形成历史
images			#列出系统当前镜像
import			#从tar包中的内容创建一个新的文件系统映像（对应export）
info			#显示系统相关信息
inspect			#查看容器详细信息
kill			#强制停止指定docker容器
load			#从一个tar包中加载一个镜像（对应save）
login			#注册或登录一个docker源服务器
logout			#从当前docker registry退出
logs			#输出当前容器日志信息
port			#查看映射端口对应的容器内部源端口
pause			#暂停容器
ps				#列出容器列表
pull			#从docker镜像源服务器拉取指定镜像或库镜像
push			#推送指定镜像或者库镜像至docker源服务器
restart			#重启运行的容器
rm				#移除一个或者多个容器
rmi				#移除一个或者多个镜像（无容器使用该镜像才可删除，否则需删除相关容器才可继续或 -				  f 强制删除）
run				#创建一个新的容器并运行一个命令
save			#保存一个镜像为一个tar包（对应load）
search			#在docker hub中搜索镜像
start			#启动容器
stop			#停止容器
tag				#给源中镜像添加标签
top				#查看容器中运行的进程信息
unpause			#取消暂停容器
version			#查看docker版本
wait			#截取容器停止时的退出状态值
```

## 作业练习

> **docker 安装nginx**

```shell
# 1、搜索nginx镜像  docker search 
# 2、下载nginx镜像  docker pull
# 3、运行测试

[root@web ~]# docker run -d --name nginx01 -p 8080:80 nginx
9fd67a929f14a26d403c20cc380b53ba557282a9d91bd8a0057a120bec1dcaca
[root@web ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                   NAMES
9fd67a929f14   nginx     "/docker-entrypoint.…"   4 seconds ago   Up 2 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   nginx01
[root@web ~]# curl localhost:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
[root@web ~]#

#进入容器

[root@web ~]# docker exec -it nginx01 /bin/bash
root@9fd67a929f14:/# ls
bin   dev                  docker-entrypoint.sh  home  lib64  mnt  proc  run   srv  tmp  var
boot  docker-entrypoint.d  etc                   lib   media  opt  root  sbin  sys  usr
root@9fd67a929f14:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@9fd67a929f14:/# cd /etc/nginx
root@9fd67a929f14:/etc/nginx# ls
conf.d  fastcgi_params  mime.types  modules  nginx.conf  scgi_params  uwsgi_params
root@9fd67a929f14:/etc/nginx#

```

> **docker 安装 tomcat**

```shell
#官方说明
 docker run -it --rm tomcat:9.0
--rm  #自动下载并允许，停止后删除该镜像

#docker tomcat 缺少linux命令，webapps没有文件 默认为最小镜像，保证最小环境。
#需复制webppas.dist到webapps 或更名为webapps
```

> 部署 es +kibana

```shell
#es 暴露端口多
#es	占用内存多
#es	数据一般需要放置在安全目录挂载
#--net somenetwork 网络配置

docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:tag

#启动后会占用大量内存  修改内存限制
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:tag

#命令    查看资源使用
docker stats
```

## 可视化

- portainer(先用这个)

```shell
docker run -d -p 8088:9000 \ --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

- Rancher(CI/CD再用)



**什么是portainer?**

docker图形化界面管理工具  提供一个后台面板方便操作！（不建议使用）



## commit镜像

```shell
docker commit 提交容器成为一个新的副本

#命令
docker commit -m="提交描述信息" -a="作者" 容器id  目标镜像名:[TAG]
```

实战测试

```shell
#启动一个默认tomcat

#发现默认tomcat没有设置webapps文件

#通过docker exec -it /bin/bash命令进入容器 将webapps.dirt/文件复制到webapps下
#复制命令 cp -r webapps.dirt/* webapps/  # 在tomcat目录下执行此命令

#通过commit命令提交为一个镜像
```

![image-20210604144758976](https://github.com/jinfengbo/Docker/blob/main/images/image-20210604144758976.png)

***commit可以理解等同于虚拟机快照**





# 容器数据卷

## 什么是容器数据卷

docker的理念回顾

将应荣环境打包成一个镜像

需求：数据持久化  例如：MySQL 数据存储在宿主机

容器数据卷解决数据共享技术，容器删除数据依然还在 。数据共享技术即Linux挂载。

## 使用数据卷

> 方式一：使用命令挂载 -v

```shell
docker run -it -v 主机目录:容器目录

#测试
docker run -it -v /home/ceshi:/home centos /bin/bash

启动后通过docker inspect 容器id 查看挂载
```

![image-20210604154640882](C:\Users\xusisi\AppData\Roaming\Typora\typora-user-images\image-20210604154640882.png)



## 实战：安装MySQL

MySQL的数据持久化问题

```shell
#获取镜像
[root@web ~]# docker pull mysql:5.7

#运行容器，并挂载数据   
#注意！安装启动mysql需要配置密码
#官方命令  docker run --name some-mysql -v /my/custom:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

#启动mysql
-d 后台运行
-p 端口映射
-v 目录挂载
-e 环境配置 （MYSQL_ROOT_PASSWORD 密码设置）
--name 容器名
[root@web ~]# docker run -d -p 3306:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=djtP@ssword --name mysql01 mysql5.7

```

