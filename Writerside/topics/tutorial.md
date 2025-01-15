# 快速开始
> 该示例中,我们需要先启动consul本地服务,并且使用默认的端口.在这个示例中,我们并没有涉及配置相关的介绍
>
> [项目路径](https://github.com/thkhxm/tgf_example/tree/main/quick-start)
> 
## 导入mod

```Go
go get -u github.com/thkhxm/tgf
```

## 指定版本
    修改项目的go.mod文件,或者go.work文件.手动指定版本(推荐使用go.work管理项目)
```gomodule
replace (
	github.com/rpcxio/rpcx-consul  => github.com/thkhxm/rpcx-consul v1.0.1
	github.com/smallnest/rpcx  => github.com/thkhxm/rpcx v1.0.6
	github.com/go-echarts/go-echarts/v2  => github.com/go-echarts/go-echarts/v2 v2.2.3
	github.com/alitto/pond => 	github.com/alitto/pond v1.8.3
)
```

## 创建service服务
```Go
package quick_start

import (
	"context"
	"github.com/thkhxm/tgf"
	"github.com/thkhxm/tgf/log"
	"github.com/thkhxm/tgf/rpc"
	"github.com/thkhxm/tgf_example/common/pb"
)

//***************************************************
//@Link  https://github.com/thkhxm/tgf
//@Link  https://gitee.com/timgame/tgf
//@QQ群 7400585
//author tim.huang<thkhxm@gmail.com>
//@Description
//2023/11/21
//***************************************************

func Startup() {
	c := rpc.NewRPCServer(). //创建一个rpc服务
					WithGatewayWS("8032", "/example"). //启动一个网关
					WithService(&HallService{}).       //启动一个service的服务
					WithWhiteService("hall.SayHello").      //添加该rpc到白名单,无需登录即可访问
					WithCache(tgf.CacheModuleClose).   //关闭redis等缓存服务
					Run()
	select {
	case <-c:
		log.InfoTag("service", "service is down ")

	}
}

// HallService
// @Description: implements rpc.IService
type HallService struct {
	rpc.Module
}

func (h *HallService) SayHello(ctx context.Context, args *rpc.Args[*pb.DefaultRequest], reply *rpc.Reply[*pb.DefaultRequest]) (err error) {
	log.InfoTag("debug", "%s say hello ", args.GetData().Val)
	reply.SetData(&pb.DefaultRequest{Val: "good luck"})
	return
}

func (h *HallService) GetName() string {
	return "hall"
}

func (h *HallService) GetVersion() string {
	return "1.0"
}

func (h *HallService) Startup() (bool, error) {
	return true, nil
}

```

## 启动测试
```Go
package quick_start_test

import (
	"github.com/thkhxm/tgf/robot"
	"github.com/thkhxm/tgf_example/common/pb"
	quick_start "github.com/thkhxm/tgf_example/quick-start"
	"google.golang.org/protobuf/proto"
	"testing"
)

//***************************************************
//@Link  https://github.com/thkhxm/tgf
//@Link  https://gitee.com/timgame/tgf
//@QQ群 7400585
//author tim.huang<thkhxm@gmail.com>
//@Description
//2023/11/22
//***************************************************

func TestStartup(t *testing.T) {
	//启动服务
	quick_start.Startup()
}

func TestHallService_SayHello(t *testing.T) {
	//创建一个websocket的机器人
	client := robot.NewRobotWs("/example")
	//连接到指定路径
	client.Connect("127.0.0.1:8032")
	//注册消息响应处理
	client.RegisterCallbackMessage("hall.SayHello", func(iRobot robot.IRobot, bytes []byte) {
		result := &pb.DefaultRequest{}
		err := proto.Unmarshal(bytes, result)
		if err != nil {
			t.Logf("proto unmarshal error %v", err)
			return
		}
		t.Logf("say hello call back , he say : %s", result.Val)
	})
	//发送消息
	client.SendMessage("hall", "SayHello", &pb.DefaultRequest{Val: "tim"})
	//挂起等待
	select {}
}
```
在上方代码中,可以看到我们使用 quick_start.Startup() 启动了一个service

之后我们启动下方的TestHallService_SayHello函数,这个函数使用了框架提供的robot工具,创建了一个机器人,并且对接口进行了请求测试,和回调的监听

至此我们完整的创建并开启了一组服务!十分简洁明了!! 后续我们会介绍统一的项目规范和使用工具快捷开发.


<seealso>
<!--Give some related links to how-to articles-->
</seealso>
