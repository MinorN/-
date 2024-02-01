# Docker

## 应用部署容器化的演变

### 应用部署的痛点

#### 应用程序部署流程

- 服务器配置运行环境
- 服务器安装中间件等
- 启动应用
- 数据库、缓存系统都要逐一部署

#### 应用程序扩容

多台服务器都要重复上述步骤，人力、物力耗费大

#### 应用程序多环境部署

本地、测试、生产环境可能会出现本地无问题，测试有问题，或本地、测试都没有问题，但是生产环境出现问题，必须针对每个环境排查问题

### 计算资源应用的演进

#### 物理服务器痛点

- 浪费时间，没有自动化运维手段时间开销巨大
- 配置变更也需要重复步骤

#### 使用虚拟机的优点与缺点

虚拟机就是采用虚拟化技术手段实现物理服务器计算资源打包的方式，为应用程序提供类物理服务器的运行环境，虚拟机能够实现应用程序和应用程序之间的隔离

优点：

- 相比物理服务器来说较为轻量
- 可控性强，当虚拟机出现故障可以直接使用新的虚拟机代替
- 可以达到应用程序运行环境的隔离
- 扩容、快速启动、迁移都较为方便

缺点：

- 占用物理服务器计算资源多，硬件资源消耗大
- 相比容器来说，启动时间还是过长
- 虚拟机对物理服务器硬件资源调用添加了调用链条，会浪费事件
- 迁移时，需要联动虚拟机硬盘中的操作系统一起迁移，迁移文件过大，耗时长

### 容器

#### 什么是容器？

容器就是虚拟机把操作系统部分去掉，也就是轻量级物理服务器计算资源的打包方式，也就是轻量级虚拟机，为应用程序提供类虚拟机运行环境，同时也可以在物理服务器中实现高密度部署

|              | 容器         | 虚拟机             |
| ------------ | ------------ | ------------------ |
| 隔离性       | 基于进程隔离 | 提供资源的完全隔离 |
| 启动时间     | 毫秒级/秒级  | 秒级/分钟          |
| 内核         | 共用宿机内核 | 使用独立内核       |
| 占用资源     | MB 级        | GB 级              |
| 系统支持容量 | 上千个       | 几十台             |

#### 容器的功能

- 应用在自己的容器内运行，实现应用程序之间的隔离
- 应用程序运行的容器可以生成应用程序模版文件（容器镜像），容器镜像不可变

#### 容器到底解决了什么问题？

- 快速交付/部署（镜像、容器）
- 资源的高效利用和隔离（高密度部署）
- 迁移简便、扩容缩容简单

## docker 命令

### 在容器运行`Nginx`

```bash
docker run -d nginx:latest
```

使用`ps`命令查看运行的进程

```bash
docker ps
```

### docker run

运行一个命令在容器当中

```bash
docker run -i -t --name c1 image bash
# -i -t 可简写成 -it
```

命令解释

1. `-i`：交互式
2. `-t` ：提供终端
3. `--name c1`：把运行的容器命名为 c1
4. `image`：镜像名称
5. `bash`：在 bash 中执行命令

运行的时候，会先在本地寻找镜像，没有则会下载该镜像，运行完成后会直接进入到容器当中，那么我们如何获取主机名？

```bash
hostname
```

查看网络

```bash
ip a s
```

显示系统中所有进程的详细信息

```bash
ps aux
```

查看用户

```bash
cat /etc/passwd
```

退出容器（容器会停止）并查看所有容器（包含已经停止的容器）

```bash
exit
docker ps --all
```

如何想要推出容器而不停止容器，按住`ctrl`然后按下`p`，`q`键即可

### docker ps

查看当前`docker`中正在运行的容器和已经停止的容器

```bash
docker ps --all
# docker ps -a
```

### docker inspect

主要获取容器的基础信息，想要查看信息，得有相关的容器，我们先创建一个容器(docker run)

```bash
docker inspect c2
# c2 是创建容器时候的容器名称
```

### docker exec

该命令是在容器中运行命令，也就是不需要进入容器，运行相应的命令

```bash
docker exec -i -t c2 command
```

解释

1. `c2`：容器名称
2. `command`：需要运行的命令

### docker attach

进入到**正在运行**的容器

```bash
docker attach c2
```

### docker stop

停止一个运行中的容器，可以传入容器的`id`，也可以是容器的名称（可以停止一个或者多个容器）

```bash
docker stop containerId
```

### docker start

启动一个已经被停止的容器，可以传入容器的`id`，也可以是容器的名称（可以重启一个或者多个容器）

```bash
docker start containerId
```

### docker top

查看容器中运行的进程信息，可以传入容器的`id`，也可以是容器的名称

```bash
docker top c2
```

### docker rm

删除一个或多个容器，需要容器先停止

```bash
docker rm containerId
```

添加`-f `能够强制删除容器

```bash
docker rm -f containerId
```

删除所有已经停止的容器

```bash
docker rm $(docker ps -a -q)
```

## Docker 容器镜像

`docker`容器镜像是什么？`docker`镜像是只读的容器模版，是`docker`容器的基础，为`docker`容器提供了静态文件系统运行环境

### 查看本地容器镜像

使用`docker images`查看

```bash
docker images
# 也可以使用
docker image list
```

### docker 搜索容器镜像

会遇到我们本地没有镜像，想要去下载如何搜索呢

```bash
docker search 镜像名称
```

也可以前往[官网](https://hub.docker.com/)

### docker 容器镜像下载

```bash
docker pull 镜像名称
```

### docker 容器镜像删除

成功删除的前提是改镜像没有被使用（包括正在运行的容器和已经停止的容器）

```bash
docker rmi 镜像名称/镜像ID
```

### 联合文件系统

在下载`docker pull`容器镜像的时候，会发现有多个进度条，这就是针对每个文件(目录)进行下载

联合文件系统是实现联合挂载技术的文件系统，为了实现在一个挂载点同时挂载多个文件系统，将挂载点的原目录与被挂载的内容进行整合，最终课件的文件系统包含整合之后各个层文件个目录

## docker 容器镜像操作命令

### docker commit

该命令可以从容器创建一个新的镜像，注意，该容器必须处于运行状态(`docker ps` 查看)

```bash
docker commit 容器id 镜像名称:镜像标签
```

### docker save

该命令可以将指定镜像保存成`tar`文件，也就是导出镜像

```bash
docker save -o 文件名称.tar 镜像名称
ls
# 就可以发现多了一个 tar 包，如何使用该文件？只需要 copy 到其他主机即可
```

### docker load

可以加载`docker save`产生的`tar`包

```bash
docker load -i 名称
```

### docker export

该命令可以将正在运行的**容器**导出为`tar`文件

```bash
docker export -o 名称.tar 容器Id
```

### docker import

该命令与`docker export`搭配使用，该命令用于从`docker export`导出的`tar`包中创建镜像

```bash
docker import 名称.tar 镜像名称:镜像标签
```

## docker 容器镜像加速器

当我们使用国外镜像的时候，不可避免的会出现下载速度慢等各种问题，所以我们就需要 docker 容器镜像加速器

这里推荐[阿里云容器镜像加速](https://help.aliyun.com/zh/acr/user-guide/accelerate-the-pulls-of-docker-official-images)，按照教程操作即可

## 容器镜像仓库

### docker hub

[docker hub](https://www.docker.com/products/docker-hub/) 是官方的容器镜像仓库，进行注册，然后点击 `Create a Repository`（其实可以理解成`github`）

然后在本地登陆`docker hub`

```bash
docker login
```

需要对自己的镜像重新打 tag

```bash
docker tag local-image:tagname new-repo:tagname
```

然后 push 即可

```bash
docker push new-repo:tagname
```

### harbor

[harbor](https://hezhiqiang-book.gitbook.io/docker/harbor)这个一般是企业自己搭建的`docker容器镜像仓库`，不过多介绍

## docker 容器化部署

### 前言

为什么要使用 docker 容器化部署应用？

1. 速度快
2. 快速部署、灵活性高
3. 资源利用率高

### Nginx 部署

可以直接看[官方 nginx 部署文档](https://hub.docker.com/_/nginx)

先看看我们是否有`nginx`镜像

```bash
docker images
# docker image ls
# 如果没有，可以拉取 nginx 镜像
# docker pull nginx:latest
```

接下来就要运行`nginx`容器，官方推荐有两个方法，这里都逐一介绍

1. 不在 docker host 暴露端口

顾名思义，我们不希望暴漏出来端口号

```bash
docker run --name my-nginx -v /some/content:/usr/share/nginx/html:ro -d nginx
```

-v 表示绑定一个数据卷，-d 表示在后台运行容器，`-v /some/content:/usr/share/nginx/html:ro`这段含义表示：把宿主机的/some/content 的文件夹挂载到容器中`/usr/share/nginx/html:ro`(ro 表示只读的权限)

接下来我们需要拿到对应的`ip`地址

```bash
docker inspect 容器Id
# 太多数据了，我们可以筛选一下
docker inspect 容器Id | grepIPAddress
```

我们访问一下

```bash
curl 获取的地址
```

如果报错也不用担心，我们在宿主机的`/some/content`（也就是刚才做到目录映射）下创建`index.html`即可

2. 暴漏端口

```bash
docker run --name my-nginx -d -p 80:80 -v /some/content:/usr/share/nginx/html:ro nginx
```

将宿主机的`80`端口提供给容器`80`端口使用，这样既可以在 docker 里面访问，也可以直接在宿主机 80 端口访问

接下来，我们需要修改`nginx`的配置文件，本质就是自己先写好一个`nginx`配置文件，然后覆盖`docker`容器内的配置文件即可

先可以查看一下`nginx`容器的进程

```bash
docker top 容器名称
```

发现有一个`master`进程，还有若干个`worker`进程

接下来我们需要配置`nginx`文件，我们先把原来的文件拷贝出来

```bash
docker cp 容器名称:/etc/nginx/nginx.conf 目标目录
# 注意 目标目录必须存在，如果不存在需要提前创建
```

然后修改`copy`过来的文件即可，修改完成后，我们需要重新运行一个`nginx`容器（刚才的容器删了即可）

```bash
docker run --name my-custom-nginx-container \
-v 本机目录/nginx.conf:/etc/nginx/nginx.conf:ro  \
-v /some/content:/usr/share/nginx/html:ro \
-d nginx
```

### MySQL 部署

这里是[官方教程](https://hub.docker.com/_/mysql)

#### 单节点 MySQL

```bash
# 拉取官方镜像，可以指定版本,版本号不写即使 latest
docker pull mysql:版本号
# 运行容器，暴漏端口号
docker run --name my-mysql \
-p 3306:3306 \
-v 宿主机目录:/var/log/mysql \ # 日志存储目录
-v 宿主机目录:/var/lib/mysql \ # 存放数据
-v 宿主机目录/myconf.d:/etc/mysql/conf.d \ # mysql 配置文件
-e MYSQL_ROOT_PASSWORD=my-secret-pw \ # mysql 密码，默认用户是 root
-d \
mysql:版本号
# 访问 MySQL
docker exec -it my-mysql mysql -uroot -pmy-secret-pw
```

#### MySQL 主从复制部署

##### MySQL 主节点部署

和刚才部署方式一样

```bash
docker run --name my-mysql-master \
-p 3306:3306 \
-v 宿主机目录:/var/log/mysql \ # 日志存储目录
-v 宿主机目录:/var/lib/mysql \ # 存放数据
-v 宿主机目录/myconf.d:/etc/mysql/conf.d \ # mysql 配置文件
-e MYSQL_ROOT_PASSWORD=my-secret-pw \ # mysql 密码，默认用户是 root
-d \
mysql:版本号
# 如果按照顺序操作 3306 端口可能被占用，删除之前创建的容器或者换一个端口均可
```

##### MySQL 主节点配置

刚才把`/etc/mysql/conf.d`映射到了我们的目录下，只需要在目录下创建 mysql 配置文件即可

##### MySQL 从节点部署

```bash
docker run --name my-mysql-slave \
-p 3307:3306 \
-v 宿主机目录:/var/log/mysql \ # 日志存储目录
-v 宿主机目录:/var/lib/mysql \ # 存放数据
-v 宿主机目录/myconf.d:/etc/mysql/conf.d \ # mysql 配置文件
-e MYSQL_ROOT_PASSWORD=my-secret-pw \ # mysql 密码，默认用户是 root
-d \
--link my-mysql-master:my-mysql-master # 第一个是主节点名称，第二个是起了个别名
mysql:版本号
# 如果按照顺序操作 3307 端口可能被占用，删除之前创建的容器或者换一个端口均可
```

##### MySQL 从节点配置

部署也和主节点一样，修改内部`server_id`即可

##### 配置主从复制

###### master 节点配置

`master`节点需要配置给谁授权，谁需要同步

```bash
# 先登录
mysql -h 主机ip -uroot -p密码 -P端口号
# 授权
grant replication slave on *.* to 'backup'@'' identified by '123456';
#  on *.* 表示同步所有的数据库 to 'backup'@'' 给ip主机用户名为backup identified by '123456' 验证码为 '123456'
# 刷新授权表
flush privileges
# 重启容器，先退出mysql
exit
docker restart 容器名
# 再次进入 mysql，然后查看一下
show master status\G
```

###### 从节点配置

```bash
# 先重启从节点的容器
docker restart 容器名
# 进入配置
mysql -h 主机ip -uroot -p密码 -P端口号
# 连接 master 节点
change master to
master_host='mysql-master',
master_user='backup',
master_password='123456',
master_log_file='mysql-bin.000001', # mysql-bin.000001 是刚才查看的 show master status\G
master_log_pos=154,
master_port:3306;
# 启动
start slave;
# 查看 Slave_IO_Running 和 Slave_SQL_Running 状态可以检查是否成功
show slave status\G
```

验证是否能够同步，也就是主节点数据能否同步到从节点

```bash
# 进入主节点 mysql
mysql -h 主机ip -uroot -p密码 -P端口号
show databases;
create database my-mysql-test;
# 进入从节点查看是否同步成功(在同一个终端记得退出 exit)
mysql -h 主机ip -uroot -p密码 -P端口号
show databases;
```

这样我们就可以在主节点进行写入数据，从节点负责读取数据，做到了读写分离

## Dockerfile

`Dockerfile`是能够被`Docker`程序解释的脚本，我们可以在`Dockerfile`里面添加、修改指令，就能够生成我们自定义的容器镜像

### 指令

分为两大类：**构建类指令**，**设置类指令**。构建类指令主要用于构建镜像，设置类指令主要设置镜像的属性

| 指令    | 描述                                                  |
| ------- | ----------------------------------------------------- |
| FROM    | 构建新镜像基于的基础镜像                              |
| LABEL   | 标签                                                  |
| RUN     | 构建镜像时运行的`shell`命令                           |
| COPY    | 拷贝文件/目录到镜像                                   |
| ADD     | 解压压缩包并拷贝                                      |
| ENV     | 设置环境变量                                          |
| USER    | 为 `RUN` `CMD` `ENTRYPOINT` 执行命令指定运行用户      |
| EXPOSE  | 声明容器运行的服务端端口                              |
| WORKDIR | 为 `RUN` `CMD` `ENTRYPOINT` `copy` `add` 设置工作目录 |
| CMD     | 运行容器时默认执行，如果有多个`CMD`指令，最后一个生效 |

1. `FROM`：`FROM:<image>:<tag>`，eg：`FROM: Nginx:latest`

2. `RUN`：`RUN`命令有两种格式

   1. `shell`格式：`RUN <命令>`，eg：`RUN ls -a`
   2. `exec`格式：`RUN [可执行文件，参数1，参数2]`，eg：`RUN ["/bin/bash", "ls","-a"]`

   如果要执行多条命令，建议使用 && 符号连接，避免镜像分层

3. `CMD`：`CMD`命令有三种格式

   1. `CMD ["executable","参数1","参数2"]`
   2. `CMD ["参数1","参数2"]`
   3. `CMD command 参数1 参数2`

4. `EXPOSE`：`EXPOSE <port> [<port>...]`，eg：`EXPOSE 80 3306 8080`

5. `ENV`：`ENV <key> <value>`或`ENV <key>=<value>`

6. `ADD`：`ADD <src> <dest>`

7. `CPOY`：`CPOY <src> <dest>`

8. `ENTRYPOINT`：该命令与`CMD`命令相似，不同点是如果用户启动容器的时候指定了运行的命令，`CMD`会被覆盖，而`ENTRYPOINT`不会被覆盖，格式除了`CMD`命令第二种格式以外都支持

9. `VOLUME`：把宿主机的目录与容器的目录做映射，但是只能指定挂载点，docker 宿主机映射的目录会自动生成,`VOLUMN ["mountpoint"]`

10. `USER`：`USER xxx`

11. `WORKDIR`：`WORKDIR 目录`

### Dockerfile 基本组成

- 基础镜像信息
- 维护者信息
- 操作指令
- 容器启动时需要执行的指令

### 使用 Dockerfile 生成 Nginx 容器镜像

先创建一个目录，在目录下创建`Dockerfile`文件，编写完成后`docker build`

先创建一个`nginx`的`index.html`，写啥都行

```dockerfile
FROM centos:centos7	# 基础镜像
MAINTAINER "MinorN"	# 维护者，一般邮箱为主

RUN yum -y install wegt

RUN wegt -O /etc/yum.repos.d/epel/repo http://mirrors.aliyun.com/repo/epel-7.repo

RUN yum -y install nginx # 安装nginx

ADD index.html /user/share/nginx/html

RUN echo "daemon off;" >> /etc/nginx/niginx.conf # 告诉nginx关闭守护进程，不会被关闭

EXPOSE 80

CMD /usr/sbin/nginx # 每次启动该容器的时候都会启动 nginx
```

然后使用`docker build -t my-centos7-nginx:v1 .` 生成镜像

### Dockerfile 生成容器镜像优化

我们在生成自己的镜像的时候，会发现容器镜像大小过大，此时就需要对镜像进行相应的优化

#### 减少镜像分层

在进行编写`Dockerfile`文件过程中，最长使用的指令就是`RUN`，建议把能够合并的`RUN`指令合成一条，可以有效地减少镜像分层

#### 清理无用数据

每次`RUN`形成新的一层，如果没有在同一层删除，都会带到下一层，所以需要在每一层清理对应的残留数据，比如下载的安装包，安装完成后及时删除等

#### 多阶段构建镜像

这里不过多赘述，有需要[查看一下](https://yeasy.gitbook.io/docker_practice/image/multistage-builds)即可

## Docker 容器网络与通信原理

### Docker 容器默认网络模型

![docker容器网络模型原理](./images/docker容器网络模型原理.png)

- docker0：
  - 是一个网桥
  - 通过网桥可以将不同的端口连接起来
  - 可以实现类交换机多对多的通信
- veth：
  - 虚拟以太网设备
  - 成对出现，用于解决网络命名空间之间的隔离
  - 一端连接容器网络命名空间，一段连接主机网络命名空间

### Docker 容器默认网络模型工作原理

容器如何访问外部网络？数据包->eth0->veth->docker0->eht0->外网

外网如何访问容器？外网->数据包->eth0->docker0->veth->eth0

### Docker 容器四种网络模型

总共分为四种：

1. `Bridge`模式：这个是`docker`容器默认的网络模式
2. `Host`模式：可以让容器和宿主机共享同一个网络命名空间
3. `Container`模式：新创建的容器和已经存在的容器共享一个网络命名空间使用
4. `None`模式：不能够连接外网

如何创建一个新的`Docker`网络？

```bash
docker network create -d 网络模型 名称
```

运行一个容器并且连接到新的`Docker`网络

```bash
docker run -itd --name my-docker-network --network 名称 镜像
```

### 查看已有的网络模型

```bash
docker network ls
```

查看已有网络模型的具体信息

```bash
docker network inspect 网络模型
```

### 创建网络模型

#### Bridge

```bash
docker network create -d bridge --subnet "192.168.100.0/24" --gateway "192.168.100.1" -o com.docker.network.bridge.name my-bridge
# --subnet 指定网段
# --gateway 网关
# -o 可以修改对应网络模型里面的 option
docker network ls
# 可以查看到我们新创建的网络模型
```

运行容器连接该网络

```bash
docker run -it --network my-bridge --rm centos:latest
# --rm 表示退出时删除
```

#### Host

操作方法一致(`-d host`)

#### Container

该网络模型要求现有一个运行的容器

```bash
docker run -it --name c1 --rm centos
# 这里使用了默认的网络模型
docker run -it --name c2 --network container:c1 --rm centos
# 这里创建了第二个容器c2，使用的网络模型是c1的
```

#### None

操作方法与`bridge`一致(`-d none`)，该模式无法连接外网

### 跨 Docker Host 容器通信

#### 必要性

因为 docker 容器在运行的时候，无法直接被外界访问，如果采取暴露端口的方式，那么会导致宿主机端口被严重消耗，所以需要能够实现不同的`docker host `方便访问其他`docker host`上容器提供的服务

#### 实现

1. Docker 原生方案

   - `overlay`：基于 VXLAN 封装实现 Docker 原生 overlay 网络

   - `macvlan`：Docker 主机网卡接口逻辑上分为多个子接口，每一个子接口标识一个 VLAN，容器接口直接连接 Docker Host

   - 网卡接口

2. 第三方方案

   - 隧道方案：`Flannel`、`Weave`、`OpenvSwitch`
   - 路由方案：`Calico`

## Docker 容器数据持久化

### 基本介绍

由于 Docker 容器是由容器镜像生成的，但是 Docker 容器在停止后内部会销毁，所以就需要数据持久化

### 数据持久化存储方式

- `docker run -v`：运行容器时，直接挂在本地目录到容器中
- `volumes`：数据卷，是 Docker 默认的存储方式
- `bind mounts`：将宿主机目录的任意位置文件/目录挂载到容器中

### 应用

#### `Docker run -v`

```bash
docker run -d --name test -v /my/docker:/usr/share/nginx/html nginx:latest
```

#### `volumes`

```bash
# 创建一个数据卷
docker volume create nginx-volume
# 所在位置是在 /var/lib/docker/volumes
# 查看已经创建的数据卷
docker volume ls
# 使用该数据卷
docker run -d --name my-nginx --mount src=nginx-volume,dst=/usr/share/nginx/html nginx:latest
```

#### `bind mounts`

```bash
docker run -d --name my-nginx --mount type=bind,src=/opt/mynginx,dst=/usr/share/nginx/html niginx:latest
```

## Docker Compose
