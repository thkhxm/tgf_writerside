# 启动一个服务

> 启动之前,需要保证已经依照[准备工作](Build-workspace.md)完成了前期的准备

## 代码范例

创建一个service
:
```Go
package login
import (
	"context"
	"github.com/thkhxm/tgf/log"
	"github.com/thkhxm/tgf/rpc"
	"github.com/thkhxm/tgf/xgame/common/pb"
)
//***************************************************
//@Link  https://github.com/thkhxm/tgf
//@Link  https://gitee.com/timgame/tgf
//@QQ群 7400585
//author tim.huang<thkhxm@gmail.com>
//@Description
//2023/4/15
//***************************************************
var (
	Version    = "1.0"
	ModuleName = "login"
)
// service
// @Description: 登录相关服务
type service struct {
	rpc.Module
}
func (s *service) Login(ctx context.Context, args *rpc.Args[*pb.LoginReq], reply *rpc.Reply[*pb.LoginRes]) (err error) {
	return
}
func (s *service) GetName() string {
	return s.Name
}
func (s *service) GetVersion() string {
	return s.Version
}
func (s *service) Startup() (bool, error) {
	log.InfoTag("login", "login service startup")
	return true, nil
}
func (s *service) Destroy(sub rpc.IService) {
	log.InfoTag("login", "login service destroy")
}
func NewService() rpc.IService {
	s := &service{}
	s.Name = ModuleName
	s.Version = Version
	return s
}
```

实现`rpc#IService`接口  
依赖`rpc.Module`  

> 最少有一个服务器或客户端协议函数
>
{style = "warning"}


单逻辑节点启动类
:
```Go
package login
import "github.com/thkhxm/tgf/rpc"
//***************************************************
//@Link  https://github.com/thkhxm/tgf
//@Link  https://gitee.com/timgame/tgf
//@QQ群 7400585
//author tim.huang<thkhxm@gmail.com>
//@Description
//2023/4/15
//***************************************************
func Startup() {
	r := rpc.NewRPCServer(). //创建一个服务
		WithRandomServicePort(8010, 8020). //该服务随机监听8010-8020之间任意一个端口
		WithService(NewService()). //为这个服务,注册登录逻辑节点,这里可以通过链式调用,注册多个不同的逻辑节点
		Run()
	<-r
	//Server Destroy Logic
}
```

**正常来说,我们会为每一个逻辑节点创建一个服务,但是我们在调试阶段,或者在初期,可以使用`WithService`一次性注册多个不同的业务  
逻辑节点.**