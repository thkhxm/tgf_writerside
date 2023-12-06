# 准备工作 
基础服务部署

>  框架中用到了以下几个第三方的工具,需要提前安装并启动.

## consul

为底层rpcx提供服务发现的服务,**必须启动**.

访问[官网](https://developer.hashicorp.com/consul/downloads)下载对应的包，开发环境中可以直接启动单机模式,推荐使用docker部署

```
Window启动命令 
.\consul.exe agent -dev -node=consul -config-dir=F:\server_tool\consul\default.json

##json配置文件，启动之后会默认自动生成
```

## redis

为框架和业务提供数据的缓存

访问[官网](https://redis.io/download/)下载对应环境的安装包，开发环境中可以直接启动单机模式，推荐使用docker部署

## protobuf

用户和服务器之间的通讯协议,需要安装对应环境的protoc和protoc-gen-go工具.

```
示例项目中,包含对应的工具,可以直接使用,建议配合idea的GenProtobuf
```