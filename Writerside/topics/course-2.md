
# 环境准备和服务创建
    课程介绍了TGF框架的前期的准备工作,启动一个websocket网关服务,和大厅逻辑节点。
    文章最后附有项目案例地址和视频教程地址,下期预告等信息


## 安装第三方软件
    tgf框架的服务发现依赖于Consul,所以我们需要先安装并启动Consul

### 官网安装
访问[官网](https://developer.hashicorp.com/consul/downloads)下载对应的包，开发环境中可以直接启动单机模式

```
Window启动命令 
.\consul.exe agent -dev -node=consul -config-dir=F:\server_tool\consul\default.json  
```
json配置文件，启动之后会默认自动生成

### Docker安装
访问[DockerHub](https://hub.docker.com/_/consul) 参考文档安装即可

> 更多第三方软件安装,可以查看我们的[文档](Build-workspace.md)


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

## 初始化项目
    对项目进行初始化
1.创建work工作空间
: 我们在根目录命令行中执行 `go work init` 命令,创建一个work工作空间

2.初始化所有项目的mod管理
: 切换到各个项目的目录下, 执行 `go mod init xxxx/xxx/xxx/xxx` xxx为自定义参数

3.导入tgf框架
: 切换到common项目,执行 `go get -u github.com/thkhxm/tgf` 命令导入框架

## 创建服务节点
    gate,hall节点的创建和启动

#### gate服务
启动service函数:
```Go
func Startup() {
	r := rpc.NewRPCServer().
		WithGatewayWS("8443", "/tgf", nil).
		WithCache(tgf.CacheModuleClose).
		Run()
	<-r
}
```


#### hall服务
创建service:
```Go
type service struct {
}
func (s *service) Login(ctx context.Context, args *rpc.Args[*pb.LoginRequest], reply *rpc.Reply[*pb.LoginResponse]) (err error) {
	log.DebugTag("hall", "hall login")
	return
}
func (s *service) GetName() string {
	return "hall"
}
func (s *service) GetVersion() string {
	return "1.0.0"
}
func (s *service) Startup() (bool, error) {
	log.DebugTag("hall", "hall startup")
	return true, nil
}
func (s *service) Destroy(sub rpc.IService) {
	log.DebugTag("hall", "hall destroy")
}
func NewService() rpc.IService {
	return &service{}
}
```
启动service函数:
```Go
	r := rpc.NewRPCServer().
		WithService(NewService()).
		WithGatewayWS("8443", "/tgf", nil).
		WithCache(tgf.CacheModuleClose).
		WithRandomServicePort(8010, 8020).
		Run()
	<-r
```

## 下期预告
    下一节课我们会讲解如何使用robot发起请求,进行登录和请求操作.

## 视频教程

[golang游戏服务器 - tgf系列课程02](https://www.bilibili.com/video/BV1a64y157DQ/)  
[项目地址](https://github.com/thkhxm/tgf)  
[项目文档](https://thkhxm.github.io/tgf_writerside/starter-topic.html)  
[知乎博客](https://www.zhihu.com/people/tim-30-83/posts)  
[CSDN专栏](https://blog.csdn.net/thkhxm/category_12520142.html)  
[B站教程](https://space.bilibili.com/64497732/channel/seriesdetail?sid=3815364)  

