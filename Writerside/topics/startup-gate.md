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
	r := rpc.NewRPCServer(). //创建一个服务
		WithGatewayWS("8443", "/tgf"). //启动一个网关,绑定端口和路径
		WithWhiteService("login.Login"). //白名单,不验证
		Run()
	<-r
	//Server Destroy Logic
}

```

## 高级特性 {id="startup-gate-top"}

- [推送用户消息](rpc-request-tutorial.md#rpc-tutorial-user)
- [登录离线钩子](login-offline.md)