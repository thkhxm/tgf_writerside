# 模拟客户端请求
    介绍了如何使用机器人模拟客户端发起请求。
    详细讲解了如何创建请求函数、设置白名单、进行数据反序列化等操作，感谢大家的关注。

## 交流群
    QQ群:7400585

## Service
    大厅节点操作

### 创建接口
    创建HelloWorld的请求接口

创建目录
: 根据项目文档中的开发规范, 我们在`Common`项目下创建了`services`目录

创建接口
: 创建`hall_service`接口文件,生成接口的pb文件
: 
```Go
type IHallService interface {
	HelloWorld(ctx context.Context, args *rpc.Args[*pb.HelloWorldRequest], reply *rpc.Reply[*pb.HelloWorldResponse]) (err error)
}
```

### 启动代码
    修改启动代码,增加白名单接口

修改代码
: 新增白名单
```Go
func Startup() {
	r := rpc.NewRPCServer().
		WithService(NewService()).
		WithGatewayWS("8443", "/tgf", nil).
		WithCache(tgf.CacheModuleClose).
		WithRandomServicePort(8010, 8020).
		WithWhiteService("HelloWorld"). // 新增白名单
		Run()
	<-r
}
```

### 逻辑代码
    Hall节点中,HelloWorld提供的逻辑

代码详解
: 通过`args.GetData`函数获取客户端请求数据,再通过`reply.SetData`设置客户端的响应数据
```Go
func (s *service) HelloWorld(ctx context.Context, args *rpc.Args[*pb.HelloWorldRequest], reply *rpc.Reply[*pb.HelloWorldResponse]) (err error) {
	// args.GetData() -> *pb.HelloWorldRequest
	msg := fmt.Sprintf("hello world %s", args.GetData().GetName())
	reply.SetData(&pb.HelloWorldResponse{Message: msg})
	return
}
```


## Robot
    创建robot,并发起请求

创建robot
: 创建并且连接网关服务
```Go
func CreateRobot() robot.IRobot {
	rb := robot.NewRobotWs("/tgf")
	rb.Connect("127.0.0.1:8443")
	return rb
}
```

发起请求
: 发送客户端请求(为了后续教程方便,这里使用test运行机器人)
```Go
func TestHelloWorld(t *testing.T) {
	rb := CreateRobot()
	req := &pb.HelloWorldRequest{Name: "tgf"}
	rb.RegisterCallbackMessage("hall.HelloWorld", func(i robot.IRobot, bytes []byte) {
		resp := &pb.HelloWorldResponse{}
		proto.Unmarshal(bytes, resp)
		t.Log(resp.Message)
	})
	//
	rb.SendMessage("hall", "HelloWorld", req)
	select {}
}
```
: 使用`RegisterCallbackMessage`函数,注册接口响应监听(可以不监听)
: 使用`SendMessage`函数发送客户端请求,`hall`为模块名,`HelloWorld`为接口名,`req`为请求数据


## 下期预告
    使用工具创建接口代码,并且通过rpc,向网关发起登录请求.完成登录行为.

## 视频教程

[golang游戏服务器 - tgf系列课程03](https://www.bilibili.com/video/BV1jc411q7xX/)  
[B站教程合集](https://space.bilibili.com/64497732/channel/seriesdetail?sid=3815364)

## 项目地址

[项目地址](https://github.com/thkhxm/tgf)  
[项目案例](https://github.com/thkhxm/tgf-tutorial)  
[项目文档](https://thkhxm.github.io/tgf_writerside/starter-topic.html)  
[知乎博客](https://www.zhihu.com/people/tim-30-83/posts)  
[CSDN专栏](https://blog.csdn.net/thkhxm/category_12520142.html)  