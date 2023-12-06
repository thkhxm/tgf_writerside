# 登录,离线钩子

> 我们在日常的游戏开发中,常常需要对用户的登录和登出行为,进行一些额外的操作.例如
> 
>登录之后,对玩家数据进行初始化
> 
> 登出之后,对玩家数据进行清除
> 
> 登录之后,主动推送消息到客户端
> 
> 登出之后,对玩家本地缓存数据进行数据入库
> 
> 这些操作,我们提供了统一的钩子.
>

钩子的使用方式十分简单,我们看一下下面这个例子

```Go
	r := rpc.NewRPCServer().
		WithGatewayWS("8443", "/tgf", rpc.NewUserHook().
		AddOfflineHook(propservice.UserOffline.NewEmpty())).
		AddLoginHook(propservice.UserLogin.NewEmpty())).
		WithWhiteService("Login").
		Run()
	<-r
	//Server Destroy Logic
```

我们在前面的基础教程中已经知道,上面这段代码是启动一个网关服务,并且将Login接口加入白名单中(无需登录,即可调用).

我们主要看这段代码
`rpc.NewUserHook().AddOfflineHook(propservice.UserOffline.NewEmpty())).` 
这里首先使用`rpc.NewUserHook()`创建了一个`IUserHook`的结构体

之后使用链式调用的方式`AddOfflineHook,AddLoginHook`添加需要执行的协议.这里的`propservice.UserOffline`和
`propservice.UserLogin`都是通过工具生成的rpc接口,可以查看[生成协议接口](generate-rpc.md).这里的协议参数,需要遵循
以下规范
```Go
	UserOffline(ctx context.Context, args *rpc.DefaultArgs, reply *rpc.EmptyReply) (err error)
	UserLogin(ctx context.Context, args *rpc.DefaultArgs, reply *rpc.EmptyReply) (err error)
```

> 函数名称可以随意修改,但是后面的入参不可修改`(ctx context.Context, args *rpc.DefaultArgs, reply *rpc.EmptyReply) (err error)`

最后我们只需要调用对应的`NewEmpty()`生成一个空的结构体即可.其他就可以交给框架了.

