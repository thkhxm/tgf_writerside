# TGF框架的特点和功能
    课程介绍了TGF框架的特点和功能
> 在第一节课程中我们并不会介绍框架的使用。我们希望在这节课程中,能让你了解到tgf是一个什么样的框架

## 概要
	本节课程介绍了TGF框架的特点和功能。TGF是一个开箱即用的服务器框架,
    适合中小型团队和独立开发者进行游戏开发。

    框架内置了完整的开发工具和规范的开发原则,使开发者只需关注业务本身。
    框架还提供了一些常用的组件和通讯方式,并对接口进行了封装。

    项目文档详细介绍了如何创建服务和使用TGF框架。
    在视频教程中还展示了项目的结构和启动方式,
    并表示会持续更新框架。

## 要点

### TGF框架是一个怎样的框架
	是一个开箱即用的服务器框架,使用的是分布式集群的理念,代码十分简洁的
    
### 适合什么人群使用
    目前框架适合中小型团队和独立开发者使用,快速迭代开发.学习成本极低
    (后续版本会增加更多特性和管理工具适应大型游戏)

### 开发工具
	提供了一整套完整的开发工具,并且定义了相对规范的开发原则。

### 提供了哪些常用的通讯方式
	提供了像RPC通用通讯方式和机器人的一个通讯方式,并对所有的接口都进行了封装。

### 提供了哪些组件
	提供了像Excel转换excel转接线配置的工具,配置文件工具,权重工具,
    机器人等十分常用的组件。

### 是如何为服务注册接口的
	不需要像常规框架或者是web框架一样去进行接口的注册
    只需要实现IService接口并符合接口的结构规范,
    框架就会自动注册路由中,开发者就可以进行常规的请求操作

### 项目结构是怎样的
	在开发规范中可以看到,每一个包,每个项目都可以单独启动,也可以在一个进程内同时启动。
    项目结构十分干净,简洁,一目了然。

## 视频教程

[golang游戏服务器 - tgf系列课程01](https://www.bilibili.com/video/BV1KC4y1X74e/?share_source=copy_web&vd_source=98e878e4a1b57de8a8196c354030d753)  
[项目地址](https://github.com/thkhxm/tgf)  
[项目文档](https://thkhxm.github.io/tgf_writerside/starter-topic.html)  
[知乎博客](https://www.zhihu.com/people/tim-30-83/posts)  
[CSDN专栏](https://blog.csdn.net/thkhxm/category_12520142.html)  
[B站教程](https://space.bilibili.com/64497732/channel/seriesdetail?sid=3815364)  