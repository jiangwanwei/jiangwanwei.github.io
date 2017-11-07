---
title: Automator添加自己的启动工具
comments: true
date: 2017-11-07 10:37:33
tags:
    - Mac
categories:
    - Mac
---

> 在Mac系统上，Finder选中一个目录，右键菜单并没有“通过Code打开”这个操作。不过我们可以通过Automator自己添加这个操作。
>这将大大提升将来的操作快捷度。

先运行Automator，选择“服务”：

然后，执行以下操作：


1.在右侧面板选择“服务”收到选定的“文件夹”，位于“Finder.app“，该选项是为了从Finder中接收一个文件夹；

2.在左侧面板选择”实用工具“，然后找到”运行Shell脚本“，把它拽到右侧面板里；

3.在右侧”运行Shell脚本“的面板里，选择Shell”/bin/bash“，传递输入“作为自变量”，然后修改Shell脚本如下：

```
for f in "$@"
do
    open -a "Visual Studio Code" "$f"
done
```

保存为“Open With VSCode”后，打开Finder，选中一个文件夹，点击右键，“服务”，就可以看到“Open With VSCode”菜单：

