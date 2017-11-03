---
title: 'sublime text 3 安装 install package'
comments: true
date: 2017-03-06 21:22:13
tags:
    - Sublime Text3
    - 前端工具
---

打开Sublime Text3 ，按Ctrl+`（和qq输入法快捷切换冲突，可以修改qq的输入法切换热键）

复制粘黏以下代码添加至命令行，然后回车
``` bash
$ import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())
```

重启Sublime Text 3