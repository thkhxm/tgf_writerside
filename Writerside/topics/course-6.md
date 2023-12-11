# 游戏配置
    使用框架提供的游戏配置工具,只要两步,开箱即用

## 需求描述
    沿用上一节课的案例, 创建道具表,通过道具id在道具服中获取配置中道具的名称

## Excel
    创建配置表

![course6-excel.png](course-6-excel.png)

根据项目文档中进阶教程目录下ExcelToJson的教程文档,创建指定格式的Excel文件.

脚本
: 生成脚本
```Go
func main() {
	//关闭框架缓存
	db.WithCacheModule(tgf.CacheModuleClose)
	//设置excel路径
	util.SetExcelPath("./excel")
	//设置excel导出的go文件路径
	util.SetExcelToGoPath("../common/conf")
	//设置excel导出的json文件路径
	util.SetExcelToJsonPath("../common/conf/js")
	//开始导出excel
	util.ExcelExport()
}
```
生成代码
: 配置结构文件
```Go
type PropConf struct {
	//唯一id
	Id string
	//道具名称
	Name string
}
```

## Prop节点
    道具节点相关修改

### 启动函数
    修改节点的启动函数,追加指定json路径

代码
: json路径配置
```Go
func Startup() {
	r := rpc.NewRPCServer().
		WithRandomServicePort(8021, 8030).
		WithCache(tgf.CacheModuleClose).
		WithService(NewService()).
		WithGameConfig("../Common/conf/js").  //新增的json路径指向
		Run()
	<-r
}
```

### 逻辑代码使用
    通过框架提供的函数,使用生成后的配置文件

代码
: 核心使用函数`component.GetGameConf[*conf.PropConf](args.PropId)`通过泛型指定和传入的主键id,获取配置相关内容
```Go
func (s *service) GetUserPropCount(ctx context.Context, args *model.GetUserPropArgs, reply *model.GetUserPropReply) (err error) {
	userId := rpc.GetUserId(ctx)
	reply.Count = s.propCountCache[args.PropId]
	if propConfig, h := component.GetGameConf[*conf.PropConf](args.PropId); h {
		reply.Name = propConfig.Name
	}
	log.DebugTag("prop", "get %s user %s prop %s count %d ", userId, args.PropId, reply.Name, reply.Count)
	return
}
```

> 更多高级用法,可以查看[项目文档](ExcelToJson.md)相关介绍.

## 交流群
    QQ群:7400585


## 下期预告
    使用数据管理工具,对玩家数据进行自动化的缓存管理.

## 视频教程

[golang游戏服务器 - tgf系列课程06](https://www.bilibili.com/video/BV1EG411e7yz/)  
[B站教程合集](https://space.bilibili.com/64497732/channel/seriesdetail?sid=3815364)

## 项目地址

[Github项目地址](https://github.com/thkhxm/tgf)  
[Gitee项目地址](https://gitee.com/timgame/tgf)  
[项目案例](https://github.com/thkhxm/tgf-tutorial)  
[项目文档](https://thkhxm.github.io/tgf_writerside/starter-topic.html)  
[知乎博客](https://www.zhihu.com/people/tim-30-83/posts)  
[CSDN专栏](https://blog.csdn.net/thkhxm/category_12520142.html)  