---
title: taro开发注意事项
comments: true
date: 2019-09-04 21:27:00
tags:
  - Taro
categories:
  - 前端
  - 多终端开发
---

> 近两个项目的开发采用的是 `Taro` 多终端开发框架，一个为优电车管段微信小程序、另一个是奇遇的Wap版本，这里列一下我在开发中遇到的注意事项。

## 框架简介

用官方的一句简单的概括就是 "多端统一开发解决方案"（京东 *凹凸实验室* 出品，star 20131），重点是一套 ***遵循 `React` 语法规范*** 的 多端开发 解决方案。这也就能帮我们前端er们解决这个多终端的问题，帮我们节省大量开发时间，从此以后 wap、微信小程序、支付宝小程序、百度小程序、快应用、字节跳动小程序、qq轻应用、ReactNative 一套代码轻松搞定，当然由于各个终端格子的表现形式不一，我们不得不针对性的做一些代码兼容（官方也提供兼容处理形式：以文件后缀来区分针对不同的端打包运行 eg: vehicle.h5.js 只会针对h5运行...，以及环境变量的提供 `process.env.TARO_ENV = weapp / swan / alipay / h5 / rn / tt`，具体开发场景自己针对性的选择），但很明显也比同样的功能正对不同的平台多次开发便利很多。

## 语法风格

Taro 的语法规则基于 React 规范，它采用与 React 一致的组件化思想，组件生命周期与 React 保持一致，同时在书写体验上也尽量与 React 类似，支持使用 JSX 语法，让代码具有更丰富的表现力。但是有一点就是react更新后的一段时间 Taro才会更新相同的语法功能，例如hooks在我们现在使用的版本未支持，但是其实react很早就更新了这块内容，而taro在1.30 bate版本才支持。

> 现在多端开发有基于react语法的taro、也有基于vue的uniapp，滴滴的chameleon等，说明这一块值得我们前端掌握。因为小微信小程序、支付宝小程序等api以及写法都大同小异的比较难用。

## 不足
  
2018年4月份第一个正式版推出第一个版本，到现在的时间也不过才一年多：

1、***存在bug*** 比如选择上传方法 `Taro.chooseImage` 在小程序表现正常但是在H5中选择返回的File对象无效的问题，在1.3的版本里面已经修复(多上issues找找，或许你遇到的问题能找到决绝方案)。

2、***生态圈不成熟*** 虽然能像写 `react` 一样开发，但是要在taro中使用必须要针对taro开发，也就是说基于react的小工具、插件等不能运行在taro，我们不得不自己去做一些轮子。（目前Taro团队也在征集优秀的库 [官方物料市场](https://taro-ext.jd.com/?page=1), 看样子征集不理想还比较少，质量也貌似不太好）

3、***React自身部分语法不能使用*** 因为是多终端，所以像wap能用的BOM方法、DOM方法以及react能用的方法等在其他端就不能生效等。(cache、localstorage、React.createClass等)

4、***配置存在差异*** react中我们大多完全基于webpack打包，而在taro中webpack配置只是配置中的一部分，所以你熟悉之前写webpack配置在taro中会存在写法上的差异，但是大同小异。

5、***代码写法差异*** 比如react引用中请求可以用其他任意工具比如axios、fetch等，但是在taro中只能用 `Taro.request` ，其他还有分享等。

...

尽管才出来一年多，但是还是有很多不少的公司已投入生产环境，其中不乏大公司的部分业务在使用（京东、拼多多、猫眼等）



## 安装

```bash
# 使用 yarn 安装 CLI
$ npm install -g @tarojs/cli

# 创建项目
$ taro init myApp

```

*cli 很重要，必须安装并且和开发依赖的taro包保持一致。比如我们开发中依赖的 taro版本为1.2.23，那么cli的版本对应的要一致，因为我们在开发中，以及生产打包都会用到cli，如果我们依赖的版本高，而我们本地cli版本低，那么就会存在部分语法在cli中不识别从而导致打包或运行失败。*

安装成功后可以看到目录结构跟我们 用 `cerate-react-app` 创建的目录差不多, 数据方面我们选择的模板是redux，异步数据saga的安装方式没有变化。

## 配置

项目配置：各个平台有自己的配置文件。(微信小程序，project.config.json、百度智能小程序，project.swan.json、快应用，manifest.json) 其他平台暂无；

打包配置：`/config/dev.js | /config/prod.js` 中对应的webpack配置方式（小程序和h5有单独自己的配置.h5 .weapp），例如：

```js
module.exports = {
  env: {
    NODE_ENV: '"development"'
  },
  defineConstants: {
  },
  weapp: {},
  h5: {
    devServer: {
      port: 10087,
      proxy: {
        '/api/': {
          target: 'https://motoapi.sodacar.com',
          pathRewrite: {
            '^/api/': '/'
          },
          changeOrigin: true
        },
        '/auth/': {
          target: 'https://motoauthapi.sodacar.com',
          pathRewrite: {
            '^/auth/': '/'
          },
          changeOrigin: true
        },
      }
    },
    // 合并reducers 和sagas
    webpackChain (chain, webpack) {
      chain.merge({
        plugin: {
          install: {
            plugin: new reduxSagaHelperPlugin(),
          }
        }
      })
    }
  }
}
```

我们更多的关注的是 `webpackChain、devServer、router、cssModule、alias、plugins` 的配置。

详细参考 [编译配置详情](https://taro-docs.jd.com/taro/docs/config-detail.html)

*需要提一下的就是全局plugin，目前有四个固定的配置babel、uglify、csso、sass，配置api和官方一模一样。例如我们需要配置sass相关:*

```js
...
plugins: {
  sass: {
    resource: path.resolve(__dirname, '..', 'src/styles/variable.scss'),
    // OR 
    // resource:  ['path/to/global.variable.scss', 'path/to/global.mixin.scss']
    projectDirectory: path.resolve(__dirname, '..'),
    data: '$nav-height: 48px;',
  }
}
// resource: 如果要引入多个文件，支持数组形式传入
// projectDirectory: 项目根目录的绝对地址(若为小程序云开发模板，则应该是client目录)
// data: 全局 scss 变量，若 data 与 resource 中设置了同样的变量，则 data 的优先级高于 resource
...
```

## 路由

路由方面来说跟 `react` 比起来，那就差的太多了，最多只能用过高阶组件来做到一些公用的逻辑操作（例如身份验证等），应该是考虑到小程序是没有组件继承的说法（当然H5相对支持要好些，比如能支持组件反向继承时对render进行劫持来做一些判断性的组件显示等，看到taro官方说后续会进行实现）。路由配置都是在 `src/app.js` 中, 如：

```js
class App extends Component {

  config = {
    pages: [
      'pages/index/index',
      'pages/login/index',
      'pages/notFound/index',
    ],
    ...
```

跳转api为 `Taro.navigateTo`, 例如： 

```js
// 传入参数 id=2&type=test
Taro.navigateTo({
  url: '/pages/page/path/name?id=2&type=test'
})

// 获取方式
class C extends Taro.Component {
  componentWillMount () {
    console.log(this.$router.params) // 输出 { id: 2, type: 'test' }
  }
}
```

需要注意的是：

`this.$router.path`  所返回的内容H5和小程序存在差异，小程序返回页面路径 `/pages/login/index`，而H5返回的是 `config/index.js` 中的 `h5.router.customRoutes` 配置，也就是浏览器路径 `/login`(hash、history模式相同)。

为了保持多终端通用，很显然 wap能用的 `hash` 就不能使用了。

在h5中，原生的跳转跟 `src/app.js` 中配置的路径不一样，例如 `'pages/index/index'` 在浏览器对应的地址就是 `http://localhost:3000/#/pages/index/index`， 很显然难以接受，如果需要使用我们习惯的 `history` 模式，我们需要在配置中( `/config/index.js` )做如下类似的修改:
```js
h5: {
    publicPath: '/',
    ...
    // 路由配置
    router: {
      mode: 'browser',
      basename: '/',  // 基准路径的配置
      customRoutes: {   // 自定义路由配置
        '/pages/index/index': '/index',
        '/pages/my/index': '/my',
        '/pages/my/info/index': '/my/info',
```
 调用 Taro.navigateTo({ url: '/pages/index/index' }) 后，浏览器地址栏将被变为 http://{{domain}}/index。

 跳转 `?page=1 or #hash` 页面都会刷新，所有生命周期都会重新执行。
 
  ***注意：Taro.navigateTo 的 url 参数必须 填写 customRoutes 配置中的key，不能 使用 Taro.navigateTo({ url: '/my' })。***


### 路由H5 404处理

目前没有看到处理方式，包括官方的例子的（h5）404都没有做处理，直接报错白屏。

目前项目中使用的处理方式比较粗暴：

```js
// 自定义H5 404跳转
if (isH5) {
  window.onerror = e => {
    if (e.match('Can not find proper registered route for')) {
      location = '/'
    }
  }
}
```

## 样式方面

无差别，`app.js 中 import 'global.scss'` 为全局通用。组件中 引用 `import style from 'index.module.scss'` 即可。

样式的写法上，各个终端存在一定的差异。例如 `background: #fff url(xxx.png) 30px/100px no-repeat;` 简写在RN中不能生效、以及部分属性不支持等。

推荐使用flex布局，由于RN 只支持flex布局（更重要的是flex布局使用更方便，现在的设备支持情况也良好）。

详情可参考 [样式的条件编译](https://taro-docs.jd.com/taro/docs/before-dev-remind.html#%E6%A0%B7%E5%BC%8F%E7%9A%84%E6%9D%A1%E4%BB%B6%E7%BC%96%E8%AF%91) | [特殊问题的处理](https://taro-docs.jd.com/taro/docs/specials.html) | [组件的外部样式和全局样式](https://taro-docs.jd.com/taro/docs/component-style.html)。


## UI组件差异

跟小程序类似，只能使用自己的组件 例如 `View\Text`等，但是在H5中能使用 web自己的组件(div、header、footer等)。如果考虑多终端，那么就不要使用平台自身的东西。

UI库就没有react那么丰富了，目前只有官方提供的库 [TaroUI](https://taro-ui.aotu.io/) (目前以后 2271 star)。


## 生命周期

相差无几，多了 `componentDidShow`，相当于 react 中的 `componentWillMount` ，小程序中的 `onShow`。

另外多了一个 `config` 配置：
```js
export default class Index extends Component {
  config = {
    navigationBarBackgroundColor: '#ffffff',
    navigationBarTextStyle: 'black',
    navigationBarTitleText: '首页',
    backgroundColor: '#eeeeee',
    backgroundTextStyle: 'light'
  }
  ...
```

相对的是 小程序的对应的配置。`navigationBarTitleText` 与 `document.title` 相等。

还有小程序专属的函数：
下拉刷新事件: `onPullDownRefresh()`  上拉触底事件: `onReachBottom()` 滑动页面 `onPageScroll()` 分享配置`onShareAppMessage()`等等 [详细配置](https://taro-docs.jd.com/taro/docs/tutorial.html#%E9%A1%B5%E9%9D%A2%E4%BA%8B%E4%BB%B6%E5%A4%84%E7%90%86%E5%87%BD%E6%95%B0)。


还有就是 `componentWillMount` 表现差异问题，由于小程序里在 `onLoad` 里才能拿到路由参数信息，而页面 `onLoad` 前组件已经固定了。因此页面的 `componentWillMount` 会有和预期表现不一致的问题。
处理方法可以用state存起来。

*建议放到都放到 `DidMount` 中执行，因为本身react的 `WillMount` 是存在重复执行的bug*

最后一个taro 提供了一个预加载的钩子 `componentWillPreload`, 因为在小程序中，从调用 Taro.navigateTo、Taro.redirectTo 或 Taro.switchTab 后，到页面触发 componentWillMount 会有一定延时。因此一些网络请求可以提前到发起跳转前一刻去请求。 而 `componentWillPreload` 就是为了处理这个问题，它接收页面跳转的参数作为参数。可以把需要预加载的内容通过 return 返回，然后在页面触发 componentWillMount 后即可通过 this.$preloadData 获取到预加载的内容。eg:

```js
// 官方例子
class Index extends Component {
  componentWillMount () {
    console.log('isFetching: ', this.isFetching)
    this.$preloadData
      .then(res => {
        console.log('res: ', res)
        this.isFetching = false
      })
  }

  componentWillPreload (params) {
    return this.fetchData(params.url)
  }

  fetchData () {
    this.isFetching = true
    ...
  }
}
```




## 渲染

组件props的值如果是组件  那么参数名称必须以 rednerXXX开头的驼峰形式。但是 `children` 可以传任意类型。eg:  

```jsx
<MyComponent renderHeader={<View className={`xxx`}></View>} />  // correct


// 这样同样会有错误（h5没问题、小程序中存在错误）
render() {
  const renderSuffix = <Text>1</Text>

  return <MyComponent renderSuffix={renderSuffix}>
}    // error

// 这样则正确
render() {

  return <MyComponent renderSuffix={<Text>1</Text>}>
}    // correct


// renderSuffix 内容只能是 jsx, 不能为其他的（H5中可以是任意类型、但是小程序中必须为jsx）

```



暂不支持在 `render()` 之外的方法定义 JSX (v1.3.0-beta.0 起支持)


不能在 JSX 参数中使用对象展开符 (v1.3.0-beta.0 起，自定义组件可以使用对象展开符，内置组件仍然需要分别单独传入参数) eg: 

```js
let params = {a: 1, b: 2}

<MyComponent ...params />  // error

<MyComponent a={params.a} ... />

```

在 Taro 中，父组件要往子组件传递函数，属性名必须以 on 开头，这是因为微信小程序端组件化是不能直接传递函数类型给子组件的，在 Taro 中是借助组件的事件机制来实现这一特性，而小程序中传入事件的时候属性名写法为 `bindmyevent` 或者 `bind:myevent`。


给组件设置 defaultProps：

我们知道在小程序中组件属性只有定义在 `properties` 中，才能被识别，eg:

```js
Component({
  properties: {
    props1: { // 属性名
      type: String, // 类型（必填），目前接受的类型包括：String, Number, Boolean, Object, Array, null（表示任意类型）
      value: '', // 属性初始值（可选），如果未指定则会根据类型选择一个
      observer:  (newVal, oldVal, changedPath) => {
         // 属性被改变时执行的函数（可选），也可以写成在 methods 段中定义的方法名字符串, 如：'_propertyChange'
         // 通常 newVal 就是新设置的数据， oldVal 是旧数据
      }
    },
    props2: String, // 简化的定义方式
  }
  ...
})
```

而在taro中规定则是每个组件属性必须定义在 `defaultProps` 中，eg:

```js
class Component extends Taro.Component {
  static defaultProps = {
    props1: '',
    ...
  }
}
```

通过设置 `defaultProps` 在运行时用来处理编译时处理不到的异常情况，而所有的属性都会被追加到 `properties` 中初始化组件，设置正确的 `defaultProps` 能够避免一些异常的情况的出现。

不要用 `id、class、style` 作为自定义组件的 `props` 与内部 `state` 的名称，因为这些属性名在微信小程序中会丢失。

不要在 `state` 与 `props` 中用相同名称的字段，原因是这些被字段在转成小程序后都会放到 `this.data` 上（eg：会触发错误 `ESLint(taro/duplicate-name-of-state-and-props) 错误：this.state.value 与 this.props.value 重复可能会导致渲染结不如意料之中的结果`）。

Taro 里尽量使用单引号，尤其是 JSX，使用双引号 可能会导致编译错误。

在JSX中，不支持柯里化（自 v1.3.0-beta.1 开始支持）。

`export { default as iconEye } from './eye.png'`  暂时不支持这样统一暴露。*小程序*

jsx中不要写匿名函数，会遇到的情况就是H5解析没问题，但是在小程序解析时会报错。*小程序*

高阶组件中的 `super.render()` 在小程序中无法使用，因为小程序编译后是没有render的，`jsx` 部分会被编译成 `wxml`，`return` 之前的数据处理会被编译到 _createData() 方法里。*小程序*

自定义组件不能和 内置组件名称相同 （Button Input 等），会触发 `Error: 内置组件名: 'Input' 只能从 @tarojs/components 引入。` 错误提示。*小程序*

循环中的 ref 只能使用函数。(ref 仅支持传入字符串、匿名箭头函数和 class 中已声明的函数) *小程序*

组件事件传参只能在使用匿名箭头函数，或使用类作用域下的确切引用(this.handleXX || this.props.handleXX)，或使用 bind。*小程序*

render 函数定义一个不从 this.state 解构或赋值而来的变量，此变量又与 this.state 下的变量重名可能会导致无法渲染。*小程序* eg:

```jsx
  ...
  render() {
    const { len, error, focus, complite, } = this.props
    const { value } = this.state
    const gridStyle = `${styles.grid} ${error ? (value.length === len && styles.err) : ''}`
    return (
      <View className={styles.box}>
        <MYInput
          focus={value.length === len ? false : focus}   // 长度达到6位自动隐藏键盘
          className={styles.ipt}
          type='number'
          onInput={e => {
            const value = e.detail.value     // 这里定义的value会触发此错误，解决方案此处的value更换为不予state.value字段重复即可
            this.setState({ value }, e => {
              if (value.length === len) {
                try {
                  this.inputRef.inputRef.blur()
                  complite(this)
                } catch (e) {
                  Toast({ title: e.message, icon: 'error' })
                }
              }
            })
          }}
  ...
```

在 `scss` 中第一样和最后一行 注释 不能使用 `// 注释内容`,  解决方案 要么使用 `/* 注释内容 */` 或者 删掉注释。*小程序*

`this.props.children` 为只读, 如需在自定义方法中调用  需要用到ES6对象解析 `const {children} = this.props`，否则会触发 `(taro/render-props) 错误：无法操作 this.props.children` 错误。*小程序*

自定义组件 `render` 不能返回 `null`,会触发错误 `组件Marker路径错误，请检查！（可能原因是导出的组件名不正确）`。*小程序*

在样式文件里不能使用本地图作为背景图片，即 `background: url(../assets....) `。需要使用网络图片或者使用 `<Image />` 标签。 *小程序*


## 资源引入

在react 中我们能  在jsx中动态引入图片,eg: 

```js

...

render() {
  return <Image src={require(`@/assets/images/${this.state.index}.png`)} />
}
```

但是在taro中，目前不支持，但是他提供了一个需要手动配置的配置项 [copy](https://nervjs.github.io/taro/docs/config-detail.html#copy) 来达到此目的。
手动指定需要copy的目录，就ok了， eg：

```js
copy: {
  patterns: [
    { from: 'src/asset/tt/', to: 'dist/asset/tt/', ignore: '*.js' }, // 指定需要 copy 的目录
    { from: 'src/asset/tt/sd.jpg', to: 'dist/asset/tt/sd.jpg' } // 指定需要 copy 的文件
  ]
},


// used
<Image src={`/assets/images/${this.state.index}.png`} />
```

*原因是 地址替换这一步是放到编译时做的，需在编译时找到引入的图片然后 copy 到 dist 目录。*



## 跨平台开发

如上简介所说：

官方也提供兼容处理形式：以文件后缀来区分针对不同的端打包运行 eg: `vehicle.h5.js` 只会针对h5运行...，以及环境变量的提供 `process.env.TARO_ENV = weapp / swan / alipay / h5 / rn / tt`


## 数据管理

taro推荐使用redux来管理数据，刚好和我们相同。但是如果小项目小功能的程序那么久不适合用数据管理了(太重)。自己维护一个global.js就可以，eg:

```js
const globalData = {}

export function set (key, val) {
  globalData[key] = val
}

export function get (key) {
  return globalData[key]
}


// use
import { set as setGlobalData, get as getGlobalData } from './path/name/global_data'

setGlobalData('test', 1)

getGlobalData('test')
```


## 性能

性能方面可优化的其实跟react相差无几:

可以通过 `shouldComponentUpdate` 来避免重复渲染

以及 `React.PureComponent` 在taro中同样有 `Taro.PureComponent`，

在react中新增的useMemo，taro中同样有 `Taro.memo`。

...


## 支付宝、微信、字节跳动小程序差异。



其他注意事项已经在官网有详细的说明,[开发前注意
](https://taro-docs.jd.com/taro/docs/before-dev-remind.html) | [特殊问题的处理](https://taro-docs.jd.com/taro/docs/specials.html) 。