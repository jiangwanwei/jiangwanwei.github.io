---
title: AOP面向切面编程
comments: true
date: 2020-06-08 09:01:28
tags:
  - AOP面向切面编程
categories:
  - Other
---

`AOP`不一定都像`Spring AOP`那样，是在运行时生成代理对象来织入的，还可以在编译期、类加载期织入，比如AspectJ。

下面再慢慢聊`AOP`。
什么时候要用到面向切面`AOP`呢？
举个例子，你想给你的网站加上鉴权，
对某些`url`，你认为不需要鉴权就可以访问，
对于某些`url`，你认为需要有特定权限的用户才能访问
如果你依然使用`OOP`，面向对象，
那你只能在那些`url`对应的`Controller`代码里面，一个一个写上鉴权的代码
而如果你使用了`AOP`呢？
```java
protected void configure(HttpSecurity http) throws Exception {
      http
        .authorizeRequests()
           .antMatchers("/static","/register").permitAll()
           .antMatchers("/user/**").hasRoles("USER", "ADMIN") 
```
这样的做法，对原有代码毫无入侵性，这就是`AOP`的好处了，把和主业务无关的事情，放到代码外面去做。
所以当你下次发现某一行代码经常在你的`Controller`里出现，比如方法入口日志打印，那就要考虑使用`AOP`来精简你的代码了。

聊完了`AOP`是啥，现在再来聊聊实现原理。
`AOP`像`OOP`一样，只是一种编程范式，`AOP`并没有规定说，实现`AOP`协议的代码，要用什么方式去实现。
比如上面的鉴权的例子，假设我要给`UserController`的`saveUser()`方法加入鉴权，
第一种方式，我可以采用代理模式，
什么是代理模式，就是我再生成一个代理类，去代理`UserController`的`saveUser()`方法，代码大概就长这样：
```java
class UserControllerProxy {
    private UserController userController;

    public void saveUser() {
        checkAuth();
        userController.saveUser();
    }
}
```
这样在实际调用`saveUser()`时，我调用的是代理对象的`saveUser()`方法，从而实现了鉴权。
代理分为静态代理和动态代理，静态代理，顾名思义，就是你自己写代理对象，动态代理，则是在运行期，生成一个代理对象。
`Spring AOP`就是基于动态代理的，如果要代理的对象，实现了某个接口，那么Spring AOP会使用JDK Proxy，去创建代理对象，而对于没有实现接口的对象，就无法使用JDK Proxy去进行代理了（为啥？你写一个`JDK Proxy`的demo就知道了），这时候`Spring AOP`会使用`Cglib`，生成一个被代理对象的子类，来作为代理，放一张图出来就明白了：

好，上面讲的是`AOP`的第一种实现，运行时织入。
但是不是所有`AOP`的实现都是在运行时进行织入的，因为这样效率太低了，而且只能针对方法进行`AOP`，无法针对构造函数、字段进行`AOP`。