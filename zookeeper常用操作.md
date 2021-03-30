# zookeeper

## 一、简介

### 1. 什么是分布式

#### 集中式系统

​	集中式系统，集中式系统中整个项目就是一个独立的应用，整个应用也就是整个项目，所有的东西都在一个应用里面。部署到一个服务器上。

​	单点故障：某一个点故障，会导致整个服务不可用

#### 分布式系统

​	随着公司的发展，应用的客户变多，功能也日益完善，加了很多的功能，整个项目在一个tomcat上跑，tomcat的负荷很高，难以提供大规模高并发的服务，这时候分布式就产生了。我们可以把大项目按功能划分为很多的模块，比如说单独一个系统处理订单，一个处理用户登录，一个处理后台等等，然后每一个模块都单独在一个tomcat中跑，合起来就是一个完整的大项目，这样每一个tomcat都非常轻松。

![1573197203689](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/1573197203689.png)

### 2. 什么是zookeeper

- zookeeper是一个分布式的，开放源码的分布式应用程序协调服务。

  ZooKeeper从字面意思理解，【Zoo - 动物园，Keeper - 管理员】动物园中有很多种动物，这里的动物就可以比作分布式环境下多种多样的服务，而ZooKeeper做的就是管理这些服务。

> zookeeper命名来源：hadoop生态圈多采用动物作为logo，zookeeper则用于对hadoop生态圈进行分布式协调和管理，所以起名称叫动物园管理员。
>
> - pig：用于数据挖掘，图标是一只猪
> - hive：数据仓库框架，图标是一个蜜蜂
> - hbase：列式NoSQL，图标是一个海豚
> - hadoop：大数据的分布式存储和计算框架，图标是一只大象

- ZooKeeper 适用于存储和协同相关的关键数据，不适合用于大数据量存储。

  **ZooKeeper 本质上是一个分布式的小文件存储系统。**提供基于类似于文件系统的目录树方式的数据存储，并且可以对树中的节点进行有效管理。从而用来维护和监控你存储的数据的状态变化。通过监控这些数据状态的变化，从而可以达到基于数据的集群管理。

- zookeeper的特点：

  - 全局数据一致性。zk集群中每个Server保存一份数据副本，无论Client连接哪个Server，数据都是一致的
  - 更新请求顺序执行。来自同一个Client的更新请求按照其发送顺序依次执行
  - 数据更新的原子性。一次更新要么成功，要么失败
  - 实时性，在一定时间范围内，Client能读取到最新数据

### 3. zookeeper的发展历程

​	ZooKeeper 最早起源于雅虎研究院的一个研究小组。当时研究人员发现，在雅虎内部很多大型系统基本都需要依赖一个系统来进行分布式协同，但是这些系统往往都存在分布式单点问题。所以，雅虎的开发人员就开发了一个通用的无单点问题的分布式协调框架，这就是ZooKeeper。ZooKeeper之后在开源界被大量使用，很多著名开源项目都在使用zookeeper，例如：

- Hadoop：使用ZooKeeper 做Namenode 的高可用。
- HBase：保证集群中只有一个master，保存hbase:meta表的位置，保存集群中的RegionServer列表。
- Kafka：集群成员管理，controller 节点选举。

### 小结

- 什么是集中式：all in one，一切在一个系统里，部署到一台服务器
- 什么是分布式：不同的系统提供不同的功能，这些系统共同组成一个完整的服务（有利于针对性的伸缩）
- 什么是集群 ：多个系统提供相同的功能，共同组成的集群 （是一种冗余备份，保证服务的高可用）
- 什么是zookeeper：是一个分布式的、开源的分布式应用协调系统，本质是一个文件存储系统
  - 用于存储数据，但是不能存储大量数据

## 二、安装配置

### 1. 准备工作

#### 1.1 环境要求

要求JDK版本1.8；需要配置好JDK环境变量

#### 1.2 下载zk

下载地址：http://zookeeper.apache.org

![image-20201226225401896](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226225401896.png)

进入下载页面，点击下载

![image-20201226225526882](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226225526882.png)

### 2. 安装配置

#### 2.1 解压

把下载好的《apache-zookeeper-3.5.8-bin.tar.gz》，解压到一个不含中文、空格、特殊字符的目录里。例如，我解压到了下图这个路径下

![image-20201226225840510](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226225840510.png)

#### 2.2 配置

1. 在解压目录里创建文件夹`data`

   注：可以不创建。如果在zoo.cfg里配置了数据目录，会自动生成文件夹的

![image-20201226225927832](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226225927832.png)

1. 把`conf`里的`zoo_sample.cfg`复制一份，得到`zoo.cfg`

![image-20201226230042683](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226230042683.png)

1. 使用文件编辑器打开并修改`zoo.cfg`文件

   ![image-20210120161628865](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20210120161628865.png)

### 3. 启动与测试

#### 3.1 启动zk

在zookeeper的`bin`目录下，有两个命令文件

1. `zkServer.cmd`：用于启动zk服务
2. `zkCli.cmd`：用于启动zk客户端，会自动连接到本机的zk服务

![image-20201226230311832](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226230311832.png)

#### 3.2 测试

##### 启动zkServer

zookeeper服务的访问端口，默认是2181

![image-20201226230809318](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226230809318.png)

##### 启动zkCli

![image-20201226230906497](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226230906497.png)

## 三、基本操作

### 1. zk的数据结构

​    ZooKeeper 的数据模型是层次模型。层次模型和key-value 模型是两种主流的数据模型。ZooKeeper 使用文件系统模型主要基于以下两点考虑：

1. 文件系统的树形结构便于表达数据之间的层次关系。
2. 文件系统的树形结构便于为不同的应用分配独立的命名空间（namespace）

​    ZooKeeper 的层次模型称作data tree。data tree的每个节点叫作znode，每个ZNode有一个唯一标识(path)，其默认可以存储1M数据。并且每个znode有一个版本(version)，版本从0 开始计数。

![1572831517484](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/1572831517484.png)	

### 2. zk的节点分类

|            |        普通的        |         循序的          |
| :--------: | :------------------: | :---------------------: |
| **持久的** | `PERSISTENT`（默认） | `PERSISTENT_SEQUENTIAL` |
| **临时的** |     `EPHEMERAL`      | `EPHEMERAL_SEQUENTIAL`  |

- 普通持久性znode：`PERSISTENT`，这种znode一旦创建不会丢失，无论是zookeeper宕机，还是client宕机。
- 普通临时性的znode：`EPHEMERAL`，如果zookeeper宕机了，或者client在指定的timeout时间内没有连接server，都会被认为丢失。
- 循序持久性的znode：`PERSISTENT_SEQUENTIAL`， znode 具备持久性的znode的特点，名称也具备顺序性。
- 循序临时性的znode：`EPHEMERAL_SEQUENTIAL`，znode具备临时性的znode特点，名称也具备顺序性。

### 3. 客户端命令【了解】

#### 基础命令

##### 列出所有命令：`help`

![image-20201226231801022](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226231801022.png)

##### 查询子节点：`ls path`

- 查询根节点下的子节点：`ls /`

![image-20201226232146166](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226232146166.png)

- 查询指定节点下的子节点，例如：`ls /zookeeper`

![image-20201226232210060](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226232210060.png)

#### 创建

##### 创建普通永久节点：`create path data`

![image-20201226232622665](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226232622665.png)

##### 创建循序永久节点：`create -s path data`

循序节点：创建时自动给节点名称添加序号

永久节点：永久存储的节点，关闭客户端时也不消失

![image-20201226232907340](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226232907340.png)

##### 创建普通临时节点：`create -e path data`

关闭客户端后临时节点会消失，重新打开客户端就看不到了

![image-20201226233157979](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226233157979.png)

##### 创建循序临时节点：`create -e -s path data`

循序节点：创建时会自动给节点名称添加序号

临时节点：关闭客户端时临时节点会消失

![image-20201226233321243](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226233321243.png)

##### 小结：

- `create [-e] [-s] path data`
- 参数：
  - -e：要创建临时节点
  - -s：要创建有序节点

#### 查询

##### 查询节点数据：`get path`

![image-20201226234121312](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226234121312.png)

##### 查看节点状态：`stat path`

![image-20201226234147058](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226234147058.png)

```shell
# ================节点的状态信息，也称为stat结构体================
# 创建该znode的事务的zxid(ZooKeeper Transaction ID)
# 事务ID是ZooKeeper为每次更新操作/事务操作分配一个全局唯一的id，表示zxid，值越小，表示越先执行
cZxid = 0x4454                        # Created Zxid 创建时的事务zxid
ctime = Thu Jan 01 08:00:00 CST 1970  # Created Time 节点的创建时间
mZxid = 0x4454 						  # Modified Zxid 最后一次更新的zxid
mtime = Thu Jan 01 08:00:00 CST 1970  # Modified Time 最后一次更新的时间
pZxid = 0x4454 						  # 最后添加/删除子节点的zxid
cversion = 5 						  # 子节点的变化号，表示子节点被修改的次数
dataVersion = 0 					  # 当前节点的数据变更次数，0表示从未被修改过
aclVersion = 0  					  # 访问控制列表的变化号 access control list
# 如果临时节点，表示当前节点的拥有者的sessionId
ephemeralOwner = 0x0				  # 如果不是临时节点，则值为0
dataLength = 13 					  # 数据长度
numChildren = 1 					  # 子节点数量
```

##### 小结

- 获取节点的数据：`get path`
- 查看节点的状态：`stat path`

#### 修改

##### 修改节点数据：`set path data`

![image-20201226234520876](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226234520876.png)

##### 小结

- 修改节点数据：`set path data`

#### 删除

##### 删除节点：`delete path`

![image-20201226234649345](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226234649345.png)

##### 递归删除：`deleteall path`

![image-20201226235127256](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201226235127256.png)

##### 小结

- 删除叶子节点：`delete path`。叶子节点，没有子节点的节点
- 删除非叶子节点：`deleteall path`。非叶子节点，有子节点的节点

#### 日志可视化【了解】

##### 查找zk日志文件

​	如果在conf的zoo.cfg中使用`dataLogDir`指定了日志目录，则日志保存到指定的目录下；否则日志也会保存到data目录下。我们去对应的目录下可以查找到日志文件

![image-20201227144724583](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201227144724583.png)

##### 查看zk日志文件

zk的日志是二进制的，使用文本编辑器不能查看。zk提供了查看日志的方法，步骤：

1. 把查看日志需要的包放到日志文件夹里

![image-20201227150530091](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201227150530091.png)

1. 使用cmd切换到日志目录后，查看日志：

```shell
java -classpath ".;*" org.apache.zookeeper.server.LogFormatter log.1
```

![image-20201227150615412](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20201227150615412.png)

##### 小结

1. 把zk的lib文件夹里的三个jar包：zookeeper-3.5.8.jar, zookeeper-jute-3.5.8.jar, slf4j-api-1.7.25.jar拷贝到日志文件同一目录里
2. 打开cmd，切换到日志文件所在的目录
3. 执行命令：`java -classpath ".;*" org.apache.zookeeper.server.LogFormatter log.1`

### 4. zk的JavaAPI【演示】

#### 常用的API介绍

- 原生的JavaAPI：不推荐

ZooKeeper是Java原生的API，位于org.apache.ZooKeeper包中。ZooKeeper-3.x.x. Jar （这里有多个版本）为官方提供的 java API

- ZkClient：不推荐

Github上一个开源的ZooKeeper客户端，由datameer的工程师Stefan Groschupf和Peter Voss一起开发。        zkclient-x.x.Jar也是在原生api 基础之上进行扩展的开源 JAVA 客户端。

- Curator：推荐使用

Apache Curator最初是Netfix研发的,后来捐献了 Apache基金会,目前是 Apache的顶级项目

Apache Curator是 Apache ZooKeeper的Java客户端库。  Curator项目的目标是简化ZooKeeper客户端的使用。 例如,在以前的代码展示中,我们都要自己处理ConnectionLossException。另外 Curator为常见的分布式协同服务提供了高质量的实现。

#### Curator使用示例

##### 1. 导入依赖

```xml
    <dependencies>
        <!--zookeeper的依赖-->
        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.4.7</version>
        </dependency>
        <!--zookeeper CuratorFramework-->
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>4.0.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-recipes</artifactId>
            <version>4.0.1</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
```

##### 2. 创建节点

创建一个测试类，在测试类里添加单元测试方法：

```java
@Test
public void testCreate() throws Exception {
    //1. 创建Curator客户端
    //1.1 创建重试策略：
    // baseSleepTimeMs-重试间隔的时间初始值，用于计算每次重试之间的时间间隔；
    // maxRetries-最多重试多少次；
    // maxSleepMs：重试间隔最大等待时间。如果根据baseSleepTimeMs计算出来的时间比这个值大，那么使用这个时间
    RetryPolicy policy = new ExponentialBackoffRetry(1000, 1, 1000);
    //1.2 创建Curator客户端对象
    // connectString-连接zk的ip地址和端口号，连接zk集群时使用逗号分隔多个地址
    // sessionTimeoutMs-会话超时时间，单位毫秒
    // connectionTimeoutMs-连接超时时间，单位毫秒
    // retryPolicy-重试策略
    CuratorFramework client = CuratorFrameworkFactory.newClient("localhost:2181", 3000, 1000, policy);
    //1.3 启动客户端
    client.start();


    //2.创建节点
    //2.0 创建空节点。curator默认获取当前客户端的ip作为节点值
    client.create().forPath("/app0");
    //2.1 创建普通永久节点
    client.create().forPath("/app1", "app1".getBytes());
    //2.2 创建循序永久节点
    client.create().withMode(CreateMode.PERSISTENT_SEQUENTIAL).forPath("/app2", "app2".getBytes());
    //2.3 创建普通临时节点：关闭客户端(会话结束)数据会消失
    client.create().withMode(CreateMode.EPHEMERAL).forPath("/app3", "app3".getBytes());
    //2.4 创建循序临时节点：关闭客户端(会话结束)数据会消失
    client.create().withMode(CreateMode.EPHEMERAL_SEQUENTIAL).forPath("/app4", "app4".getBytes());

    //2.5 创建多层级节点
    client.create().creatingParentsIfNeeded().forPath("/app5/app55", "app55".getBytes());

    //3.关闭客户端连接
    client.close();
}
```

##### 3. 修改节点

```java
@Test
public void testSet() throws Exception {
    //1. 创建客户端
    RetryPolicy policy = new ExponentialBackoffRetry(10, 3, 10);
    CuratorFramework client = CuratorFrameworkFactory.newClient("localhost:2181", 3000, 1000, policy);
    client.start();

    //2.修改节点
    client.setData().forPath("/app1", "a1".getBytes());

    //3. 关闭连接
    client.close();
}
```

##### 4. 获取数据

```java
@Test
public void testGet() throws Exception {
    //1. 创建客户端
    RetryPolicy policy = new ExponentialBackoffRetry(10, 3, 10);
    CuratorFramework client = CuratorFrameworkFactory.newClient("localhost:2181", 3000, 1000, policy);
    client.start();

    //2. 获取节点数据
    byte[] bytes = client.getData().forPath("/app1");
    System.out.println(new String(bytes));

    //3. 关闭客户端
    client.close();
}
```

##### 5. 删除节点

```java
@Test
public void testDelete() throws Exception {
    //1. 创建客户端
    RetryPolicy policy = new ExponentialBackoffRetry(10, 3, 10);
    CuratorFramework client = CuratorFrameworkFactory.newClient("localhost:2181", 3000, 1000, policy);
    client.start();

    //2. 删除节点
    //2.1 删除节点：有子节点的节点 删除时报错
    client.delete().forPath("/app1");
    //2.2 删除节点，递归删除其子节点：deletingChildrenIfNeeded
    client.delete().deletingChildrenIfNeeded().forPath("/app5");
    //2.3 保证必定删除节点：guaranteed
    //    只要会话没有结束，就不断重试删除操作，直至删除成功；在网络不佳的情况下会很有效果
    client.delete().guaranteed().deletingChildrenIfNeeded().forPath("/app0");

    //3. 关闭客户端
    client.close();
}
```

### 5. zk的watch机制【重点】

#### watch机制介绍

​    zk作为一款成熟的分布式协调框架，订阅-发布功能是很重要的一个。所谓订阅发布功能，其实说白了就是观察者模式。观察者会订阅一些感兴趣的主题，然后这些主题一旦变化了，就会自动通知到这些观察者。

​    zookeeper的订阅发布也就是watch机制，是一个轻量级的设计。因为它采用了一种**推拉结合**的模式。一旦服务端感知主题变了，那么只会发送一个事件类型和节点信息给关注的客户端，而不会包括具体的变更内容，所以事件本身是轻量级的，这就是所谓的“推”部分。然后，收到变更通知的客户端需要自己去拉变更的数据，这就“拉”部分。	watche机制分为添加数据和监听节点。

​    Curator在这方面做了优化，Curator引入了Cache的概念用来实现对ZooKeeper服务器端进行事件监听。Cache是Curator对事件监听的包装。而且Curator会自动的再次监听，我们就不需要自己手动的重复监听了。

- 当监听到节点数据有变化时，zk会推送给监听者。监听完就不再监听（zk本身特性）Curator会再次绑定事件继续监听
- 一旦监听到数据变化，zk只会推送通知数据有变化，而不会把数据告知监听者。Curator会自动拉取数据

Curator中的cache共有三种

- NodeCache：只监听节点本身，不监听子节点、不监听后代节点
- PathChildreCache：只监听子节点，不监听节点本身，不监听后代节点
- TreeCache：监听节点，及所有的子节点、后代节点

#### `NodeCache`

##### 说明

`NodeCache`是用于监听节点本身的数据变化的。当被监听的节点数据发生变化时就会执行对应的回调函数

注意：如果删除了被监听节点，则不触发事件

##### 示例

```java
/**
 * 测试NodeCache：只监听节点本身的数据变化
 */
@Test
public void testNodeCache() throws Exception {
    //1. 创建客户端
    RetryPolicy policy = new ExponentialBackoffRetry(10, 3, 10);
    CuratorFramework client = CuratorFrameworkFactory.newClient("localhost:2181", 3000, 1000, policy);
    client.start();
    System.out.println("----客户端已经成功连接zk服务----");

    //2. 创建节点缓存对象
    NodeCache cache = new NodeCache(client, "/app1");
    //   true：立即初始化节点数据。如果为true，则初始化数据不会触发事件
    //   false：不立即初始化节点数据。初始化数据时会触发事件
    cache.start(true);

    //3. 绑定监听函数
    cache.getListenable().addListener(new NodeCacheListener() {
        /**
             * 如果被监听的节点发生变化，这个方法会自动执行
             */
        @Override
        public void nodeChanged() throws Exception {
            //获取被监听节点的数据
            String data = new String(cache.getCurrentData().getData());
            System.out.println("数据Watcher：节点路径="+ cache.getPath() + ", data="+ data);
        }
    });

    //让程序等5分钟再结束，这时我们去客户端里修改/app1的数据，查看这里是否监听到了数据变化
    Thread.sleep(1000*60*5);
    
    client.close();
}
```

#### `PathChildrenCache`

##### 说明

`PathChildrenCache`用于监听节点的子节点变化，一旦子节点数据变化就会执行对应的回调函数

注意：只监听子节点，不监听节点本身、不监听孙子节点

##### 示例

```java
@Test
public void testPathChildrenNodeCache() throws Exception {
    //1. 创建客户端
    RetryPolicy policy = new ExponentialBackoffRetry(10, 3, 10);
    CuratorFramework client = CuratorFrameworkFactory.newClient("localhost:2181", 3000, 1000, policy);
    client.start();

    //2. 创建节点缓存对象
    PathChildrenCache cache = new PathChildrenCache(client, "/app1", true);
    //  如果设置为BUILD_INITIAL_CACHE：表示立即构造初始化缓存数据，初始化动作不会触发事件
    //  如果设置为POST_INITIALIZED_EVENT：表示初始化时会触发事件
    cache.start(PathChildrenCache.StartMode.BUILD_INITIAL_CACHE);

    //3. 绑定监听函数
    cache.getListenable().addListener(new PathChildrenCacheListener() {
        /**
             *
             * @param client
             * @param event
             * @throws Exception
             */
        @Override
        public void childEvent(CuratorFramework client, PathChildrenCacheEvent event) throws Exception {
            PathChildrenCacheEvent.Type eventType = event.getType();
            if (eventType == PathChildrenCacheEvent.Type.CHILD_ADDED) {
                System.out.println("新增了子节点，path=" + event.getData().getPath() + ", data=" + new String(event.getData().getData()));
            } else if (eventType == PathChildrenCacheEvent.Type.CHILD_UPDATED) {
                System.out.println("修改了子节点，path=" + event.getData().getPath() + ", data=" + new String(event.getData().getData()));
            } else if (eventType == PathChildrenCacheEvent.Type.CHILD_REMOVED) {
                System.out.println("删除了子节点，path=" + event.getData().getPath());
            }
        }
    });

    Thread.sleep(1000*60*5);
    
    client.close();
}
```

#### `TreeCache`

##### 说明

`TreeNode`用于监听节点本身、及所有后代节点的变化

##### 示例

```java
@Test
public void testTreeCache() throws Exception {
    //1. 创建客户端
    RetryPolicy policy = new ExponentialBackoffRetry(10, 3, 10);
    CuratorFramework client = CuratorFrameworkFactory.newClient("localhost:2181", 3000, 1000, policy);
    client.start();

    //2. 创建缓存对象
    TreeCache cache = new TreeCache(client, "/app1");
    cache.start();

    //3. 绑定事件函数
    cache.getListenable().addListener(new TreeCacheListener() {
        @Override
        public void childEvent(CuratorFramework client, TreeCacheEvent event) throws Exception {
            TreeCacheEvent.Type type = event.getType();
            if (type == TreeCacheEvent.Type.NODE_ADDED) {
                System.out.println("新增了节点，path=" + event.getData().getPath() + "， data=" + new String(event.getData().getData()));
            }else if (type == TreeCacheEvent.Type.NODE_UPDATED) {
                System.out.println("修改了节点，path=" + event.getData().getPath() + "， data=" + new String(event.getData().getData()));
            }else if (type == TreeCacheEvent.Type.NODE_REMOVED) {
                System.out.println("删除了节点，path=" + event.getData().getPath());
            }
        }
    });

    Thread.sleep(1000*60*5);

    client.close();
}
```

### 小结

- zookeeper的数据结构：层次模型，是一个树形结构
  - 这棵树叫datatree，树上的每个节点叫ZNode
  - 每个节点ZNode：
    - 有自己的唯一标识，叫path，比如： /dubbo/userService
    - 可以存储数据，默认可以存储1M数据
- 节点的分类
  - 普通持久的：没有序号，永久存在的
  - 有序持久的：有序号，永久存储的
  - 普通临时的：没有序号，临时存储的（当客户端关闭，和服务端会话结束时，数据会消失）
  - 有序临时的：有序号，临时存储的
- Curator的watch机制
  - 比zk原生的监听优势：
    - 可以持续监听。原生zk的监听，监听到事件，解除监听了。而Curator可以在解除监听后再次绑定监听
    - 可以拉取数据。原生zk的监听，只能监听到事件变化；如果需要用数据，需要自己再次查询得到。而Curator可以帮我们拉取最新的数据
  - Curator提供了三个Cache用于监听：
    - NodeCache：只监听节点本身，不监听子节点、后代节点
    - PathChildrenCache：监听子节点，不监听节点本身、后代节点
    - TreeCache：监听节点本身、及所有的子节点、所有的后代节点

## 四、使用场景【重点】

### 注册中心

​    分布式应用中，通常需要有一套完整的命名规则，既能够产生唯一的名称又便于人识别和记住，通常情况下用树形的名称结构是一个理想的选择，树形的名称结构是一个有层次的目录结构。通过调用Zookeeper提供的创建节点的API，能够很容易创建一个全局唯一的path，这个path就可以作为一个名称。
​    阿里巴巴集团开源的分布式服务框架Dubbo中使用ZooKeeper来作为其命名服务，维护全局的服务地址列表。

![image-20210120221741450](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20210120221741450.png)



### 配置中心

​	当我们搭建了应用集群后，要求集群中每个服务的数据库地址都是一致的。一旦数据库地址变化，就不得不修改集群中所有服务中的配置。

​	我们可以把数据地址配置到zookeeper中，然后利用zookeeper的监听机制，让集群中所有服务都监听配置的数据。一旦发现配置信息变化，就自动获取最新的配置数据。

​	目前互联网企业中，通常会使用spring cloud config， Apollo（携程）, Nacos（阿里）作为配置中心

![image-20210120223844553](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20210120223844553.png)

### ID生成器

通过在ZooKeepr里创建循序节点，很容易创建一个全局唯一的名字，即生成全局唯一的ID。

但是要发请求到zookeeper，可能会有网络问题导致出现问题，用户体验很差。

所以现代开发，可以使用雪花算法代替



### 分布式协调

分布式协调服务使用的是Zookeeper的watch机制

- 不同的客户端如机器节点，发生了变化，那么所有订阅的客户端都能够接收到相应的Watcher通知，并做出相应的处理。

  例如：服务提供者集群中，每个提供者都会把自己的数据存储到zookeeper中，存储到一个临时节点上。消费者可以随时获取每个提供者的状态。一旦某个提供者有所变化、宕机或者关闭，zk会向消费者发送信息，消费者会立刻收到消息。

- 分布式协调注册中心：zookeeper，eureka，consul，nacos

### 分布式锁

分布式锁使用的是Zookeeper的临时有序节点

需求： 
​    在分布式系统中，很容出现多台主机操作同一资源的情况， 比如两台主机同时往一个文件中追加写入文本， 
如果不去做任何的控制，很有可能出现一个写入操作被另一个写入操作覆盖掉的状况

方案：
​    此时我们可以来一把锁，哪个主机获取到了这把锁，就执行写入，另一台主机等待;直到写入操作执行完毕，另一台主机再去获得锁，然后写入
这把锁就称为分布式锁， 也就是说:分布式锁是控制分布式系统之间同步访问共享资源的一种方式

实现:
​    使用Zookeeper的临时有序节点可以轻松实现这一需求

1. 所有需要执行操作的主机都去Zookeeper上创建一个临时有序节点
2. 然后获取到Zookeeper上创建出来的这些节点进行一个从小到大的排序
3. 判断自己创建的节点是不是最小的，如果是，自己就获取到了锁;如果不是，则对最小的节点注册一个监听
4. 如果自己获取到了锁，就去执行相应的操作，当执行完毕之后，连接断开，节点消失，锁就被释放了
5. 如果自己没有获取到锁，就等待，一直监听节点消失，锁释放后，再重新执行抢夺锁的操作

替换方案： 
1、通过redis setNx实现。setNx：向redis里设置一个key，如果key不存在，则设置成功；否则不能设置
2、基于redis的分布式锁框架：redisson

## 五、集群搭建【拔高】

### 集群介绍

#### 集群介绍

Zookeeper在一个系统中一般会充当一个很重要的角色，所以一定要保证其高可用性，这就需要部署Zookeeper的集群。

Zookeeper 有三种运行模式：

- 单机模式：使用一台主机部署一个Zookeeper来对外提供服务，有单点故障问题，仅适合于开发、测试环境
- 集群模式：使用多台服务器，每台上部署一个Zookeeper一起对外提供服务，适合于生产环境
- 伪集群模式：在服务器不够多的情况下，也可以考虑在一台服务器上部署多个Zookeeper来对外提供服务

#### 集群中的角色

##### leader领导者

- leader是集群的核心，负责对集群中各个服务器进行调度，并且是事务性请求(写操作)的唯一处理者，保证集群事务处理的顺序性；
- 所有的写操作，全部要交给leader进行处理，而leader需要决定编号、执行操作，这个过程称为一个事务
- leader也可以处理读操作

##### follower跟随者

- 仅用于处理非事务性操作；如果有事务性操作，则转交给leader来处理；
- 会参与投票选举leader

##### observer观察者

- 仅用于处理非事务性操作；如果有事务性操作，则转交给leader来处理
- 观察者不参与投票选举
- 通常用于在不影响集群事务处理能力的前提下提升集群的非事务处理能力

![1573475887256](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/1573475887256.png)

### 搭建zk集群

#### 1. 准备三台服务器

##### 1.1 在VMWare里创建3个虚拟机，都安装CentOS

假设三个虚拟机的名称分别为`cluster1`, `cluster2`, `cluster3`（名称随便，自己能区分得开即可）

![image-20210101214815256](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20210101214815256.png)

##### 1.2 安装JDK并配置环境变量

在三个虚拟里都要安装jdk并配置环境变量，操作步骤如下：

1. 把jdk安装包上传到虚拟机的`/root`目录下
2. 解压jdk安装包到`/usr/local`
3. 配置环境变量 `vim /etc/profile`，在文件最后添加

```shell
#set java environment
JAVA_HOME=/usr/local/jdk1.8.0_181
CLASSPATH=.:$JAVA_HOME/lib.tools.jar
PATH=$JAVA_HOME/bin:$PATH
export JAVA_HOME CLASSPATH PATH
```

1. 保存并退出vim，然后执行命令`source /etc/profile`重载环境变量

##### 1.3 查看每个CentOS的ip地址

- 稍后在搭建zk集群时，需要使用到每个服务器的ip地址
- 查看ip的命令是：`ifconfig`（或者 `ip addr`， 两个命令用哪个都行）
- 例如，我的三个虚拟ip分别是：
  - `cluster1`：`192.168.247.137`
  - `cluster2`：`192.168.247.138`
  - `cluster3`：`192.168.247.139`

![image-20210101220805950](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20210101220805950.png)

![image-20210101221023907](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20210101221023907.png)

![image-20210101221135182](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20210101221135182.png)

#### 2. 安装zk

在三个CentOS里都要安装zookeeper，安装步骤如下：

1. 把zookeeper安装包上传到CentOS的`/root`目录下

2. 把zookeeper安装包都解压到`/usr/local`目录下，并重命名成为`zookeeper`

   ```shell
   tar -xvf /root/apache-zookeeper-3.5.8-bin.tar.gz -C /usr/local
   cd /usr/local
   mv zookeeper-zookeeper-3.5.8 zookeeper
   ```

3. 在`/usr/local/zookeeper`里创建`data`目录

   ```shell
   mkdir /usr/local/zookeeper/data
   ```

4. 切换到zookeeper的`conf`目录下，把`zoo_sample.cfg`重命名成为`zoo.cfg`

   ```shell
   cd /usr/local/zookeeper/conf
   mv zoo_sample.cfg zoo.cfg
   ```

5. 编辑`zoo.cfg`，设置数据目录为`/usr/local/zookeeper/data`

   编辑文件：`vim zoo.cfg`

   修改内容如下，修改后保存并退出vim

![image-20210101215845651](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20210101215845651.png)

#### 3. 搭建zk集群

##### 3.1 设置zk的id

搭建zk集群时，要给每个zk服务指定一个id，步骤：

1. 在`/usr/local/zookeeper/data`里创建文件`myid`
2. 使用vim编辑`myid`文件，文件内容是一个数字，这个数字就是zk服务的id

![image-20210101222400315](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day31-zookeeper/%E8%AE%B2%E4%B9%89/img/image-20210101222400315.png)

例如：我的三个zk服务id分别设置为

- `cluster1`里的zk，设置id为1
- `cluster2`里的zk，设置id为2
- `cluster3`里的zk，设置id为3

##### 3.2 配置集群

三个服务器里的zk都需要做同样的配置，步骤如下：

1. 使用vim打开`/usr/local/zookeeper/conf/zoo.cfg`文件
2. 在文件最后添加以下内容：

```shell
server.1=192.168.247.137:2888:3888
server.2=192.168.247.138:2888:3888
server.3=192.168.247.139:2888:3888
```

配置说明：

- 配置集群的格式：`server.A=B:C:D`
- 说明：
  - A：是服务器的id，是myid文件里的那个数字
  - B：服务器的ip
  - C：通信端口，集群里各个服务器之间进行通信的端口，可以用任意端口，但是不能有端口冲突
  - D：选举端口，集群里各个服务器之间选举leader的端口，可以用任意端口，但是不能有端口冲突

##### 3.3 启动服务

按照服务器1->2->3的顺序，分别启动所有的zk服务：

1. 关闭防火墙：`systemctl stop firewalld`
2. 切换到zk的bin目录：`cd /usr/local/zookeeper/bin`
3. 启动：`./zkServer.sh start`

启动后可以分别查看每个zk服务的状态和角色：

- `./zkServer.sh status`

结果发现：cluster2的角色是leader，其它两个角色是follower

### leader选举

leader的选举有两种：

- 第一种：服务器初始化启动的Leader选举。
- 第二种：服务器运行时期的Leader选举（服务器运行期间无法和Leader保持连接）。

#### 启动时的选举

在集群初始化阶段，当第一台服务器启动时，是不能单独进行选举的；当第二台服务器启动后，两台服务器相互通信，才可以进行选举，进入选举过程。

##### 1. 每个服务器投票给自己

初始化启动时，第个服务器会投票给自己。投票数据包含服务器自己的myid和zxid，使用(myid, zxid)来表示。

结果：

- 服务器1投票：投自己，按照`(myid,zxid)`格式表示为`(1, 0)`
- 服务器2投票：投自己，按照`(myid,zxid)`格式表示为`(2, 0)`

##### 2. 每个服务器接收并校验投票信息

每个服务器会接收其它服务器的投票信息，校验投票是否有效。

##### 3. 投票PK

每个服务器都会拿自己投的票和其它服务器的投票信息进行PK，PK规则是：

- 优先检查`zxid`值。zxid大的获胜
- 如果`zxid`相同，则`myid`大的获胜

结果：

- 服务器1：自己投的票是`(1,0)`，收到服务器2投的票`(2,0)`，自己输了，重新投票为`(2,0)`
- 服务器2：自己投的票是`(2,0)`，收到服务器1投的票`(1,0)`，自己赢了，投票仍然为`(2,)`

##### 4. 统计投票

每次投票后，服务器都会统计投票信息，判断是否已经有过半机器收到了相同的投票。对于服务器1和服务器2而言，已经统计出两台服务器投票是`(2,0)`，集群共3台服务器，已经过半，此时便认为已经选出服务器2为leader。

##### 5. 改变服务器状态

一旦确定了leader，每个服务器就会更改自己的服务器状态

- 服务器1：Follower
- 服务器2：Leader

而等服务器3启动时，只能作为跟随者Follower了

#### 运行时的选举

在zk运行过程中，如果有非Leader服务器加入或者挂掉，不会对Leader造成影响。但是一旦Leader出现问题，整个集群将会暂停对外服务，重新选举一个Leader之后再提供服务。而选举的过程，和启动时选举过程基本相同。