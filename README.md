## golang 构建数据服务
本部分的目标是使用 `golang database/sql`写出易于阅读、扩展和可维护的数据库服务。重点是掌握经典的 `entity - dao - service`层次结构编程模型。
```
entity层 是实体类 充当一个对象的作用，也称为模型 
dao层是使用了Hibernate连接数据库、操作数据库（增删改查 也就是你说的持久层）。
service层：引用对应的Dao数据库操作，在这里可以编写自己需要的代码（比如简单的判断调用 处理一些业务逻辑之类的）
```
参考博客：[golang 构建数据服务-pml](http://blog.csdn.net/pmlpml/article/details/78602290)

### ORM框架
对象-关系映射（Object/Relation Mapping，简称ORM），是随着面向对象的软件开发方法发展而产生的。面向对象的开发方法是当今企业级应用开发环境中的主流开发方法，关系数据库是企业级应用环境中永久存放数据的主流数据存储系统。对象和关系数据是业务实体的两种表现形式，业务实体在内存中表现为对象，在数据库中表现为关系数据。内存中的对象之间存在关联和继承关系，而在数据库中，关系数据无法直接表达多对多关联和继承关系。因此，对象-关系映射(ORM)系统一般以中间件的形式存在，主要实现程序对象到关系数据库数据的映射。

ORM方法论基于三个核心原则：

    简单性：以最基本的形式建模数据。
    传达性：数据库结构被任何人都能理解的语言文档化。
    精确性：基于数据模型创建正确标准化了的结构。
面向对象是从软件工程基本原则(如耦合、聚合、封装)的基础上发展起来的，而关系数据库则是从数学理论发展而来的，两套理论存在显著的区别。为了解决这个不匹配的现象,对象关系映射技术应运而生。O/R中字母O起源于"对象"(Object),而R则来自于"关系"(Relational)。几乎所有的程序里面，都存在对象和关系数据库。在业务逻辑层和用户界面层中，我们是面向对象的。当对象信息发生变化的时候，我们需要把对象的信息保存在关系数据库中。


本文只简要列举实现后结果和测试情况。

### 1.首先用docker安装mysql 5.7，可以参考下面的博客。
[Linux上使用docker安装mysql 5.7](http://www.linuxidc.com/Linux/2015-07/120543.htm)
![安装mysql5.7](/img/安装mysql5.7.png)
### 2.使用docker images查看本地镜像。
`docker images : 列出本地镜像。`
语法:
`docker images [OPTIONS] [REPOSITORY[:TAG]]`
OPTIONS说明：
```
-a :列出本地所有的镜像（含中间映像层，默认情况下，过滤掉中间映像层）；
--digests :显示镜像的摘要信息；
-f :显示满足条件的镜像；
--format :指定返回值的模板文件；
--no-trunc :显示完整的镜像信息；
-q :只显示镜像ID。
```

![dockerimages](/img/dockerimages.png)
### 3.启动mysql作为主机服务。主机服务（HOST Service）就是作为宿主机器的一个服务进程。
docker run 就是运行容器镜像文件系统内部的一个命令（CLI）。 
```
参数 1 是镜像 mysql:5.7 ；
-p 是容器内部网络端口到主机端口的映射；
--name 进程的名称， ID 是短唯一标识
-e 设置进程（容器）的环境变量
-d 后台运行
```

![启动mysql](/img/启动mysql.png)
### 4.启动 mysql client 访问服务器。mysql 镜像已自带了命令行客户端。
`sudo docker run -it --rm --net host mysql:5.7 "sh" 启动了容器内 sh 进程。 `
```
-it 等价于 -i -t ，表示使用当前 stdin 和 stdout 作为该进程的 io
--rm , 当该进程结束时，自动清理该容器的文件系统空间
--net host，表示容器使用当前主机的网络环境
参数1 参数2，分别是镜像和在镜像中执行的命令
```
`\#表示你处于容器的超级管理员的 shell`
`mysql -h127.0.0.1 -P3306 -uroot -proot mysql 客户端的命令`

![启动mysqlclient](/img/启动mysqlclient.png)
### 5.创建数据库test，用户表userinfo，关联用户信息表userdetail。
```
创建数据库 test ： create datebase test; 注意以 ;结束 
查询数据库： show databases; 
设定 test 为当前数据库： use test; 
删除数据库 test： drop database test;

显示表： show tables; 
显示表 userinfo 结构： describe userinfo 
执行查询语句： sql statement;
```
![建表](/img/建表.png)
![表的详情](/img/表的详情.png)
### 6.使用程序进行数据库访问，并构建数据服务。

#### 6.1 加载驱动
#### 6.2 打开数据库
#### 6.3 执行 sql 语句
#### 6.4 数据库设定
#### 6.5 数据实体定义
#### 6.6 数据访问 CRUD
CRUD 的含义，对于每个数据表（实体 / Entity ），数据操作分为四个大类：
```
Create 创建数据实体
Retrieve 获取数据实体
Update 修改数据实体
Delete 删除数据实体
```
对应的就是四类数据表操作的 sql 语句。
#### 6.7 事务与服务
#### 6.8 数据服务测试
#### 6.9 web 数据服务包装
#### 6.10 测试 web 服务，简要测试情况如下：
![测试情况](/img/测试情况.png)
### 7.8080端口数据输出测试。
![8080测试](/img/8080测试.png)
### 8.POST数据错误信息反馈。
![错误反馈](/img/错误反馈.png)
### 9.模板测试。
![测试](/img/测试.png)
### 10.使用ab命令对比 database/sql 与 orm 的性能。
ab命令参数解释如下：
```
Document Path:          /  ###请求的资源
Document Length:        28 bytes  ###文档返回的长度，不包括相应头


Concurrency Level:      1   ###并发个数
Time taken for tests:   1.116 seconds   ###总请求时间
Complete requests:      5000     ###总请求数
Failed requests:        0     ###失败的请求数
Total transferred:      725000 bytes
HTML transferred:       140000 bytes
Requests per second:    4481.76 [#/sec] (mean)      ###平均每秒的请求数
Time per request:       0.223 [ms] (mean)     ###平均每个请求消耗的时间
Time per request:       0.223 [ms] (mean, across all concurrent requests)  ###上面的请求除以并发数
Transfer rate:          634.62 [Kbytes/sec] received   ###传输速率


Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:       0    0   0.0     0        0
Processing:    0    0   0.1     0        4
Waiting:       0    0   0.1     0        4
Total:         0    0   0.1     0        4


Percentage of the requests served within a certain time (ms)
  50%  0   ###50%的请求都在0ms内完成
  66%  0
  75%  0
  80%  0
  90%  0
  95%  0
  98%  1
  99%  1
 100%  4 (longest request)
```
![sql性能](/img/sql性能.png)
![orm性能](/img/orm性能.png)
可以看到，使用orm牺牲了一定的性能，它的优缺点如下：
#### 优点：

- 程序员不再关注数据库细节，专心在业务逻辑，程序员可以不懂数据库就可以开发系统。
- 让数据库迁移变的非常方便，如果系统需要更改使用的数据库，直接改配制，不再管不同数据库之间的语法差异。
- 省时，可快速开发，因为不需要自己写复杂的SQL语句，不需要封装复杂的数据底层，这样可以节省很多时间。

#### 缺点：

- ORM不能帮你生成所有的业务语句，有些复杂的生成不了，还是需要写SQL，例如复杂的报表。
- 配制过于繁琐，出错后不好定位问题点在哪。
- 性能低，内部使用了大量反射以及数据库检测，造成性能必然低下。
- 需要额外的学习成本，虽然不需要学习数据库，但是需要学习ORM语句。
- 容易引起不规范开发，因为ORM可以在任何地方写ORM语句然后调用开发，给维护带来了很大的难度。

### 11.GoSqlTemplate 原型。
![sqlt模板](/img/sqlt模板.png)


