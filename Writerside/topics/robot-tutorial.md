# 机器人
机器人相关操作

## 概述
机器人工具提供了完整的客户端模拟操作,可以进行自动化测试,或者压测等行为.是项目开发中不可多得的助手.

## 代码范例
我们来看一下下面的伪代码：
```Go
func main() {
	component.WithConfPath("../common/conf/js")
	component.InitGameConfToMem()
	r := robot.NewRobotWs("/tgf")
	r.Connect("127.0.0.1:8443")
	r.RegisterCallbackMessage(
		api.Login.MessageType,
		LoginCallBack,
	).RegisterCallbackMessage(
		api.LoadUserMapLevelInfo.MessageType,
		LoadUserMapLevelInfo,
	)
	time.NewTimer(time.Second * 2)
	r.Send(api.Login.MessageType, &pb.LoginReq{
		Account:  "test3",
		Password: "123456",
	})
	select {}
}

func LoginCallBack(robot robot.IRobot, data []byte) {
	res := &pb.LoginRes{}
	proto.Unmarshal(data, res)
	log.Info("login success userid %v", res.UserId)
	//robot.Send(api.LoadUserMapLevelInfo.MessageType, &pb.LoadUserInfoReq{})
	robot.Send(api.LoadUserPropInfo.MessageType, &pb.LoadUserInfoReq{})
}

func LoadUserMapLevelInfo(robot robot.IRobot, data []byte) {
	res := &pb.LoadUserMapLevelInfo{}
	proto.Unmarshal(data, res)
	log.Info("load user map level info  %v", res.GetUserData())
	mapConf, _ := component.GetGameConf[*conf.MapConf](res.UserData.MapId)
	robot.Send(api.ChallengeBoss.MessageType, &pb.DefaultRequest{Val: mapConf.ClearanceCondition})
}
```
## 代码详解
### 初始化机器人
```Go
    component.WithConfPath("../common/conf/js")
	component.InitGameConfToMem()
	r := robot.NewRobotWs("/tgf")
	r.Connect("127.0.0.1:8443")
```
初始化机器人,并且与网关建立了websocket的连接.  

`component.WithConfPath` 初始化配置文件
`component.InitGameConfToMem` 加载配置文件
`r := robot.NewRobotWs("/tgf")` 创建一个使用websocket的机器人
`r.Connect` 连接服务器网关

### 注册回调
```Go
r.RegisterCallbackMessage(
		api.Login.MessageType,
		LoginCallBack,
	).RegisterCallbackMessage(
		api.LoadUserMapLevelInfo.MessageType,
		LoadUserMapLevelInfo,
	)
```
使用链式调用的方式,注册消息回调
`RegisterCallbackMessage` 注册消息回调,`api.Login.MessageType`是自动生成代码中的属性,
可以参考[生成协议结构](generate-rpc.md#generate-rpc-client-resource)机器人在收到该响应消息时,会执行`LoginCallBack`函数

### 发送请求
```Go
	r.Send(api.Login.MessageType, &pb.LoginReq{
		Account:  "test3",
		Password: "123456",
	})
```
发送消息到网关
`r.Send` 机器人的请求函数, `api.Login.MessageType`是自动生成代码中的属性,
可以参考[生成协议结构](generate-rpc.md#generate-rpc-client-resource).

> 我们可以通过上面提供的函数,构建一个自动化的机器人,每个机器人根据不同的场景加入随机函数,进行不同操作可以实现机器人的功能.
> 也可以覆盖所有api接口进行自动化测试的机器人构建.