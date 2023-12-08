# 系列课程2
    课程介绍了TGF框架的前期的准备工作,启动一个websocket网关服务,和大厅逻辑节点。
    并且使用机器人连接这个网关.发起请求

## 安装第三方软件
    tgf框架的服务发现依赖于Consul,所以我们需要先安装并启动Consul

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

> 更多第三方软件安装,可以查看我们的[文档](Build-workspace.md)

{style="note"}

## 创建项目
    根据开发规范,创建所需目录和项目目录

### service
    网关节点,大厅节点

gate
: 网关节点,持有客户端连接,转发客户端请求

hall
: 大厅节点,业务逻辑节点,处理业务逻辑


### common
    通用项目

service目录
: 节点接口,通常以_service结尾.

api目录
: 存放我们自动生成的协议文件,该目录我们不需要修改

### kit
    工具箱

cmd
: 存放工具箱的运行脚本

proto
: pb相关的运行文件,不包含.proto文件




