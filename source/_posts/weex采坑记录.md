---
title: weex采坑记录
comments: true
date: 2018-09-13 16:47:38
tags: [weex, android]
categories:
    - weex
---

### 创建项目

``` 
$ weex create appname
```

### 添加platform
```
$ weex platform add android/ios
```

### 给APP签名
```
# 生成签名文件
# android.keystore 文件名
$ keytool -genkey -alias android.keystore -keyalg RSA -validity 20000 -keystore android.keystore

# 给 apk文件 签名
# android.keystore          签名文件
# app-release-signed.apk    签名后的文件名
# app-release-unsigned.apk  要签名的apk文件
$ jarsigner -verbose -keystore android.keystore -signedjar app-release-signed.apk app-release-unsigned.apk android.keystore
```


### Error: Command failed: ./gradlew  assembleRelease

/platforms/android/app/build.gradle 文件里新增
```
aaptOptions {
    cruncherEnabled = false
    useNewCruncher = false
}
```
和 defaultConfig {} 同级

### Cannot set the value of read-only property 'outputFile' for ApkVariantOutpu

修改打包部分代码为：
```
applicationVariants.all { variant ->
    variant.outputs.all { output ->  // each 改为 all
        def fileName = "${variant.versionName}_release.apk"
        def outFile = output.outputFile
        if (outFile != null && outFile.name.endsWith('.apk')) {
            outputFileName = fileName  //  output.outputFile 改为 outputFileName 
        }
    }
}
```

### Annotation processors must be explicitly declared now.

在app的build中
```
android {
    ...
    defaultConfig {
        ...
        //添加如下配置就OK了
        javaCompileOptions { annotationProcessorOptions { includeCompileClasspath = true } }
    }
    ...
}
```

### 【Android】报错INSTALL_FAILED_TEST_ONLY

在真机安装APK的过程中报错：INSTALL_FAILED_TEST_ONLY

在查阅网上资料后，采用以下方案成功解决：
在项目的gradle.properties文件中添加android.injected.testOnly=false

如果采用上述方法不能解决，请查看以下链接中的方法：
https://blog.csdn.net/pengchanghua/article/details/79474759

参考网址：
https://blog.csdn.net/pengchanghua/article/details/79474759
https://blog.csdn.net/myislijingjing/article/details/80911565

### 图标文件夹修改

替换掉 ```/platforms/android/app/src/main/res/mipmap-xxxhdpi/ic_launcher.png``` 文件
或者 修改 ```/platforms/android/app/src/main/AndroidManifest.xml``` 中得 ```android:icon``` 

### APP名称修改

修改 ```/platforms/android/app/src/main/res/values/strings.xml``` 和
```/platforms/android/app/src/main/res/values-zh-rCN/strings.xml``` 中得 app_name

### 启动页修改

1、进入项目目录：\platforms\android\app\src\main\res? 新建文件夹：drawable

2、把背景图片放到?drawable 文件夹里面去，我这边的图片文件名是 bg.jpg

3、打开文件夹?\platforms\android\app\src\main\res\layout 找到?activity_splash.xml 文件，用编辑器打开

4、修改 FrameLayout 节点的background 属性为：@drawable/bg


### app 加固

https://www.cnblogs.com/syw20170419/p/8081321.html

### 横屏设置

在 `AndroidManifest.xml` 中 的每个 `<activity>` 中新增/修改 `android:screenOrientation="landscape"`

### 关闭调试

屏蔽 java 文件 WXPageActiviy `setContentView(R.layout.activity_wxpage);` 和 `mTipView.setText("render error:" + errCode);`

### navigator.push跳转本地文件页面空白

最简单的修复方法是在WxPageActivity.java的onCreate方法
```
try {
    JSONObject initData = new JSONObject(uri.toString());
    String bundleUrl = initData.optString("WeexBundle", null);
    if (bundleUrl != null) {
      mUri = Uri.parse(bundleUrl);
    }

    ......
    ......
} catch (JSONException e) {
    e.printStackTrace();
}
```
这里catch里面加一句 `mUri = Uri.parse(uri.toString());`
```
......
} catch (JSONException e) {
    e.printStackTrace();
    mUri = Uri.parse(uri.toString());
}
```
[参考链接-1](https://segmentfault.com/q/1010000013444038)  
[参考链接-2](http://www.shymean.com/article/%E5%88%9D%E8%AF%86weex#4.3.%20weex-%3Eweex)

### Android平台接入扫码模块
[参考地址](https://www.aliyun.com/jiaocheng/3217.html)  
注意的地方： `ScanModule.java`不需要导入这个包`import com.alibaba.weex.WXPageActivity;`

### 注意事项

- 文字必须 用 `<text>` 标签
- 样式必须每个标签添加类名（scss 类嵌套 在真机环境下不生效）
- cdn字体 一个页面只需在任意一个组件里加载一次（必须给使用的标签添加类名）
- `<text></text>` 文字居中 用 `align-self: center;` 或者 `text-align: center;`
- 手机竖屏版本，宽度单位为二倍像素
- div 嵌套不要太深(5层左右)
- 引入vue组件必须带后缀名称, 例 `import MyHeader from './components/header.vue'`
- 横屏时如果出现闪屏 ，在css设置默认宽度（默认750px）
- `:class` 仅支持array syntax ，你可以通过 `:class="[isTrue ? 'a' : '', 'b']"` 来判断是否要加入 'a' 这个 class.
- `:style` 仅支持object syntax，例如：`style="{color: index<3 ? '#fff' : '#000', 'border-bottom-width': index===2 ? 10 + 'px' : '0'}"`
- weex 不支持 `v-html` 指令。(没有dom这个说法)
- 样式单位只支持百分百（不支持百分比、em、rem、vh等）