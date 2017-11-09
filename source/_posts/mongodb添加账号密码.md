---
title: mongodb添加账号密码
comments: true
date: 2017-11-09 15:30:01
tags: [MongoDB]
categories:
    - MongoDB
    - 工具
---

1.输入命令：`$ show dbs`，你会发现它内置有两个数据库，一个名为admin，一个名为local；本文只对admin库进行描述

2.输入命令：`$ use admin`，你会发现该DB下包含了一个名为`system.user`的collection，这是用户表，用来存放超级管理员的

> 备注：本文使用的数据库版本是3.4.5，没有默认的admin数据库，但是在执行第二步之后自动创建了一个admin库； 当然也没有默认的system.user表，运行后面的第三步后会自动创建 `system.user`和`system.indexes` )

3.输入命令：`$ db.createUser({user:root",pwd:"root",roles:["root"]})`，这里我添加一个超级管理员用户，username为root，password也为root,这里设置其角色为root。先退出 (ctrl+c)程序，测试重启服务后再次连接MongoDB是否需要按提示输入用户名、密码进行操作。

4.输入命令：`$ use admin`

5.输入命令：`$ show collections`，查看该库下所有的表，你会发现，MongoDB并没有提示你输入用户名、密码，原因是，在文章最开始提到了，MongoDB默认设置为无权限访问限制，我们需要先把它设置成为需要权限访问

6.从新打开cmd，在mongodb路径的bin目录下，执行`$ mongod --dbpath=/data/db --port=27017 --fork --auth --logpath=/var/log/mongo.log`

7.输入命令：`$ use admin`

8.输入命令：`$ show collections`，提示：`"$err" : "unauthorized db:admin lock type:-1 client:127.0.0.1"`

显然，已经提示没有权限；用刚才设置的用户名、密码来访问集合

9.输入命令：`$ db.auth(“root”,”root”)`，输出一个结果值为1，说明这个用户匹配上了，如果用户名、密码不对，输出为0

10.输入命令：`$ show collections`，将成功显示结果继续操作，可以访问已经存在的数据库，但对于新建的数据库仍然没有权限；继续操作，先退出(ctrl+c)服务

11.输入命令：`$ mongo TestDB`

12.输入命令：`$ show collections`，提示：没有权限

13.输入命令：`$ db.auth(“root”, “root”)`，输出结果为0，说明用户名或者密码有问题，刚刚前面才创建，怎么会不对呢？原因在于：当我们单独访问MongoDB的数据库时，需要权限访问的情况下，用户名密码并非超级管理员，而是该库的system.user表中的用户，注意，我这里说的是单独访问的情况，什么是不单独访问的情况呢？后面再讲。针对上述情况，接下来操作：

14.输入命令：`$ db.createUser({user: "testdb1u1", pwd: "xyz123", roles: [{ role: "dbOwner", db: "TestDB" }]})`，这里设置其角色为dbOwner,仍然提示没有权限，新的数据库使用超级管理员也无法访问，创建用户也没有权限，不过即然设定了超级管理员用户，那它就一定有权限访问所有的库

15.输入命令：`$ use admin`

16.输入命令：`$ db.auth(“root”, “root”)`

17.输入命令：`$ use TestDB`

18.输入命令：`$ show collections`，之后可以利用超级管理员用户访问其它库了，这个就是不单独访问的情况。在上述操作过程中，我们是先进入admin库，再转到其它库来的，admin相当于是一个最高级别用户所在的区域，对数据库操作，需要经过最高级别用户，之后可以创建每个数据库的用户。

19.输入命令：`$ db.createUser({user: "testdb1u1", pwd: "xyz123", roles: [{ role: "dbOwner", db: "TestDB" }]})`，我们给TestDB库添加一个用户，以后每次访问该库，我都使用刚刚创建的这个用户，我们先退出（ctrl+c）

20.输入命令：`$ mongo TestDB`

21.输入命令：`$ show collections`，提示没有权限

22.输入命令：`$ db.auth('testdb1u1','xyz123')`，输出结果1，用户存在，验证成功

23.输入命令：`$ show collections`，成功显示结果