# 用户登录
    介绍了如何使用tgf自带的登录功能进行用户的登录操作,并且编写机器人客户端的一个模拟请求代码

## 需求描述
    用户请求登录,登录成功之后请求HelloWorld接口.

## Common
    接口定义和生成

接口定义
: 新增登录接口 
```Go
type IHallService interface {
	Login(ctx context.Context, args *rpc.Args[*pb.LoginRequest], reply *rpc.Reply[*pb.LoginResponse]) (err error)
	HelloWorld(ctx context.Context, args *rpc.Args[*pb.HelloWorldRequest], reply *rpc.Reply[*pb.HelloWorldResponse]) (err error)
}
```
生成api文件
: 查看项目文档,在kit项目中创建生成api接口文件脚本
```Go
func main() {
	//设置导出的golang目录
	util.SetAutoGenerateAPICodePath("../common/api")
	//根据接口生成对应的api结构
	util.GeneratorAPI[services.IHallService](hall.ModuleName, hall.Version, "api")
}
```


## Service
    逻辑端

完善接口逻辑
: 使用`rpc.UserLogin`函数登录
```Go
func (s *service) Login(ctx context.Context, args *rpc.Args[*pb.LoginRequest], reply *rpc.Reply[*pb.LoginResponse]) (err error) {
	var userId string
	var pbData *pb.LoginResponse
	if args.GetData().GetAccount() == "admin" && args.GetData().GetPassword() == "123" {
		userId = util.GenerateSnowflakeId()
		rpc.UserLogin(ctx, userId)
		pbData = &pb.LoginResponse{Success: true}
	} else {
		pbData = &pb.LoginResponse{Success: false}
	}
	reply.SetData(pbData)
	return
}
```


## Robot
请求代码
: 监听登录和HelloWorld接口的回调,并发起请求
```Go
func TestHelloWorld(t *testing.T) {
	rb := CreateRobot()
	rb.RegisterCallbackMessage(api.HelloWorld.MessageType, func(i robot.IRobot, bytes []byte) {
		resp := &pb.HelloWorldResponse{}
		proto.Unmarshal(bytes, resp)
		t.Log(resp.Message)
	}).RegisterCallbackMessage(api.Login.MessageType, func(i robot.IRobot, bytes []byte) {
		resp := &pb.LoginResponse{}
		proto.Unmarshal(bytes, resp)
		if resp.Success {
			i.Send(api.HelloWorld.MessageType, &pb.HelloWorldRequest{Name: "tgf"})
		} else {
			t.Log("login fail")
		}
	})
	//
	rb.Send(api.Login.MessageType, &pb.LoginRequest{
		Account:  "admin",
		Password: "123",
	})
	select {}
}
```

## 交流群
    QQ群:7400585

## 下期预告

## 视频教程

[golang游戏服务器 - tgf系列课程04](https://www.bilibili.com/video/BV1Vc411q7YF/)  
[B站教程合集](https://space.bilibili.com/64497732/channel/seriesdetail?sid=3815364)

## 项目地址

[项目地址](https://github.com/thkhxm/tgf)  
[项目案例](https://github.com/thkhxm/tgf-tutorial)  
[项目文档](https://thkhxm.github.io/tgf_writerside/starter-topic.html)  
[知乎博客](https://www.zhihu.com/people/tim-30-83/posts)  
[CSDN专栏](https://blog.csdn.net/thkhxm/category_12520142.html)  