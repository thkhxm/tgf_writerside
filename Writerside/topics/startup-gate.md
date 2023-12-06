# 启动一个网关
 网关服务
## 概述
    框架对网关进行了封装,对于开发人员来说,只需要启动网关
    即可进行所有相关操作和行为,而不需要关心网关的内部逻辑.
## 代码范例

```Go
package gate

import (
	"github.com/thkhxm/tgf/rpc"
)

//***************************************************
//@Link  https://github.com/thkhxm/tgf
//@Link  https://gitee.com/timgame/tgf
//@QQ群 7400585
//author tim.huang<thkhxm@gmail.com>
//@Description
//2023/4/20
//***************************************************

func Startup() {
	r := rpc.NewRPCServer().
		WithGatewayWS("8443", "/tgf", nil).
		WithWhiteService("Login").
		Run()
	<-r
	//Server Destroy Logic
}

```

## 代码详解

`rpc.NewRPCServer()` 创建一个服务,可以理解为创建了一个进程
`WithGatewayWS("8443", "/tgf", nil)` 注册一个websocket的网关服务,监听`8443`端口,ws目录使用`/tgf`,最后一个为高级特性中的[登录离线钩子](login-offline.md)
`WithWhiteService("Login")` 注册白名单接口(**无需登录即可调用**),接口名称为`Login`(**api接口函数名称**)

## 高级特性 {id="startup-gate-top"}

- [登录离线钩子](login-offline.md)
- [推送用户消息](rpc-request-tutorial.md#rpc-tutorial-user)
- 