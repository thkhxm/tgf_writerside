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
    ...
}

func (m *Module) Destroy(sub IService) {
    ...
}

func (m *Module) GetUserHook() IUserHook {
    ...
}

func (m *Module) StateHandler(ctx context.Context, args *client.ConsulServerState, reply *string) (err error) {
    ...
}
```

## 结构分析
### service 
所有的service都需要实现`rpc.IService`接口,接口中提供了一个服务的基础数据信息和基础逻辑切面.

GetName()string
: 该函数获取当前服务的名称,这个名称需要唯一不可变,一般是该节点提供的服务名称,建议小写

GetVersion() string
: 版本号, 主要用于在管理后台或者日志中,查看当前节点的版本信息.版本信息规范为 `a.b`, 其中a为大版本号,b为小版本号  

Startup() (bool, error)
: 启动切面,该函数会在节点启动之后执行,我们通常会在这里对一些数据进行初始化

Destroy(sub IService)
: 停服切面,我们会捕获停服信号,当我们进行常规的停服操作时,会触发该函数

### module
所有的service都推荐引用的结构.包含service的默认处理.例如 `Destroy`,`StateHandler`,UserHook的初始化.

StateHandler() error
: 服务器状态变更的回调函数,开发人员可以在这里处理服状态变更之后产生的业务处理,例如数据迁移,数据缓存等操作.
:
```Go
func (m *UserService) StateHandler(ctx context.Context, args *client.ConsulServerState, reply *string) (err error) {
    //服务器切换成维护状态,将用户数据实时同步到缓存,保证用户的数据完整,并且移除用户的业务节点缓存,让用户重新路由节点.
}
```
