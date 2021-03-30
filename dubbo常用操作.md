# dubbo

## 一、RPC

- [ ] 了解架构的演进过程
- [ ] 了解RPC的概念和执行过程

### 1. 应用架构的演进

​	随着互联网的发展，网站应用的规模也在不断的扩大，进而导致系统架构也在不断的进行变化。从互联网早起到现在，系统架构大体经历了下面几个过程：

​	单体应用架构--->垂直应用架构--->分布式架构--->SOA架构--->微服务架构（项目二会学习）

![image-20201216082601572](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20201216082601572.png)



#### 单一应用架构（all  in one）

当网站流量很小时，只需一个应用，将所有功能一起部署在一台服务器。

缺点：

- 可靠性差：一旦服务器宕机就全完，一个功能问题会影响整个系统
- 可伸缩性差：只能针对整个系统扩展集群，不能针对某个功能模块扩展
- 技术栈受限：整个开发团队只能使统一的编程语言、技术
- 性能低：一台服务器，性能低

![image-20210119163857558](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119163857558.png)

#### 垂直应用架构

将一个应用里多个模块拆分开，形成多个独立项目，分别部署到不同的服务器上。

缺点：

- 重复代码太多

![image-20210119164313676](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119164313676.png)

#### 分布式服务架构

分布式架构就是指将服务层（service）单独启动，并部署在tomcat上对外提供服务，在controller中可以通过远程调用访问服务层中的方法。

问题：

- 服务提供方一旦变更，所有的消费方都要跟着变

![image-20210119165122276](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119165122276.png)

#### SOA架构

在分布式架构的基础上，增加一个调度中心对系统进行实时管理。

SOA架构：Service-Oriented Architecture，面向服务的架构，它将服务程序的不同功能单元（称为服务）进行拆分，并通过这些服务之间定义良好的接口和契约联系起来。

![image-20210119165711485](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119165711485.png)



#### 小结

- 单体应用架构（all in one）
- --->垂直应用架构(按模块拆分成不同的系统，有大量的重复代码)
- --->分布式架构（消费者，远程调用服务提供者。 服务提供者可以共用。提供者地址改变，消费者要做大量的代码修改）
- --->SOA架构（分布式架构 + 注册中心）

### 2. RPC介绍

#### 什么是RPC

Remote Procedure Call 远程过程调用，是分布式架构的核心

- 是一种进程间的通信方式
- 它允许应用程序调用网络上的另一个应用程序中的方法
- 对于服务的消费者而言，无需了解远程调用的底层细节，是透明的

按响应方式分如下两种：

- 同步调用：客户端调用服务方方法，等待直到服务方返回结果或者超时，再继续自己的操作。
- 异步调用：客户端把消息发送给中间件，不再等待服务端返回，直接继续自己的操作。

需要注意的是RPC并不是一个具体的技术，而是指整个网络远程调用过程。

RPC是一个泛化的概念，严格来说一切远程过程调用手段都属于RPC范畴。各种开发语言都有自己的RPC框架。Java中的RPC框架比较多，广泛使用的有RMI、Hessian、Dubbo等。

#### RPC的组件

简单来说一个RPC架构里包含如下4个组件:

- 客户端(Client)：服务调用者
- 客户端存根(Client Stub)：存放服务端地址信息，将客户端的请求参数打包成网络消息，再通过网络发送给服务方
- 服务端存根(Server Stub)：接受客户端发送过来的消息并解包，再调用本地服务
- 服务端(Server)：服务提供者。

![image-20201024171010906](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20201024171010906.png) 



#### RPC调用

![1562656641829](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/1562656641829.png)

1. 服务调用方（client）调用以本地调用方式调用服务；
2. client stub接收到调用后负责将方法、参数等组装成能够进行网络传输的消息体，在Java里就是序列化的过程
3. client stub找到服务地址，并将消息通过网络发送到服务端；
4. server stub收到消息后进行解码,在Java里就是反序列化的过程；
5. server stub根据解码结果调用本地的服务；
6. 本地服务执行处理逻辑；
7. 本地服务将结果返回给server stub；
8. server stub将返回结果打包成消息，Java里的序列化；
9. server stub将打包后的消息通过网络并发送至消费方；
10. client stub接收到消息，并进行解码, Java里的反序列化；
11. 服务调用方（client）得到最终结果。

#### 小结

- RPC：远程过程调用，是一类技术的统称
- 调用的过程：
  - Client（Controller）通过ClientStub调用
    - ClientStub：
      - 把参数序列化，传递到ServerStub
    - ServerStub：
      - 接收数据，反序列化
      - 调用XXXServiceImpl，得到结果
      - 把结果序列化，返回给ClientStub
    - ClientStub：
      - 接收结果，反序列化
      - 把最终结果返回给Client（Controller）
  - Client（Controller）：得到结果

## 二、dubbo概述

- [ ] 了解dubbo的概念
- [ ] 掌握dubbo的架构
- [ ] 能安装并启动zookeeper
- [ ] 能使用dubbo-admin查看生产者和消费者

### 什么是dubbo

#### 简介

Apache Dubbo是一款高性能的Java RPC框架。其前身是阿里巴巴公司开源的一个高性能、轻量级的开源Java RPC框架，可以和Spring框架无缝集成。Dubbo官网地址：http://dubbo.apache.org

Dubbo提供了三大核心能力：

- 面向接口的远程方法调用
- 智能容错和负载均衡
- 服务自动注册和发现

#### 发展历程

- 2012年，由淘宝团队开源的一款产品，收到了市场的欢迎，很多互联网都在使用了，淘宝团队发现吃力不讨好，就不再升级维护了....所有就由当当牵头搞了dubbox，继续进行维护...
- 2015年，spring家族就搞出了SpringCloud微服务架构（netflix公司提供的一套产品）
- 2018年，阿里重拾dubbo框架，并捐献给了apache基金会，成为了顶级项目
- 2019年， 阿里推出一套整合SpringCloud微服务架构的产品（SpringCloud alibaba）

### dobbo架构

#### dubbo架构图

Dubbo架构图（Dubbo官方提供）如下：

![1562888790694](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/1562888790694.png)

#### 节点角色说明

| 节点      | 角色名称                               |
| --------- | -------------------------------------- |
| Provider  | 暴露服务的服务提供方                   |
| Consumer  | 调用远程服务的服务消费方               |
| Registry  | 服务注册与发现的注册中心               |
| Monitor   | 统计服务的调用次数和调用时间的监控中心 |
| Container | 服务运行容器                           |

#### 调用关系说明

1. 服务容器负责启动，加载，运行服务提供者。
2. 服务提供者在启动时，向注册中心注册自己提供的服务。
3. 服务消费者在启动时，向注册中心订阅自己所需的服务。
4. 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
5. 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
6. 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。

### 注册中心zookeeper

#### 说明

​	Dubbo官方推荐使用 zookeeper 注册中心。注册中心负责服务地址的注册与查找，相当于目录服务，服务提供者和消费者只在启动时与注册中心交互，注册中心不转发请求，压力较小。

​	Zookeeper 是 Apacahe Hadoop 的子项目，是一个树型的目录服务，支持变更推送，适合作为Dubbox 服务的注册中心，工业强度较高，可用于生产环境。

#### 安装与启动

##### 1. 解压

解压到一个不含中文、空格、特殊字符的目录里。

![image-20210119182110229](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119182110229.png)

##### 2. 配置

1. 把zookeeper解压文件夹里的conf/zoo_sample.cfg复制一份，命名为zoo.cfg

![image-20210119182353138](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119182353138.png)

1. 修改zoo.cfg，配置数据存储的目录

![image-20210119182709585](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119182709585.png)

##### 3. 启动

进入zookeeper的bin目录，双击`zkServer.cmd`即可启动

![image-20210119182948852](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119182948852.png)

### dubbo-admin管理控制台

​	在开发时，我们需要知道在注册中心都注册了哪些服务。但是每次都通过zkCli在命令行查看是非常不方便的。我们可以通过部署一个管理控制台来实现可视化管理。

#### 部署启动

1. 把今天的资料里的`dubbo-admin`，拷贝到不含中文、空格、特殊字符的目录里

   这个jar包是使用SpringBoot编写的一个应用，端口是7001，登录帐号root，登录密码root

![image-20201229161338952](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20201229161338952.png)

1. 双击`dubboAdmin.cmd`即可启动

> 注意：默认只能管理本机的zookeeper，如果要管理其它主机上的zookeeper：
>
> 1. 使用解压软件打开《dubbo-admin-0.0.1-SNAPSHOT.jar》
> 2. 找到`dubbo-admin-0.0.1-SNAPSHOT.jar\BOOT-INF\classes`文件夹
> 3. 把`application.properties`文件拖出来到桌面上进行修改
>
> ![image-20201229162307313](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20201229162307313.png)
>
> 1. 把修改后的`application.properties`重新拖到压缩包的`BOOT-INF/classes`文件夹，把原本的`applicatio.properties`覆盖掉
> 2. 关闭管理控制台然后重启即可

#### 登录管理

1. 打开浏览器输入地址： `http://localhost:7001`

![image-20201229161716893](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20201229161716893.png)

1. 查看服务提供者和消费者

![image-20201229161808827](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20201229161808827.png)



### 使用dubbo的准备工作小结

1. 启动注册中心：zookeeper，端口2181
2. 启动管理控制台：dubbo-admin，端口7001，可以通过浏览器，可视化查看有多少服务提供者和消费者

## 三、快速入门

- [ ] 能够使用Spring搭建dubbo环境

### 需求介绍

#### 需求

需求：根据id查询用户对象

描述：客户端发`GET`请求`/user/1`，从数据库里查询id为1的用户返回客户端

#### 说明

​	Dubbo作为一个RPC框架，其最核心的功能就是要实现跨网络的远程调用，服务提供者、服务消费者会使用共同的接口，故本小节先创建一个父工程，父工程下有一个是公共接口模块、一个是服务提供者模块，一个是服务消费者模块。通过Dubbo来实现服务消费方远程调用服务提供方的方法。

​	为了便于了解dubbo的一些细节，我们这里使用Spring+dubbo来搭建环境。在后边章节里，我们会学习SpringBoot如何整合dubbo，来简化开发。

初始化数据库：

```mysql
CREATE DATABASE dubbo_db;
USE dubbo_db;

CREATE TABLE `t_user` (
  `id` INT(11) NOT NULL AUTO_INCREMENT,
  `username` VARCHAR(20) DEFAULT NULL,
  `age` INT(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

INSERT INTO t_user (username, age) VALUES ('张三', 30);
INSERT INTO t_user (username, age) VALUES ('李四', 40);
INSERT INTO t_user (username, age) VALUES ('王五', 50);
```

#### 步骤

开发步骤：

1. 创建父工程，不选择骨架：dubbo-demo1
2. 创建公共的实体类模块：dubbo-domain
3. 创建公共接口模块：dubbo-interface
4. 创建服务提供者模块：dubbo-provider
5. 创建服务消费者模块：dubbo-consumer

### 功能实现

#### 1. 创建父工程`dubbo-demo1`

- 创建时不选择骨架
- 创建好以后，删除多余的src目录
- 然后在`pom.xml`中设置打包方式为pom，并锁定依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.itheima</groupId>
    <artifactId>dubbo-demo1</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <spring.version>5.0.5.RELEASE</spring.version>
        <dubbo.version>2.6.2</dubbo.version>
        <zookeeper.version>3.4.7</zookeeper.version>
        <curator.verion>4.0.1</curator.verion>
        <mybatis.version>3.4.5</mybatis.version>
        <mysql.version>5.1.47</mysql.version>
        <mybatis-spring.version>1.3.2</mybatis-spring.version>
        <druid.version>1.1.9</druid.version>
        <log.version>1.2.17</log.version>
        <slf4j.version>1.7.25</slf4j.version>
    </properties>
    <dependencyManagement>
        <dependencies>
            <!--SpringMVC-->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-webmvc</artifactId>
                <version>${spring.version}</version>
            </dependency>

            <!--dubbo+zk-->
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>dubbo</artifactId>
                <version>${dubbo.version}</version>
            </dependency>
            <dependency>
                <groupId>org.apache.zookeeper</groupId>
                <artifactId>zookeeper</artifactId>
                <version>${zookeeper.version}</version>
            </dependency>
            <dependency>
                <groupId>org.apache.curator</groupId>
                <artifactId>curator-framework</artifactId>
                <version>${curator.verion}</version>
            </dependency>
            <dependency>
                <groupId>org.apache.curator</groupId>
                <artifactId>curator-recipes</artifactId>
                <version>${curator.verion}</version>
            </dependency>

            <!--持久层-->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-jdbc</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>${mybatis.version}</version>
            </dependency>
            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis-spring</artifactId>
                <version>${mybatis-spring.version}</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid</artifactId>
                <version>${druid.version}</version>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.version}</version>
            </dependency>

            <!--日志-->
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>${log.version}</version>
            </dependency>
            <dependency>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-api</artifactId>
                <version>${slf4j.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
```

#### 2. 创建实体类模块`dubbo-domain`

![image-20210119190615292](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119190615292.png)

- 创建Module时，不选择骨架
- 创建好以后在pom.xml中暂不需要添加依赖
- 创建JavaBean：`User`类

```java
package com.itheima.pojo;

import java.io.Serializable;

/**
 * @author liuyp
 * @date 2020/12/28
 */
public class User implements Serializable {
    private Integer id;
    private String username;
    private Integer age;

    //get/set...
}
```

#### 3. 创建接口子模块`dubbo-interface`

![image-20210119190752940](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119190752940.png)

- 创建Module时，不选择骨架
- 创建好，修改pom.xml，添加依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>dubbo-demo1</artifactId>
        <groupId>com.itheima</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>dubbo-interface</artifactId>

    <dependencies>
        <!-- 依赖于dubbo-commons -->
        <dependency>
            <groupId>com.itheima</groupId>
            <artifactId>dubbo-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
    </dependencies>
</project>
```

- 创建Service层接口

```java
package com.itheima.service;

import com.itheima.domain.User;

/**
 * @author liuyp
 * @date 2021/01/19
 */
public interface UserService {
    User findById(Integer id);
}
```

#### 4. 创建服务提供者模块`dubbo-provider`

​	这个模块是服务提供者，需要在容器启动时，把服务注册到zk，所以要引入spring-webmvc,zookeeper,及客户端的依赖。

​	步骤如下：

1. 准备工作：创建Module，不选择骨架，然后修改pom.xml，添加依赖
2. 创建映射器UserDao接口，并创建映射配置文件，配置好findById功能
3. 创建UserServiceImpl类，实现UserService接口，使用dubbo的注解`@Service`注册到zk里
4. 创建配置文件
   - `spring-dao.xml`，主要配置dao层Spring和Mybatis的整合
   - `spring-service.xml`，主要配置组件扫描和事务管理
   - `spring-dubbo.xml`，配置dubbo，把服务暴露出去
   - `log4j.properties`，日志配置文件
5. 创建启动类，启动服务提供者

##### 1. 准备工作

![image-20210119191010959](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119191010959.png)

###### 创建Module，不选择骨架

###### 修改pom.xml添加依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>day30-dubbo-demo1</artifactId>
        <groupId>com.itheima</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>dubbo-provider</artifactId>

    <dependencies>
        <!--依赖于dubbo-interface-->
        <dependency>
            <groupId>com.itheima</groupId>
            <artifactId>dubbo-interface</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
        </dependency>

        <!--持久层的依赖-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
        </dependency>

        <!--dubbo和zk-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>dubbo</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-framework</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-recipes</artifactId>
        </dependency>
    </dependencies>
</project>
```

##### 2. 创建映射器

- 在`com.itheima.mapper`包里创建接口`UserMapper`

```java
package com.itheima.mapper;

import com.itheima.domain.User;
import org.apache.ibatis.annotations.Select;

/**
 * @author liuyp
 * @date 2021/01/19
 */
public interface UserMapper {
    @Select("select * from t_user where id = #{id}")
    User findById(Integer id);
}
```

##### 3. 创建UserServiceImpl并添加注解

- 创建`com.itheima.service.impl`包，在包里创建类`UserServiceImpl`

- 在类上加注解`@Service`

  <font color="red">注意：是**dubbo**提供的注解`com.alibaba.dubbo.config.annotation.Service`，千万不要导错了</font>

```java
package com.itheima.service.impl;

import com.alibaba.dubbo.config.annotation.Service;
import com.itheima.domain.User;
import com.itheima.mapper.UserMapper;
import com.itheima.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;

/**
 * 注意：类上加的注解是dubbo的@Service，而不是Spring的@Service
 * @author liuyp
 * @date 2021/01/19
 */
@Service
public class UserServiceImpl implements UserService {

    @Autowired
    private UserMapper userMapper;

    @Override
    public User findById(Integer id) {
        return userMapper.findById(id);
    }
}
```

##### 4. 创建配置文件

###### 创建`spring-dao.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--配置数据源-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql:///dubbo_db?useSSL=false"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </bean>

    <!--配置映射器扫描-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.itheima.mapper"/>
    </bean>

    <!--配置SqlSessionFactory-->
    <bean class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="typeAliasesPackage" value="com.itheima.domain"/>
    </bean>
</beans>
```

###### 创建`spring-service.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!--配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--开启事务的注解驱动-->
    <tx:annotation-driven transaction-manager="transactionManager"/>
</beans>
```

###### 创建`spring-provider.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <!--注册到zk时，要提供一个应用名称-->
    <dubbo:application name="dubbo-provider"/>

    <!--注册到zk时，需要知道zk的访问地址-->
    <dubbo:registry address="zookeeper://localhost:2181"/>

    <!--扫描包。扫描到@Service注解，就会把被注解的类暴露出去，供消费者调用-->
    <dubbo:annotation package="com.itheima.service"/>
</beans>
```

###### 创建`log4j.properties`

```properties
# Set root category priority to INFO and its only appender to CONSOLE.
#log4j.rootCategory=INFO, CONSOLE            debug   info   warn error fatal
log4j.rootCategory=debug, CONSOLE

# Set the enterprise logger category to FATAL and its only appender to CONSOLE.
log4j.logger.org.apache.axis.enterprise=FATAL, CONSOLE

# CONSOLE is set to be a ConsoleAppender using a PatternLayout.
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=%d{ISO8601} %-6r [%15.15t] %-5p %30.30c %x - %m\n
```

##### 5. 启动服务提供者

启动服务提供者常用有两种方式：

- 把服务者打包方式修改成war，然后部署到Tomcat，启动Tomcat后，IoC容器会始终存在，可以提供服务
- 通过main方法直接创建IoC容器，加载所有配置文件，保持容器不关闭、不销毁即可，可以提供服务

我们这里采用第二种方式：

```java
package com.itheima;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import java.io.IOException;

/**
 * @author liuyp
 * @date 2021/01/19
 */
public class DubboProviderMain {
    public static void main(String[] args) throws IOException {
        ApplicationContext app = new ClassPathXmlApplicationContext("classpath:spring-*.xml");

        //下面这行代码，没有任何实际作用。仅仅是为了把方法阻塞，让JVM不结束。
        //JVM不结束，则IoC容器就始终存在，dubbo框架就可以提供服务
        System.in.read();
    }
}
```



#### 5. 创建服务消费者模块`dubbo-consumer`

​	此模块是服务消费者模块，是Web应用，需要引入spring-webmvc，并且在容器启动时，要去zookeeper注册中心订阅服务，需要引入zookeeper及客户端依赖。

​    步骤如下：

1. 准备工作：创建Module，不选择骨架

   补全目录`src\main\webapp`， 在webapp里创建`WEB-INF`，在WEB-INF里创建web.xml

   修改`pom.xml`，设置打包方式为war，并添加依赖

2. 创建UserController类，并添加注解

3. 创建配置文件

   - `springmvc.xml`，主要开启组件扫描，开启mvc注解驱动
   - `spring-consumer.xml`，配置dubbo，配置要订阅的服务
   - `log4j.properties`，日志配置文件

4. 修改web.xml，配置前端控制器和编码过滤器

5. 启动服务消费者

##### 1. 准备工作

###### 创建Module，不选择骨架

###### 修改pom.xml添加依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>dubbo-demo1</artifactId>
        <groupId>com.itheima</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>dubbo-consumer</artifactId>
    <!-- 打包方式为war -->
    <packaging>war</packaging>

    <dependencies>
        <!--依赖于dubbo-interface-->
        <dependency>
            <groupId>com.itheima</groupId>
            <artifactId>dubbo-interface</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>

        <!--web层依赖-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.0</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <version>2.5</version>
            <scope>provided</scope>
        </dependency>

        <!--dubbo和zk-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>dubbo</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-framework</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-recipes</artifactId>
        </dependency>
    </dependencies>
</project>
```

##### 2. 创建UserController

<font color="red">注意：使用dubbo提供的`@Reference`注入依赖</font>

```java
package com.itheima.controller;

import com.alibaba.dubbo.config.annotation.Reference;
import com.itheima.domain.User;
import com.itheima.service.UserService;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author liuyp
 * @date 2021/01/19
 */
@RestController
@RequestMapping("/user")
public class UserController {

    //注意：使用的是dubbo提供的@Reference注解
    @Reference
    private UserService userService;

    @GetMapping("/{id}")
    public User findById(@PathVariable("id") Integer id){
        return userService.findById(id);
    }
}
```

##### 3. 创建配置文件

###### 创建`springmvc.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--开启组件扫描-->
    <context:component-scan base-package="com.itheima.controller"/>

    <!--开启mvc注解驱动-->
    <mvc:annotation-driven/>

    <!--处理静态资源-->
    <mvc:default-servlet-handler/>
</beans>
```

###### 创建`spring-consumer.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <!--注册到zk时，需要提供一个应用名称-->
    <dubbo:application name="dubbo-consumer"/>

    <!--注册到zk时，需要提供zk的地址-->
    <dubbo:registry address="zookeeper://localhost:2181"/>

    <!--扫描包。扫描@Reference注解，用于告知dubbo要远程调用哪些服务-->
    <dubbo:annotation package="com.itheima.controller"/>
</beans>
```

###### 创建`log4j.properties`

```properties
# Set root category priority to INFO and its only appender to CONSOLE.
#log4j.rootCategory=INFO, CONSOLE            debug   info   warn error fatal
log4j.rootCategory=debug, CONSOLE

# Set the enterprise logger category to FATAL and its only appender to CONSOLE.
log4j.logger.org.apache.axis.enterprise=FATAL, CONSOLE

# CONSOLE is set to be a ConsoleAppender using a PatternLayout.
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=%d{ISO8601} %-6r [%15.15t] %-5p %30.30c %x - %m\n
```

##### 4. 修改web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
    <!--配置前端控制器-->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-*.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!--配置编码过滤器-->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```

##### 5. 启动服务消费者

1. 把`dubbo-consumer`部署到Tomcat里

![image-20210119191815502](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119191815502.png)



![image-20210119191844617](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119191844617.png)

1. 启动Tomcat，使用浏览器访问测试

![image-20210119191907805](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119191907805.png)

### 步骤总结

- 创建父工程：锁定依赖版本
- 创建公共的子工程：
  - dubbo-domain：里边放实体类
  - dubbo-interface：里边放公用接口，Service层的
- 创建服务提供者
  - dao层的UserMapper， spring-dao.xml
  - service层的UserServiceImpl， spring-service.xml
  - **dubbo服务提供者相关的配置有：**
    - userServiceImpl上要使用的注解是`@Service`，要使用dubbo提供的注解，千万不要使用Spring的
    - 提供一个dubbo服务提供者的配置

![image-20210120105449158](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210120105449158.png)

- 创建服务消费者
  - 创建UserController，@RestController，springmvc.xml，web.xml
  - **dubbo消费者相关的配置有：**
    - Controller里的使用UserService时，要加注解`@Reference`
    - 提供一个spring-consumer.xml，其中要有

![image-20210120105552396](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210120105552396.png)



![image-20201228181732717](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20201228181732717.png)



## 四、配置说明

- [ ] 掌握生产者的配置
- [ ] 掌握消费者的配置
- [ ] 了解dubbo的负载均衡效果

### 1. dubbo服务提供者的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <!--配置应用名称：通常以模块名称作为应用名称-->
    <dubbo:application name="dubbo-provider"/>

    <!--配置注册中心的地址-->
    <dubbo:registry address="zookeeper://127.0.0.1:2181"/>
    
	<!--远程调用时使用的协议和端口-->
    <dubbo:protocol name="dubbo" port="20881"/>
    
    <!--扫描要暴露的服务接口-->
    <dubbo:annotation package="com.itheima.service"/>
</beans>
```

#### 应用名称

无论是服务提供者还是消费者，要注册到注册中心，就必须提供一个应用名称，并且名称不能重复。

通常是项目名称，或者模块名称作为注册的应用名称

```xml
<dubbo:application name="应用名称"/>
```

#### 注册中心

作为服务提供者，必须要把服务地址注册到注册中心里。这就需要我们配置一下注册中心的地址。

```xml
<dubbo:registry address="zookeeper://127.0.0.1:2181"/>
```

#### rpc协议和端口

服务提供者的服务是供消费者进行rpc调用的，这就需要我们给服务提供者配置协议和端口，供消费者调用。

dubbo框架支持的通信协议有：dubbo, rmi, hessian,  http, webservice, rest, redis等等

dubbo框架推荐并且默认使用：dubbo协议

- dubbo协议采用单一长连接和NIO异步通讯，适合小数据量、大并发的调用，以及消费者数量远多于提供者的情况。
- 但是dubbo协议不适合传输大数据量的服务，比如上传文件，除非请求量很少

```xml
<!-- 使用dubbo协议。dubbo协议默认端口20880，这里可以任意指定，只要没有端口冲突 -->
<dubbo:protocol name="dubbo" port="20880"/>

<!-- 使用rmi协议 -->
<dubbo:protocol name="rmi" port="11099"/>
```

#### 扫描并暴露服务

服务提供者有很多服务要暴露出去，供消费者调用。dubbo和Spring框架整合后，可以自动扫描服务，注册到IoC容器里，同时把服务地址注册到注册中心。

我们只需要：

1. 在ServiceImpl类上加注解`@Service`，<font color="red">注意是`com.alibaba.dubbo.config.annotation.Service`</font>
2. 在spring-dubbo.xml里扫描注解：

```xml
<dubbo:annotation package="com.itheima.service"/>
```

#### 服务提供者小结

```xml
<!-- 应用名称 -->
<dubbo:application name=""/>

<!-- 注册中心地址 -->
<dubbo:registry address="zookeeper://127.0.0.1:2181"/>

<!-- 扫描注解。扫描的是dubbo的@Service注解 -->
<dubbo:annotation package="com.itheima.service"/>

<!-- 远程通讯协议 -->
<dubbo:protocol name="dubbo" port="20880"/>
```



### 2. dubbo消费者的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <!--设置应用名称-->
    <dubbo:application name="dubbo-consumer"/>

    <!--设置注册中心的地址-->
    <dubbo:registry address="zookeeper://localhost:2181"/>

    <!--设置扫描包-->
    <dubbo:annotation package="com.itheima.controller"/>

    <!--消费者配置：启动时不检查，连接超时时间2000毫秒，调用失败后重试3次-->
    <dubbo:consumer check="false" timeout="2000" retries="3"/>
</beans>
```

#### 应用名称

无论是服务提供者还是消费者，要注册到注册中心，就必须提供一个应用名称，并且名称不能重复。

通常是项目名称，或者模块名称作为注册的应用名称

```xml
<dubbo:application name="应用名称"/>
```

#### 注册中心

作为服务消费者，必须要去注册中心里订阅需要访问的服务。这样的话，消费者才可以获取到服务提供者的访问地址，然后进行rpc调用。这样的话，我们在消费者的配置文件里，同样要配置注册中心的地址

```xml
<dubbo:registry address="zookeeper://127.0.0.1:2181"/>
```

#### 扫描包

消费者需要扫描Controller，注册到IoC容器里。同时Controller里要注入对应的服务Service，而这些Service不在消费者的IoC容器里，需要通过dubbo框架去rpc调用提供者。

所以我们需要：

1. 在Controller类上加`@Controller`注解

2. 在Controller类里声明要调用的服务，并在服务上使用`@Reference`声明要引用的服务

   <font color="red">注意：是`com.alibaba.dubbo.config.annotation.Reference`，千万不要导错了</font>

3. 在消费者的xml配置文件里，开启服务扫描

   扫描到`@Reference`标记的依赖，表示要通过dubbo框架来远程调用

```xml
<dubbo:annotation package="com.itheima.controller"/>
```

#### 消费者配置

```xml
<!--消费者配置-->
<dubbo:consumer check="false" timeout="2000" retries="3"/>
```

##### check服务检查

​	check属性默认值是true，作用是：当消费者启动时，就会检查依赖的服务是否可用，不可用时会抛出异常，这样的话Spring初始化就会失败。

![image-20201229163226289](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20201229163226289.png)

​	在系统上线时，设置为true，可以帮我们更早的发现问题、解决问题；

​	在开发阶段时，建议设置为false，关闭检查

##### timeout超时时间

​	Dubbo在通信时，由于网络或服务端不可靠，会导致调用过程中出现不确定的阻塞状态（超时）  。为了避免超时导致客户端线程挂起处于一致等待状态，必须设置超时时间 ，默认为1000ms。

​	可以在消费者一方，通过timeout属性来设置全局的超时时间，单位：毫秒；

​	也可以提供者一方，在ServiceImpl类上通过`@Service(timeout=3000)`来指定某一个服务的超时时间

##### retries重试次数

​	Dubbo在通信时，客户端调用失败的话会自动切换并重试其它服务器，dubbo重试的默认值是2次，我们可以自行设置重试次数。注意：消费者第一次初始化连接不算重试次数。

![image-20201229152002200](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20201229152002200.png)

​	可以在消费者一方，通过`retries`属性来设置全局的重试次数

#### 消费者配置

```xml
<!-- 应用名称  -->
<dubbo:application name=""/>
<!-- 注册中心地址 -->
<dubbo:registry address="zookeeper://127.0.0.1:2181"/>
<!-- 扫描注解。扫描的是@Reference注解 -->
<dubbo:annotation package="com.itheima.controller"/>
<!-- 消费者配置 -->
<dubbo:consumer check="true" timeout="3000" retries="3"/>
```



### 3. 负载均衡

#### 说明

​	负载均衡（Load Balance）：其实就是将请求分摊到多个操作单元上进行执行，从而共同完成工作任务。我们可以有多个服务提供者共同组建一个集群来提供服务，这样的话，消费者多次调用时，会调用到哪个服务提供者呢？这就是负载均衡要解决的问题。

Dubbo 提供了多种均衡策略：

- 随机：random，默认值
- 轮询：roundrobin
- 最少活跃调用数：leastactive
- 一致性Hash：consistenthash

配置负载均衡策略：

- 可以在服务提供者一方，使用`@Service(loadbalance="负载均衡方案")`配置
- 也可以在服务消费者一方，使用`@Reference(loadbalance="负载均衡方案")`配置

#### 示例

​	下面以消费者一方配置负载均衡为例，做负载均衡效果的演示。

​	我们再创建一个服务提供者`dubbo-provider2`，里边的配置和`dubbo-provider`几乎完全相同，只要修改使用的rpc协议和端口，保证不冲突

##### 准备服务提供者集群

1. 创建Module：`dubbo-provider2`
2. 修改`dubbo-provider2`的pom.xml，从`dubbo-provider`里把依赖拷贝过来
3. 从`dubbo-provider`里把`src`文件夹拷贝过来，粘贴到`dubbo-provider2`里
4. 修改`dubbo-provider2`里的`spring-provider.xml`，设置rpc的协议为`dubbo`、端口`20881`

![image-20210119220504443](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119220504443.png)

1. 运行`dubbo-provider2`里的`DubboProviderMain2`，启动服务提供者2

![image-20210119220602201](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119220602201.png)

1. 为了能够区别到底哪个提供者的服务被调用到了，我们分别修改`dubbo-provider`和`dubbo-provider2`里的`UserServiceImpl`如下：

![image-20210119220825723](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119220825723.png)

##### 修改`dubbo-consumer`

设置负载均衡方案，然后重启`dubbo-consumer`

![image-20210119221259478](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210119221259478.png)

##### 测试效果

​	客户端浏览器多次访问`http://localhost/user/1`，可以从idea控制台上看到`dubbo-provider`和`dubbo-provider2`被轮流访问了

## 五、原理分析

![image-20201229183125798](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20201229183125798.png)

## 六、SpringBoot整合dubbo

- [ ] 能够使用SpringBoot整合dubbo

### 步骤

1. 创建父工程`dubbo-demo2`：

   打包方式pom

   引入SpringBoot父工程坐标

   锁定依赖版本

2. 创建公共子模块`dubbo2-domain`

   创建实体类User，注意：实体类要实现Serializable接口

3. 创建公共接口模块`dubbo2-interface`

   创建公共接口UserService，接口里定义方法`findById`

4. 创建服务提供者`dubbo2-provider`

   创建接口实现UserServiceImpl，把服务注册到注册中心

5. 创建服务消费者`dubbo2-consumer`

   创建UserController，远程调用UserService，根据id查询用户

### 实现

#### 1. 创建父工程`dubbo-demo2`

```xml
    <packaging>pom</packaging>

	<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.3.RELEASE</version>
    </parent>

    <dependencyManagement>
        <dependencies>
            <!-- SpringBoot基础依赖 -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter</artifactId>
            </dependency>

            <!--dubbo的起步依赖-->
            <dependency>
                <groupId>org.apache.dubbo</groupId>
                <artifactId>dubbo-spring-boot-starter</artifactId>
                <version>2.7.5</version>
            </dependency>

            <!-- zookeeper的api管理依赖 -->
            <dependency>
                <groupId>org.apache.curator</groupId>
                <artifactId>curator-recipes</artifactId>
                <version>4.2.0</version>
            </dependency>

            <!-- zookeeper依赖 -->
            <dependency>
                <groupId>org.apache.zookeeper</groupId>
                <artifactId>zookeeper</artifactId>
                <version>3.4.12</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

#### 2. 创建公共子模块`dubbo2-domain`

创建实体类

```java
package com.itheima.pojo;

import java.io.Serializable;

public class User implements Serializable {

    private Integer id;
    private String username;
    private Integer age;

    //get/set...
}
```

#### 3. 创建公共接口模块`dubbo2-interface`

##### `pom.xml`

```xml
<dependencies>
    <!-- 引入公共实体类模块 -->
    <dependency>
        <groupId>com.itehima</groupId>
        <artifactId>dubbo2-commons</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
</dependencies>
```

##### `实体类`

```java
package com.itheima.service;

import com.itheima.pojo.User;

/**
 * @author liuyp
 * @date 2021/01/14
 */
public interface UserService {
    User findById(Integer id);
}
```

#### 4. 创建服务提供者`dubbo2-provider`

##### `pom.xml`

```xml
<dependencies>
    <!-- 引入dubbo2-interface -->
    <dependency>
        <groupId>com.itheima</groupId>
        <artifactId>dubbo2-interface</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>

    <!--MybatisPlus起步依赖-->
    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <version>3.2.0</version>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.49</version>
    </dependency>

    <!--dubbo起步依赖-->
    <dependency>
        <groupId>org.apache.dubbo</groupId>
        <artifactId>dubbo-spring-boot-starter</artifactId>
    </dependency>
    <!-- zookeeper的api管理依赖 -->
    <dependency>
        <groupId>org.apache.curator</groupId>
        <artifactId>curator-recipes</artifactId>
    </dependency>
    <!-- zookeeper依赖 -->
    <dependency>
        <groupId>org.apache.zookeeper</groupId>
        <artifactId>zookeeper</artifactId>
    </dependency>
</dependencies>
```

##### 映射器接口

```java
package com.itheima.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.itheima.domain.User;

/**
 * @author liuyp
 * @date 2021/01/19
 */
public interface UserMapper extends BaseMapper<User> {
}
```

##### Service

注意：使用的`@Service`是  `org.apache.dubbo.config.annotation.Service`

```java
package com.itheima.service.impl;

import com.itheima.domain.User;
import com.itheima.mapper.UserMapper;
import com.itheima.service.UserService;
import org.apache.dubbo.config.annotation.Service;
import org.springframework.beans.factory.annotation.Autowired;

/**
 * 注意：类上使用的是dubbo提供的@Service，不是Spring提供的@Service
 * @author liuyp
 * @date 2021/01/19
 */
@Service
public class UserServiceImpl implements UserService {

    @Autowired
    private UserMapper userMapper;

    @Override
    public User findById(Integer id) {
        return userMapper.selectById(id);
    }
}
```

##### `application.yaml`

```yaml
spring:
  datasource: # 配置数据源
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql:///dubbo_db?useSSL=false
    username: root
    password: root
mybatis-plus: # 配置MybatisPlus
  global-config:
    db-config:
      table-prefix: t_ # 统一的表名称前缀
      id-type: auto # 统一的主键类型
dubbo: # 配置dubbo服务提供者
  application:
    name: dubbo2-provider # 应用名称
  registry:
    address: zookeeper://localhost:2181 # 注册中心地址
  scan:
    base-packages: com.itheima.service # 扫描包暴露服务
  protocol: # rpc协议和端口
    name: dubbo
    port: 20880      
```

##### 引导类

注意：引导类上使用注解`@MapperScan`，扫描映射器

```java
package com.itheima;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @author liuyp
 * @date 2021/01/19
 */
@MapperScan("com.itheima.mapper")
@SpringBootApplication
public class Dubbo2ProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(Dubbo2ProviderApplication.class, args);
    }
}
```

#### 5. 创建服务消费者`dubbo2-consumer`

##### `pom.xml`

```xml
<dependencies>
    <dependency>
        <groupId>com.itheima</groupId>
        <artifactId>dubbo2-interface</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>

    <!--web起步依赖-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!--dubbo起步依赖-->
    <dependency>
        <groupId>org.apache.dubbo</groupId>
        <artifactId>dubbo-spring-boot-starter</artifactId>
    </dependency>
    <!-- zookeeper的api管理依赖 -->
    <dependency>
        <groupId>org.apache.curator</groupId>
        <artifactId>curator-recipes</artifactId>
    </dependency>
    <!-- zookeeper依赖 -->
    <dependency>
        <groupId>org.apache.zookeeper</groupId>
        <artifactId>zookeeper</artifactId>
    </dependency>
</dependencies>
```

##### `UserController`

注意：使用`@Reference`注解进行远程调用

```java
package com.itheima.controller;

import com.itheima.domain.User;
import com.itheima.service.UserService;
import org.apache.dubbo.config.annotation.Reference;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author liuyp
 * @date 2021/01/19
 */
@RestController
@RequestMapping("/user")
public class UserController {
    //注意：使用的是dubbo提供的@References
    @Reference
    private UserService userService;

    @GetMapping("/{id}")
    public User findById(@PathVariable("id") Integer id){
        return userService.findById(id);
    }
}
```

##### `application.yaml`

```yaml
dubbo:
  application:
    name: dubbo2-consumer # 应用名称
  registry:
    address: zookeeper://localhost:2181 # 注册中心地址
  scan:
    base-packages: com.itheima.controller # 扫描包，包里的@Reference会远程调用
  consumer:
    check: false # 启动时不检查服务提供者
    timeout: 3000 # 超时时间3000毫秒
    retries: 3 # 调用失败后重试3次
server:
  port: 80
```

##### 引导类

```java
package com.itheima;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @author liuyp
 * @date 2021/01/19
 */
@SpringBootApplication
public class Dubbo2ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(Dubbo2ConsumerApplication.class, args);
    }
}
```

### 小结

##### 服务提供者的配置

- 在Service类上加`@Service`注解
- 在application.yaml里配置服务提供者

```yaml
# dubbo服务提供者的配置
dubbo:
  application:
    name: dubbo2-provider # 应用名称
  registry:
    address: zookeeper://127.0.0.1:2181 # 注册中心的地址
  scan:
    base-packages: com.itheima.service # 扫描注解的包，用于扫描dubbo的@Service
  protocol:
    name: dubbo
    port: 20880
```

##### 服务消费者的配置

- 在Controller类里，要远程调用的那个UserService成员变量上加`@Reference`
- 在application.yaml里配置服务消费者

```yaml
dubbo:
  application:
    name: dubbo2-consumer # 应用名称
  registry:
    address: zookeeper://127.0.0.1:2181 # 注册中心地址
  scan:
    base-packages: com.itheima.controller # 扫描的包，主要是扫描包里@Reference注解
  consumer:
    check: false
    timeout: 3000
    retries: 3
```

## 常见问题

### 1. 实体类没有实现序列化接口

在控制台可以看到报错信息

![image-20210120154058034](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210120154058034.png)

### 2. 找不到服务提供者

去管理控制台dubbo-admin里检查一下有没有启动服务提供者

在控制台里会看到以下内容：

![image-20210120111450285](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210120111450285.png)

在控制台看到以下内容：

![image-20210120154259323](D:/IT/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AF%BE%E7%A8%8B%E8%BD%AF%E4%BB%B6%E8%B5%84%E6%96%99/%E5%B0%B1%E4%B8%9A%E7%8F%AD%E8%B5%84%E6%96%99-%E9%A1%B9%E7%9B%AE%E4%B8%80%E5%89%8D%E7%BD%AE%E8%AF%BE/day30-dubbo/%E7%AC%94%E8%AE%B0/img/image-20210120154259323.png)

