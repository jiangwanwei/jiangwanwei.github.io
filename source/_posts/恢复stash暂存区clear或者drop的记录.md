---
title: 恢复stash暂存区clear或者drop的记录
comments: true
date: 2017-11-06 22:01:58
tags: [前端工具, Git]
categories:
    - 工具
---

首先输入
``` bash
$ git fsck --lost-found
```
 
会看到 一条一条的记录 类似 
``` bash
dangling commit 7010e0447be96627fde29961d420d887533d7796
```
复制 **dangling commit**  的id（其他的dangling blob不用理会） 

然后输入
``` bash
$ git show 7010e0447be96627fde29961d420d887533d7796
```
 
查看具体内容， 找到你想要的记录
记录中会描述日期和摘要，
日期是你git stash 的日期， 
摘要会记录你是在哪一条commit 上进行git stash操作的， 
类似（WIP on integration-xf: 2e205ac Merge branch 'release' into develop）
貌似只能一条记录一条记录的查看
 
找到你想要的记录后输入
``` bash
$ git merge 7010e0447be96627fde29961d420d887533d7796
```

这样就还原了你git stash drop, git stash clear  的内容