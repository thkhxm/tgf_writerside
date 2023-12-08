# 准备工作 
基础服务部署

>  框架中用到了以下几个第三方的工具,需要提前安装并启动.

## consul
    为底层rpcx提供服务发现的服务,必须启动.
官网安装
: 访问[官网](https://developer.hashicorp.com/consul/downloads)下载对应的包，开发环境中可以直接启动单机模式
: 
```
Window启动命令 
.\consul.exe agent -dev -node=consul -config-dir=F:\server_tool\consul\default.json  
```
: json配置文件，启动之后会默认自动生成

Docker安装
: 访问[DockerHub](https://hub.docker.com/_/consul) 参考文档安装即可

## redis
    为框架和业务提供数据的缓存,(用户路由缓存基于redis实现,建议部署)

官网安装
: 访问[官网](https://redis.io/download/)下载对应环境的安装包，开发环境中可以直接启动单机模式

Docker安装
: 访问[DockerHub](https://hub.docker.com/_/redis) 参考文档安装即可

## Mysql
    可选安装,在redis的基础上提供冷数据的存放.配合redis的ttl可以对冷热数据进行有效管理    

官网安装
: 访问[官网](https://dev.mysql.com/downloads/mysql/)下载对应环境的安装包，开发环境中可以直接启动单机模式

Docker安装
: 访问[DockerHub](https://hub.docker.com/_/mysql) 参考文档安装即可

## protobuf
    用户和服务器之间的通讯协议,需要安装对应环境的protoc和protoc-gen-go工具.

官网安装
: 访问[官网](https://protobuf.dev/reference/go/go-generated/) 

Github
: 当然我们也可以下载[Github](https://github.com/protocolbuffers/protobuf)的源码,自己进行编译

示例项目
: 示例项目中,包含对应的工具,可以直接使用,建议配合idea的GenProtobuf