# docker-课堂笔记

## 一、简介

​	我们写的代码会接触到好几个环境：开发环境、测试环境以及生产环境等等。多种环境去部署同一份代码，由于环境原因往往会出现**软件跨环境迁移的问题**（也就是**“水土”不服**）

![1580809997700](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/1580809997700-1610464022747.png)

针对这种问题如何解决？我们可以将工程及此工程依赖的所有软件打包到一个容器中统一部署

![1580810113577](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/1580810113577-1610464022747.png)

### 1. docker简介

​	Docker 是一个开源的应用容器引擎，基于 [Go 语言](https://www.runoob.com/go/go-tutorial.html) 并遵从 Apache2.0 协议开源。它可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

​	容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

​	Docker 从 17.03 版本之后分为 CE（Community Edition: 社区版） 和 EE（Enterprise Edition: 企业版），我们用社区版就可以了。

![img](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/f703738da977391281957edbf0198618377ae2dd.png)

### 2. docker和虚拟机对比

#### 本质的区别

![preview](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/20006deca0fccda0d536edd626835e9e_r.jpg)

- 容器

​    容器是应用层的一个抽象，它将代码和依赖关系打包在一起。多个容器可以在同一台机器上运行，并与其他容器共享操作系统内核，每个容器作为独立的进程在用户空间运行。容器比虚拟机占用的空间更小（容器镜像的大小一般为几十MB），可以处理更多的应用，对虚拟机和操作系统的要求也更低。

- 虚拟机

​    虚拟机(VM)是物理硬件的抽象，将一台服务器变成多台服务器。管理程序允许多个虚拟机在一台机器上运行。每个虚拟机都包括一个操作系统、应用程序、必要的二进制文件和库的完整副本--占用几十GB的空间。虚拟机的启动速度也会很慢。

#### 使用的区别

![image-20210105214752352](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210105214752352.png)

### 3. 安装docker

#### docker架构

![img](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/576507-docker1.png)

##### 镜像image

​	Docker 镜像（Image），就相当于是一个文件系统，是用于创建 Docker 容器的模板。也可以将镜像当作容器的“源代码”。镜像体积很小，非常“便携”，易于分享、存储和更新。

##### 容器container

​	容器是独立运行的一个或一组应用，是镜像运行时的实体。（精简版的虚拟机，其中安装好了软件）

##### 注册中心(仓库)registry

​	Docker 仓库用来保存镜像，有点类似于Maven的远程仓库。Registry 分为公共和私有两种。Docker 公司运营公共的 Registry 叫做 Docker Hub。用户可以在 Docker Hub 注册账号，分享并保存自己的镜像（说明：在 Docker Hub 下载镜像巨慢，可以自己构建私有的 Registry）。

#### 安装docker

​	Docker 官方建议在 Ubuntu 中安装，因为 Docker 是基于 Ubuntu 发布的。

​	由于我们学习的环境都使用的是 CentOS，因此这里我们将 Docker 安装到 CentOS 上。

​	注意：这里建议安装在 CentOS7.x 以上的版本，在 CentOS6.x 的版本中，安装前需要安装其他很多的环境而且 Docker 很多补丁不支持更新。

##### 安装docker

启动CentOS7后，使用SecureCRT连接上CentOS7，然后依次执行命令（要联网）：

```shell
# 更新系统
yum update

# 安装yum-utils工具和两个驱动依赖
yum install -y yum-utils device-mapper-persistent-data lvm2

# 设置使用阿里云的yum源
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 安装docker
yum install docker-ce

# 查看docker版本
docker -v
```

##### 设置注册中心

​	如果不设置注册中心，将使用默认的docker hub，速度非常慢。

​	USTC（中科大的镜像服务）是老牌的Linux镜像服务提供者（注册中心Registry），在Ubuntu5.04版本就在使用。USTC的docker镜像速度加载很快。USTC Docker mirror的优势之一是不需要注册，是真正的公共服务。

​	设置步骤：

1. 创建文件夹：`mkdir /etc/docker`
2. 编辑文件daemon.json： `vim /etc/docker/daemon.json`
3. 如下设置注册中心后，保存并退出vim

```json
{
"registry-mirrors":["https://docker.mirrors.ustc.edu.cn"]
}
```

1. 重启docker服务：`systemctl restart docker`
2. 查看docker信息：`docker info`

## 二、常用命令【重点】

### 1. 服务管理

```shell
# 启动docker服务
systemctl start docker

# 查看docker运行状态
systemctl status docker

# 重启docker服务
systemctl restart docker

# 关闭docker服务
systemctl stop docker

# 设置docker开机自启动
systemctl enable docker
```

- 注意：在关闭CentOS之前，建议先关闭docker  `systemctl stop docker`，然后再关闭CentOS

### 2. 镜像管理

#### 查看docker帮助

- 语法： `docker --help`

#### 拉取镜像

##### 搜索镜像

- 语法：`docker search 镜像名称`

  如果配置了USTC，则从USTC上搜索镜像；否则默认从Docker Hub上搜索镜像。

- 示例：`docker search centos`

![image-20210105223353298](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210105223353298.png)

##### 拉取镜像

- 语法： `docker pull 镜像名称:tag`

  从注册中心把镜像拉取到本地。拉取下来的镜像，在`/var/lib/docker`目录下存储

  `tag`：标签名称，类似于版本号，如果不知道版本号，可以从DockerHub里查询

- 示例：

  - `docker pull centos`：拉取centos最新版(lastest)
  - `docker pull centos:7`：摘取centos，标签名称7的

![image-20210105223838745](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210105223838745.png)

#### 查看镜像

##### 查看所有镜像

- 语法：`docker images`

![image-20210105224303587](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210105224303587.png)

#### 删除镜像

##### 删除某个镜像

- 语法：`docker rmi 镜像ID`

![image-20210105224947330](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210105224947330.png)

##### 删除所有镜像

- 注意：必须先关闭镜像相关的所有容器，才可以删除镜像；如果有镜像关联的容器正在运行，则不能删除
- 语法： `docker rmi $(docker images -q)`

![image-20210105225717345](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210105225717345.png)

#### 小结

- 查看宿主机里的镜像：`docker images`
- 从registry里搜索镜像：`docker search 镜像名称`
- 从registry里拉取镜像：`docker pull 镜像名称:tag`
- 删除宿主机里的镜像：`docker rmi  镜像名称:tag`  或者  `docker rmi 镜像id`

### 3. 容器管理

#### 创建容器

##### 创建交互式容器

- 说明：交互式容器，创建后直接进入命令行交互界面，**退出交互界面后自动关闭容器**
- 语法：`docker run -it --name=容器名称 镜像名称:标签 /bin/bash`
- 参数：
  - `i`： interactive，表示开启标准输入（STDIN），启动容器
  - `t`：tty，表示给容器分配一个伪tty终端，可以在伪终端里通过命令与容器进行交互
  - `name`：给创建的容器起个名称
- 示例：`docker run -it --name=centos1 centos:7 /bin/bash`

![image-20210105231618081](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210105231618081.png)

##### 创建守护进程式容器

- 说明：守护进程式容器，创建后以后台进程的方式在运行。
- 注意：创建守护进程式容器后，不会自动进入容器，需要使用`docker exec`命令进入容器
- 语法：
  - 创建容器：`docker run -id --name=容器名称 镜像名称:标签` 
  - 进入容器：`docker exec -it 容器名称 /bin/bash`
- 参数：
  - `i`：interactive，表示开启标准输入（STDIN）
  - `t`：tty，表示分配一个伪tty终端
  - `d`：daemon，表示以守护进程的方式运行容器（即后台运行）
  - `name`：给创建的容器起个名称
- 示例：
  - 创建容器：`docker run -id --name=centos2 centos:7`
  - 进入容器：`docker exec -it centos2 /bin/bash`

![image-20210105232216025](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210105232216025.png)

#### 查看容器

##### 查看正在运行的容器

- 语法：`docker ps`

![image-20210105233129106](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210105233129106.png)

##### 查看所有容器

- 语法：`docker ps -a`

![image-20210105233228780](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210105233228780.png)

##### 查看最后一次运行的容器

- 语法：`docker ps -l`

![image-20210105233255514](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210105233255514.png)

##### 查看容器信息

- 查看容器所有信息：`docker inspect 容器名称`

![image-20210106200320005](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106200320005.png)

- 查看容器的ip地址：`docker inspect -f {{.NetworkSettings.Networks.bridge.IPAddress}} 容器名称`

![image-20210106200240154](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106200240154.png)

#### 启动关闭

##### 关闭容器

- 语法：`docker stop 容器名称/容器ID`
- 示例：`docker stop centos2`

![image-20210105233507366](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210105233507366.png)

##### 启动容器

- 说明：用于已启动过的容器，下次可以直接启动，无需再次创建
- 语法：`docker start 容器名称/容器ID`
- 示例：`docker start centos1`

![image-20210105233541153](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210105233541153.png)

#### 删除容器

##### 删除某个容器

- 说明：必须要先关闭容器，才可以删除；正在运行的容器不能删除
- 语法：`docker rm 容器名称/容器ID`
- 示例：`docker rm centos2`

![image-20210105233714336](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210105233714336.png)

##### 删除所有容器

- 说明：必须要先关闭容器，才可以删除；正在运行的容器不能删除

- 语法：`docker rm $(docker ps -aq)`

  > 一键停止所有容器并全部删除：`docker stop $(docker ps -q) & docker rm $(docker ps -aq)`

![image-20210105234019636](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210105234019636.png)

#### 小结

- 创建容器
  - 创建交互式容器：
    - 创建之后立即进入了容器内部，退出容器后容器会自动关闭
    - 语法：`docker run -it --name=容器名称 镜像名称:tag /bin/bash`
  - 创建守护式容器：
    - 创建之后容器是在后台运行，默认不会进入到容器内部
    - 创建：`docker run -id --name=容器名称 镜像名称:tag`
    - 进入容器：`docker exec -it 容器名称 /bin/bash`
- 查看容器：
  - `docker ps`：查看正在运行的容器
  - `docker ps -a`：查看所有容器，包括没有运行的容器
- 关闭容器：`docker stop 容器名称`
- 启动容器：`docker start 容器名称`
- 删除容器：`docker rm 容器名称`

## 三、应用部署【重点】

为了方便后续操作，我们关闭CentOS的防火墙

```shell
#关闭防火墙
systemctl stop firewalld
#防火墙和docker有关联，需要重启docker
systemctl restart docker
```

### 1. 部署MySql

#### 1.1 拉取镜像

- 命令：`docker pull mysql:5.7`

![image-20210106174041393](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106174041393.png)

- 查看镜像：`docker images`

![image-20210106174102938](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106174102938.png)

#### 1.2 创建容器

##### 创建容器

- 命令：`docker run -id --name=mysql_docker -p 33306:3306 -e MYSQL_ROOT_PASSWORD=root mysql:5.7 --character-set-server=utf8`
- 参数：
  - `p`：设置端口映射，格式是`-p 宿主机端口:容器端口`，这里设置为`-p 3306:3306`，外界要通过宿主机端口来访问MySql
  - `e`：设置环境变量，这里设置的`MYSQL_ROOT_PASSWORD`是MySql里root帐号的密码

##### 端口映射的说明

宿主机和容器的关系：

- docker所在的主机，称为宿主机；docker里还有容器

为什么要做端口映射：

- 每个容器都相当于一个独立的服务器，服务器之间是一个小型局域网，外界的客户端不能访问某一个容器
- 所以要做一个端口映射：宿主机的一个端口号 <----> 容器里的一个端口号。两个端口可以不同，但是建议相同
- 这样：客户端<---->宿主机端口号<---->容器里端口<---->容器里的软件

![image-20210106120903497](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106120903497.png)

##### 进入容器登录MySql

- 进入容器：`docker exec -it mysql_docker /bin/bash`
- **在容器里**登录MySql：`mysql -uroot -p`  回车后输入密码 root

![image-20210106174518088](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106174518088.png)

#### 1.3 连接测试

注意要关闭Linux里的防火墙(`systemctl stop firewalld`)，或者设置防火墙开放33306端口；否则外界不能远程连接登录。

使用SQLyog远程登录容器里的MySql：

![image-20210106175103495](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106175103495.png)

#### 小结

1. 拉取镜像：`docker pull mysql:5.7`
2. 创建容器：`docker run -id --name=mysql -p 33306:3306 -e MYSQL_ROOT_PASSWORD=root mysql:5.7 --character-set-server=utf8`
3. 使用SQLyog连接容器里的mysql：  `宿主机的ip:33306`

### 2. 部署Tomcat

#### 2.1 拉取镜像

- 拉取镜像：`docker pull tomcat:8.5`

![image-20210106180428134](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106180428134.png)

- 查看镜像：`docker images`

![image-20210106180456169](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106180456169.png)

#### 2.2 创建容器

##### 创建容器

- 创建容器：`docker run -id --name=mytomcat -p 8080:8080 tomcat:8.5`

![image-20210106180710119](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106180710119.png)

##### 上传项目到Linux

- 使用SecureFX把资料里的《travel》文件夹上传到Linux的`/root`目录下

##### 把项目拷贝到容器里

- 从宿主机里拷贝文件到容器：
  - 语法：`docker cp 宿主机文件(夹)  容器名称:文件(夹)路径`
- 把travel拷贝到容器里，命令：
  - 切换到root目录：`cd /root`
  - 拷贝文件到容器：`docker cp ./travel/ mytomcat:/usr/local/tomcat/webapps`

![image-20210106181431791](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106181431791.png)

#### 2.3 访问测试

- 在浏览器里输入地址，访问容器里部署的travel项目

![image-20210106181556026](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106181556026.png)

#### 小结

- 拉取镜像：`docker pull tomcat:8.5`
- 创建容器：`docker run -id --name=tomcat -p 8080:8080 tomcat:8.5`
- 把项目文件夹上传到容器里
  - 把文件夹，从我们的电脑上上传到宿主机：SecureFX
  - 再把宿主机的文件夹，拷贝到容器里：`docker cp ./travel tomcat:/usr/local/tomcat/webapps`
- 访问测试：打开浏览器输入地址：`http://宿主机ip:8080/travel`

### 3. 部署redis

#### 3.1 拉取镜像

- 拉取镜像：`docker pull redis:5.0`

![image-20210106200511952](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106200511952.png)

- 查看镜像：`docker images`

![image-20210106200617257](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106200617257.png)

#### 3.2 创建容器

- 创建容器：`docker run -id --name=myredis -p 6379:6379 redis:5.0`

![image-20210106200748325](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106200748325.png)

#### 3.3 访问测试

1. 在自己的物理机电脑上找到`redis-cli.exe`所在的目录
2. 打开cmd，切换到`redis-cli.exe`所在目录
3. 输入命令连接docker里的redis：`redis-cli.exe -h 宿主机ip -p 宿主机的映射端口`

![image-20210106201152624](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106201152624.png)

### 4. 部署Nginx

#### 4.1 拉取镜像

- 拉取镜像：`docker pull nginx`

![image-20210106201718797](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106201718797.png)

- 查看镜像：`docker images`

![image-20210106201802419](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106201802419.png)

#### 4.2 创建容器

- 创建容器：`docker run -id --name=mynginx -p 80:80 nginx`

![image-20210106202129647](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106202129647.png)

#### 4.3 访问测试

- 打开浏览器，输入地址：`http://宿主机的ip:80/`

![image-20210106202452359](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106202452359.png)

#### 4.4 配置反向代理

##### 查看目标服务器的地址

我们以mytomcat容器作为目标服务器，查看它的ip地址是

- 命令：`docker inspect -f {{.NetworkSettings.Networks.bridge.IPAddress}} mytomcat`

![image-20210106202908225](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106202908225.png)

##### 修改配置文件

直接在容器里编辑文件是非常不方便的，所以我们把文件拷贝到宿主机里修改后再拷贝回去。

###### a. 从mynginx容器里拷贝配置文件到宿主机

- 命令：`docker cp mynginx:/etc/nginx/conf.d/default.conf /root/`

![image-20210106202657745](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106202657745.png)

###### b. 使用vim编辑配置文件

- 切换到`/root`：`cd /root`
- 编辑配置文件：`vim default.conf`
- 修改为如下内容：

![image-20210106203058390](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106203058390.png)

###### c. 把配置文件拷贝到mynginx容器里

- 命令：`docker cp ./default.conf mynginx:/etc/nginx/conf.d/default.conf`

![image-20210106203417338](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106203417338.png)

##### 重启mynginx容器

- 启动mytomcat容器：`docker start mytomcat`
- 重启mynginx容器：`docker restart mynginx`

![image-20210106203751149](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106203751149.png)

##### 访问测试

![image-20210106203709095](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106203709095.png)

#### 小结

部署nginx

1. 拉取镜像：`docker pull nginx`
2. 创建容器：`docker run -id --name=nginx -p 80:80 nginx`
3. 已经可以使用浏览器，访问nginx了：`http://宿主机ip:80`

配置反向代理

1. 查询被代理的服务器的地址，Tomcat的访问地址：`http://192.168.247.140:8080`， `http://宿主机ip:8080`

2. 把nginx的配置文件，从容器里拷贝到宿主机，进行修改

   - 拷贝出来：`docker cp nginx:/etc/nginx/conf.d/default.conf  ./`
   - 修改文件：

   ![image-20210123110019153](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210123110019153.png)

   - 把文件重新拷贝到容器里：`docker cp ./default.conf nginx:/etc/nginx/conf.d/`

3. 重启Tomcat容器，重启nginx容器

4. 浏览器访问测试：`http://宿主机ip:80/travel`

### 5. 数据卷【了解】

#### 介绍

​	我们在使用Tomcat、Nginx等容器时，经常需要修改配置文件、部署项目等，我们可以使用`docker cp`命令来实现文件的传输。但是频繁的在容器和宿主机之间传输文件是非常麻烦的。Docker提供数据卷(Volumes)技术，可以解决这一问题。

​	数据卷是一个可供一个或多个容器使用的特殊目录，它可以：

- 在容器与宿主机之间、容器与容器之间，进行文件共享和重用
- 对数据卷的修改会立即生效，并且不会影响镜像

![1580815260718](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/1580815260718.png)



#### 挂载数据卷

以tomcat为例，来演示如何挂载数据卷。 

##### 语法说明

- 语法：`docker run -it --name=容器名称 -p 宿主机端口:容器端口 -v 宿主机目录:容器目录 镜像名称:镜像标签`
- 作用：在创建容器时挂载数据卷

##### 创建容器并挂载目录

1. 停止并删除mytomcat容器（我们已有一个正在运行的容器，没有挂载数据卷，所以删除掉它）

![image-20210106205537081](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106205537081.png)

1. 创建mytomcat容器，挂载目录:

`docker run -id --name=mytomcat -p 8080:8080 -v /root/volumes/tomcat/:/usr/local/tomcat/webapps tomcat:8.5`

![image-20210106210322080](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106210322080.png)

#### 效果测试

![image-20210106210833462](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106210833462.png)

## 四、镜像制作

### 1. 镜像原理

#### 操作系统的组成

![1580816673060](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/1580816673060.png)

#### 镜像原理

![1580816718711](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/1580816718711.png)

#### 制作镜像的方式

- 使用一个已有容器，生成镜像，保存成文件：备份与恢复
- 基于一个基础镜像，构造一个新的镜像：Dockerfile

### 2. 备份与恢复

#### 2.1 把容器保存成镜像

- 命令：`docker commit 容器名称 自定义一个镜像名称`
- 示例：`docker commit mytomcat mytomcat-image`

![image-20210106211542485](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106211542485.png)

#### 2.2 镜像备份

- 命令：`docker save -o 文件名称 镜像名称`
- 参数：
  - `o`：output，表示要输出到文件
- 说明：
  - 文件名称，通常是`xxx.tar`打包文件
- 示例：`docker save -o myTomcatImage.tar mytomcat-image`

![image-20210106211916716](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106211916716.png)

#### 2.3 镜像恢复与迁移

- 命令：`docker load -i xxx.tar`
- 参数：
  - `i`：input，表示要从文件读取
- 示例：
  - 我们先将原有镜像删除掉：`docker rmi mytomcat-image:latest`
  - 从`xxx.tar`恢复镜像：`docker load -i mytomcatImage.tar`

![image-20210106212436797](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106212436797.png)

### 3. Dockerfile

#### 3.1 Dockerfile介绍

​	前边讲的commit方式，是从一个已有容器生成一个镜像。而Dockerfile是另外一种构建镜像的方式，它是以一种基础镜像为基础，编写一系列的docker指令，每条指令构建一层镜像，通过这些指令一层层构建出一个目标镜像出来。

- 对于开发人员：可以为开发团队提供一个完全一致的开发环境
- 对于测试人员：可以直接拿开发时所构建的镜像或者通过Dockerfile文件构建一个新的镜像开始工作了
- 对于运维人员：在部署时，可以实现应用的无缝移

Dockerfile的基本指令有十三个，分别是：

| 关键字      | 作用                     | 备注                                                         |
| ----------- | ------------------------ | ------------------------------------------------------------ |
| FROM        | 指定父镜像               | 指定dockerfile基于那个image构建                              |
| MAINTAINER  | 作者信息                 | 用来标明这个dockerfile谁写的                                 |
| LABEL       | 标签                     | 用来标明dockerfile的标签 可以使用Label代替Maintainer 最终都是在docker image基本信息中可以查看 |
| RUN         | 执行命令                 | 执行一段命令 默认是/bin/sh 格式: RUN command 或者 RUN ["command" , "param1","param2"] |
| CMD         | 容器启动命令             | 提供启动容器时候的默认命令 和ENTRYPOINT配合使用.格式 CMD command param1 param2 或者 CMD ["command" , "param1","param2"] |
| ENTRYPOINT  | 入口                     | 一般在制作一些执行就关闭的容器中会使用                       |
| COPY        | 复制文件                 | build的时候复制文件到image中                                 |
| ADD         | 添加文件                 | build的时候添加文件到image中 不仅仅局限于当前build上下文 可以来源于远程服务 |
| ENV         | 环境变量                 | 指定build时候的环境变量 可以在启动的容器的时候 通过-e覆盖 格式ENV name=value |
| ARG         | 构建参数                 | 构建参数 只在构建的时候使用的参数 如果有ENV 那么ENV的相同名字的值始终覆盖arg的参数 |
| VOLUME      | 定义外部可以挂载的数据卷 | 指定build的image那些目录可以启动的时候挂载到文件系统中 启动容器的时候使用 -v 绑定 格式 VOLUME ["目录"] |
| EXPOSE      | 暴露端口                 | 定义容器运行的时候监听的端口 启动容器的使用-p来绑定暴露端口 格式: EXPOSE 8080 或者 EXPOSE 8080/udp |
| WORKDIR     | 工作目录                 | 指定容器内部的工作目录 如果没有创建则自动创建 如果指定/ 使用的是绝对地址 如果不是/开头那么是在上一条workdir的路径的相对路径 |
| USER        | 指定执行用户             | 指定build或者启动的时候 用户 在RUN CMD ENTRYPONT执行的时候的用户 |
| HEALTHCHECK | 健康检查                 | 指定监测当前容器的健康监测的命令 基本上没用 因为很多时候 应用本身有健康监测机制 |
| ONBUILD     | 触发器                   | 当存在ONBUILD关键字的镜像作为基础镜像的时候 当执行FROM完成之后 会执行 ONBUILD的命令 但是不影响当前镜像 用处也不怎么大 |
| STOPSIGNAL  | 发送信号量到宿主机       | 该STOPSIGNAL指令设置将发送到容器的系统调用信号以退出。       |
| SHELL       | 指定执行脚本的shell      | 指定RUN CMD ENTRYPOINT 执行命令的时候 使用的shell            |

从前面的内容可以看出，要构建一个容器，需要做很多的工作，设置很多的配置，如果我们可以把每一层修改、安装、构建、操作的命令都写入一个脚本，用这个脚本来构建、定制镜像，那么之前提及的无法重复的问题、镜像构建透明性的问题、体积的问题就都会解决。 这个脚本就是 Dockerfile。

#### 3.2 Dockerfile示例

##### 需求

定义dockerfile，发布springboot项目

##### 实现

###### 1. 创建文件夹，并创建Dockerfile

在宿主机里创建一个文件夹`~/springboot_app`，文件夹里创建文件`Dockerfile`，内容如下：

```shell
#1.定义父镜像：
FROM java:8
#2.定义作者信息：
MAINTAINER  itheima <itheima@itcast.cn>
#3.将jar包添加到容器： 
ADD app.jar app.jar
#4.定义容器启动执行的命令： 当通过此镜像启动容器的时候，执行的命令
CMD java -jar app.jar
```

###### 2. 上传jar包到宿主机

把SpringBoot工程打包为`app.jar`，然后上传到CentOS的`~/springboot_app`里

###### 3. 通过Dockerfile构建镜像

```shell
# 构建镜像
docker build –f dockerfile文件路径 –t 镜像名称 ./

docker build -f ./Dockerfile -t bootapp ./
```

###### 4. 启动容器

```shell
#创建启动容器
docker run -id --name=自定义名称 -p 端口  镜像名称

docker run -id --name=mybootapp -p 80:80 bootapp
```

## 五、服务编排

### 1. 服务编排介绍

​    **服务编排：**按照一定的业务规则批量管理容器

​	Docker compose是一个用于定义和运行多个Docker容器的编排工具。可以一条命令启动多个容器。主要是解决了容器与容器之间如何管理编排的问题。

​	使用Docker compose 有三个步骤：

1. 利用Dockerfile定义运行环境（如果已有镜像，可省略这一步）
2. 使用`docker-compose.yml`定义组成应用的各服务 
3. 运行`docker-compose up -d`启动应用

### 2. 安装docker compose

#### 2.1 安装docker compose

```shell
# 下载docker compose
curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

# 设置权限
chmod +x /usr/local/bin/docker-compose

# 查看版本 
docker-compose -version
```

#### 2.2 卸载docker compose

```shell
# docker compose是二进制包方式安装的，删除二进制文件即可
rm /usr/local/bin/docker-compose
```

### 3. 服务编排示例

要求：通过docker-compose 批量创建三个容器（nginx，tomcat，redis）

1. 创建docker-compose目录

```shell
mkdir ~/docker-compose
cd ~/docker-compose
```

1. 编写docker-compose.yaml文件

```yaml
version: '3'
services:
  nginx:
    container_name: mynginx1
    image: nginx
    ports:
      - 180:80 # 宿主机端口:容器端口
  tomcat:
    container_name: mytomcat1
    image: tomcat:8.5
    ports:
      - 18080:8080 # 宿主机端口:容器端口
  redis:
    container_name: myredis1
    image: redis:5.0
    ports:
      - 16379:6379 # 宿主机端口:容器端口
```

1. 启动

```shell
# docker-compose up -d 以守护进程方式启动容器
docker-compose up -d
```

## 六、docker私服【拓展了解】

​	我们知道docker镜像可以托管到Docker Hub中，跟代码库托管到github是一个道理。但如果我们不想把docker镜像公开放到Docker Hub中，只想在部门或团队内部共享docker镜像，能不能项gitlab一样在搭建私有的仓库呢？答案是肯定的，docker也支持将镜像存到私有仓库。

### 1. 搭建私服

#### 1.1 拉取私服镜像

- 拉取私有仓库镜像：`docker pull registry`

![image-20210106224140518](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106224140518.png)

- 创建容器：`docker run -id --name=registry -p 5000:5000 registry`

![image-20210106224236899](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106224236899.png)

#### 1.2 设置私服容器

- 设置私有仓库为可信任，用vim编辑文件 `/etc/docker/daemon.json`

```shell
"insecure-registries":["私有仓库的ip:端口"]
```

![image-20210106224439160](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106224439160.png)

- 重启docker：`systemctl restart docker`
- 打开浏览器，通过网址：<http://192.168.247.140:5000/v2/_catalog>来验证是否启动成功

![image-20210106224854578](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106224854578.png)

### 2. 上传镜像到私服

#### 1. 给镜像打标记成为私有仓库镜像

- 语法：`docker tag 镜像名称 私有仓库ip地址:5000/镜像名称`
- 示例：`docker tag redis:5.0 192.168.247.140:5000/redis:5.0`

![image-20210106225337532](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106225337532.png)

#### 2. 上传到私有仓库

- 语法：`docker push 私有仓库ip地址:5000/镜像名称`
- 示例：`docker push 192.168.247.140:5000/redis:5.0`

![image-20210106225457311](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106225457311.png)

- 上传后，可以浏览器上看到

![image-20210106225533289](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day32-docker/%E7%AC%94%E8%AE%B0/img/image-20210106225533289.png)

### 3. 从私服拉取镜像

- 语法：`docker pull 私有仓库ip地址:5000/镜像名称`
- 示例：`docker pull 192.168.247.140:5000/redis:5.0`