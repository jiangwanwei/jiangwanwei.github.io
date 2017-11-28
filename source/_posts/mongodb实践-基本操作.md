---
title: mongodb实践-基本操作
comments: true
date: 2017-11-09 22:08:43
tags: [MongoDB]
categories:
    # - MongoDB
    - 工具
---

linux 命令

``` bash
$ which mongod  // 查看命令是否可执行

$ mkdir -p /data/db    // 层级创建

$ df -lh    // 查看根目录剩余空间
```

# 启动
```
// 启动参数  (mac 权限不足 需要加sudo)
$ mongod  --dbpath=/data/db --port=27017 
 // 指定储存目录和启动端口 ，启动端口默认为27017    

$ mongod  --dbpath=/data/db --port=27017  --fork --syslog
//  fork:打印日志  syslog 日志储存在系统目录(/var/log/messages)  （程序会在后台启动）
// 使用 tail -f /var/log/messages  可以监听日志
// 结束后台启动的mongodb     mongod --shutdown 或者 kill 进程号
也可以自己指定日志文件
$ mongod --dbpath=/data/db --port=27017 --fork --logpath=/var/log/mongo.log  (推荐)


客户端启动
$ mongo   或者  mongo  127.0.0.1:27017  // 127.0.0.1:27017为你要连接的地址
```

# 关闭
```
$ use admin
$ db.shutdownServer()
$ db.shutdownServer({force : true}) 强制关闭Mongod，应对副本集中主从时间差超过10s时不允许关闭主库的情况
不要使用kill直接杀mongo进程的方式关闭数据节点，会造成数据损坏
```


# 操作命令
```
$ show dbs   // 显示所有数据库
$ use dbname // 切换到dbname数据库   (不存在自动创建)
$ show collections  // 查看当前表的 数据

$ db.users.insert({'name': 'ss'})  // 插入  users 会自动创建
$ db.createCollection("mycollection")  // 单独创建

$ db.getCollectionNames() // 查看表数据  已数组形式返回 -> [ "system.indexes", "system.users", "system.version" ]

$ db.users.find()  // 查找  find({'name': 'ss'})  find可以跟参数 
$ db.users.find().count()  // 查找总数
$ db.user.find().pretty()  // 格式化查找内容
$ db.user.find().limit(NUMBER)   // 查找条数
$ db.user.find().skip(NUMBER)   // 跳过条数
.sort({'uid':1})    // 排序  1 升序 -1 降序
ensureIndex()方法的基本语法如下
$ db.COLLECTION_NAME.ensureIndex({KEY:1})
这里键是要创建索引字段，1是按名称升序排序。若以按降序创建索引，需要使用 -1.

$ db.mycol.find({"likes":{$lt:50}})  === where likes < 50
$ db.mycol.find({"likes":{$lte:50}})  === where likes <= 50
$ db.mycol.find({"likes":{$gt:50}}) === where likes > 50
$ db.mycol.find({"likes":{$gte:50}}) === where likes >= 50
$ db.mycol.find({"likes":{$ne:50}}) === where likes != 50

or:
$ db.mycol.find(
   {
      $or: [
	     {key1: value1}, {key2:value2}
      ]
   }
)

$ db.users.update({username: 'Sid'}, {$set: {username: 222}}) // 更新一条{username: 'Sid'}数据
$ db.users.update({username: 'fff'}, {$set: {username: '777'}}, {multi: true})  // 更新所有 {username: 'fff'} 的数据   {multi: true} 更新所有

$ db.users.save({'_id': ObjectId("5960f48dc93f8047ff3dcd7a"), username: 'siddd'})
 //  更新 id相同的数据  ，id为必填条件     后面的数据指定多少条保存多少条

$ db.users.remove({username:777}) // 删除所有符合条件的数据
$ db.users.remove({username: '777'}, true)  // 删除第一条符合条件的数据
$ db.users.remove({})  // 删除文档   如果有设置索引是不会删除
$ db.users.drop()  // 删除文档和索引  返回 true false
// 删除当前数据库
$ use mydb
switched to db mydb
$ db.dropDatabase()
$ { "dropped" : "mydb", "ok" : 1 }

```