# 逻辑节点

提供客户端所需接口,和rpc接口的逻辑节点结构.

## 示例代码
```Go
package login

import (
	"context"
	"github.com/thkhxm/tgf/log"
	"github.com/thkhxm/tgf/rpc"
	"github.com/thkhxm/tgf/xgame/common/pb"
	"github.com/thkhxm/tgf/xgame/login/internal"
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
	//*** do something
	return
}

func (s *service) Init() {
	var ()
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

```Go
// IService
//
//	@Description: 逻辑服务接口
type IService interface {
	GetName() string
	GetVersion() string
	Startup() (bool, error)
	Destroy(sub IService)
}
```

```Go
type Module struct {
	Name    string
	Version string
}
```

## 结构分析
所有的service都需要实现`rpc.IService`接口,接口中提供了一个服务的基础数据信息和基础逻辑切面.

GetName()string
: 该函数获取当前服务的名称,这个名称需要唯一不可变,一般是该节点提供的服务名称

GetVersion() string
: 版本号, 主要用于在管理后台或者日志中,查看当前节点的版本信息

Startup() (bool, error)
: 启动切面,该函数会在节点启动之后执行,我们通常会在这里对一些数据进行初始化

Destroy(sub IService)
: 停服切面,我们会捕获停服信号,当我们进行常规的停服操作时,会触发该函数



