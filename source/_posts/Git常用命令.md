---
title: Git常用命令
comments: true
date: 2017-07-07 21:55:31
tags: [前端工具, Git]
categories:
    - 工具
---

``` bash
$ git init          // 把当前的目录变成可以管理的git仓库，生成隐藏.git文件。
```

``` bash
$ git add XX       // 把xx文件添加到暂存区去。
```

``` bash
$ git commit –m “XX”  // 提交文件 –m 后面的是注释。
```
``` bash
$ git status        // 查看仓库状态
```

``` bash
$ git diff  XX      // 查看XX文件修改了那些内容
```

``` bash
$ git log          // 查看历史记录
```

``` bash
$ git reset  --hard HEAD^ 或者 git reset  --hard HEAD~ // 回退到上一个版本 (如果想回退到100个版本，使用git reset –hard HEAD~100 )
```

``` bash
$ git reflog       // 查看历史记录的版本号id
```

``` bash
$ git checkout -- XX  // 把XX文件在工作区的修改全部撤销。
```

``` bash
$ git rm XX          // 删除XX文件
```

``` bash
$ git remote add origin https://github.com/tugenhua0707/testgit // 关联一个远程库
```

``` bash
$ git push –u(第一次要用-u 以后不需要) origin master // 把当master分支推送到远程库
```

``` bash
$ git clone https://github.com/tugenhua0707/testgit  // 从远程库中克隆
```
``` bash
$ git checkout –b dev  // 创建dev分支 并切换到dev分支上
```

``` bash
$ git branch  // 查看当前所有的分支
```

``` bash
$ git checkout master // 切换回master分支
```

``` bash
$ git merge dev    // 在当前的分支上合并dev分支
```
$ git branch –d dev // 删除dev分支
```
$ git branch name  // 创建分支
```

``` bash
$ git stash // 把当前的工作隐藏起来 等以后恢复现场后继续工作
```

``` bash
$ git stash list // 查看所有被隐藏的文件列表
```

``` bash
$ git stash apply // 恢复被隐藏的文件，但是内容不删除
```

``` bash
$ git stash drop // 删除文件
```

``` bash
$ git stash pop // 恢复文件的同时 也删除文件
```

``` bash
$ git remote // 查看远程库的信息
```

``` bash
$ git remote –v // 查看远程库的详细信息
```

``` bash
$ git push origin master  // Git会把master分支推送到远程库对应的远程分支上
```

``` bash
$ git push origin 本地分支:远程分支   // 推送本地分支到远程
```


多人协作工作模式一般是这样的：
  1. 首先，可以试图用git push origin branch-name推送自己的修改.
  2. 如果推送失败，则因为远程分支比你的本地更新早，需要先用git pull试图合并。
  3. 如果合并有冲突，则需要解决冲突，并在本地提交。再用git push origin branch-name推送。