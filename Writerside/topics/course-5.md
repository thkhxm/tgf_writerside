# 服务器rpc调用
    介绍了在TGF框架中实现RPC调用的过程,以及RPC结构自动生成功能。

## 需求描述
    用户登录成功之后, 请求大厅的LoadUserData接口,大厅节点通过rpc,获取用户在Prop节点的道具信息.

## Common
    创建rpc请求结构文件,定义接口,并生成对应的api和rpc结构文件

> rpc请求结构,为常规的数据结构即可.并无特殊的地方,可以参考视频教程或者[项目案例](https://github.com/thkhxm/tgf-tutorial)中的代码.
> api接口在上一节中已经讲解过如何生成.如果有疑问可以参考[上一节](course-4.md)

接口定义
: 遵循rpc接口的[规范](rpc-standard.md)定义.
```Go
type IPropRPCService interface {
	GetUserPropCount(ctx context.Context, args *model.GetUserPropArgs, reply *model.GetUserPropReply) (err error)
}
```

生成rpc结构文件
: 查看[项目文档](generate-rpc.md),在kit项目中创建生成rpc接口文件脚本
```Go
func main() {
	//设置导出的golang目录
	util.SetAutoGenerateAPICodePath("../common/api")
	//设置生成的文件后缀
	util.SetGenerateFileNameSuffix("rpc")
	//根据接口生成对应的rpc结构
	util.GeneratorRPC[services.IPropRPCService](prop.ModuleName, prop.Version, "propservice", "prop")
}
```

## Service
    新增prop逻辑节点,hall逻辑节点新增`LoadUserData`逻辑

> 新增prop节点,可以参考前面的章节[教程](course-2.md)创建.

大厅节点逻辑
: 在LoadUserData函数中调用rpc请求
```Go
func (s *service) LoadUserData(ctx context.Context, args *rpc.Args[*pb.LoadUserDataRequest], reply *rpc.Reply[*pb.LoadUserDataResponse]) (err error) {
	// 用户登录成功之后, 请求大厅的LoadUserData接口,大厅节点通过rpc,获取用户在Prop节点的道具信息.
	propId, _ := util.AnyToStr(rand.Int31n(10))
	rpcReply := &model.GetUserPropReply{}
	// 发送rpc请求,阻塞等待响应
	rpc.SendRPCMessage(ctx, propservice.GetUserPropCount.New(&model.GetUserPropArgs{PropId: propId}, rpcReply))
	log.DebugTag("hall", "load user data propId=%v count=%v", propId, rpcReply.Count)
	reply.SetData(&pb.LoadUserDataResponse{Name: "tgf framework", PropCount: rpcReply.Count})
	return
}
```

道具节点逻辑
: 根据道具id获取数量并返回
```Go
func (s *service) GetUserPropCount(ctx context.Context, args *model.GetUserPropArgs, reply *model.GetUserPropReply) (err error) {
	userId := rpc.GetUserId(ctx)
	reply.Count = s.propCountCache[args.PropId]
	log.DebugTag("prop", "get %s user %s prop count %d ", userId, args.PropId, reply.Count)
	return
}
```

## Robot
    模拟客户端请求

代码
: 用户请求登录,登录成功之后,请求加载用户数据.
```Go
func TestRPCRobot(t *testing.T) {
	rb := CreateRobot()
	rb.RegisterCallbackMessage(api.Login.MessageType, func(i robot.IRobot, bytes []byte) {
		resp := &pb.LoginResponse{}
		proto.Unmarshal(bytes, resp)
		if resp.Success {
			i.Send(api.LoadUserData.MessageType, &pb.LoadUserDataRequest{})
		} else {
			t.Log("login fail")
		}
	}).RegisterCallbackMessage(api.LoadUserData.MessageType, func(i robot.IRobot, bytes []byte) {
		resp := &pb.LoadUserDataResponse{}
		proto.Unmarshal(bytes, resp)
		t.Log(resp.Name, resp.PropCount)
	})
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
    使用excel2json工具,生成配置相关文件,并且获取配置数据

## 视频教程

[golang游戏服务器 - tgf系列课程05](https://www.bilibili.com/video/BV1tj411s7co/)  
[B站教程合集](https://space.bilibili.com/64497732/channel/seriesdetail?sid=3815364)

## 项目地址

[项目地址](https://github.com/thkhxm/tgf)  
[项目案例](https://github.com/thkhxm/tgf-tutorial)  
[项目文档](https://thkhxm.github.io/tgf_writerside/starter-topic.html)  
[知乎博客](https://www.zhihu.com/people/tim-30-83/posts)  
[CSDN专栏](https://blog.csdn.net/thkhxm/category_12520142.html)  