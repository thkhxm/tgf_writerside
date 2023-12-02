# ExcelToJson

Excel文件自动转化Json文件,Golang的.go文件,C#的.cs文件. all in one . 

## excel讲解

![excel1](excel_1.png)

这是一份常规配置

第一行`v1.0`为当前配置的版本号, 用于区分当前配置的版本. 我们在程序中并不会对这个版本号做任何操作.

第二行,是配置的字段名,我们会根据这个字段名生成对应的golang结构中的字段名.所以我们首字母需要大写,并且遵循规范的命名规则.

第三行,这里是字段的类型,我们以golang的基础类型为准,支持 `string`,`int all,uint all`,`[]`,`bool`, 其余数据类型会在后续陆续添加

第四行,这里是字段描述.我们会使用该行生成结构体字段对应的注释

第五行开始为配置数据,常规数据类型,可以直接填入所需数据即可,如果是一维数组,那么我们使用`,`分割,最终的效果如`1,4,6,7,9`


![excel2](excel_2.png)


这是一份错误码配置,我们继续分析一下

我们可以看到,它与常规的配置是一样的, 唯一有区别的地方在于我们定义了一列`FieldName`数据,这一列数据在这里表示我们将要生成的错误码结构常量名.我们会在后面的自动生成中,解释它的用途

## 自动生成

```Go
package main

import (
	"github.com/thkhxm/tgf"
	"github.com/thkhxm/tgf/component"
	"github.com/thkhxm/tgf/db"
	"github.com/thkhxm/tgf/util"
	"github.com/thkhxm/tgf_example/common/conf"
)

// ***************************************************
// @Link  https://github.com/thkhxm/tgf
// @Link  https://gitee.com/timgame/tgf
// @QQ群 7400585
// author tim.huang<thkhxm@gmail.com>
// @Description
// 2023/4/18
// ***************************************************
func main() {
	//关闭框架缓存
	db.WithCacheModule(tgf.CacheModuleClose)
	//设置excel路径
	util.SetExcelPath("./excel")
	//设置excel导出的go文件路径
	util.SetExcelToGoPath("../common/conf")
	//设置excel导出的json文件路径
	util.SetExcelToJsonPath("../common/conf/js")
	//给前端路径也导出一份
	//util.SetExcelToJsonPath("E:\\unity\\project\\t2\\Assets\\Config\\js")
	
	//设置excel导出的unity文件路径
	util.SetExcelToUnityPath("E:\\unity\\project\\t2\\Assets\\HotFix\\Config")
	//设置excel导出的unity命名空间
	util.SetExcelToUnityNamespace("HotFix.Config")

	//开始导出excel
	util.ExcelExport()

	//设置配置源数据路径
	component.WithConfPath("../common/conf/js")
	//初始化游戏配置到内存中
	component.InitGameConfToMem()
	//获取配置数据
	codes := component.GetAllGameConf[*conf.ErrorCodeConf]()
	//初始化结构化kv数据源
	data := make([]util.TemplateKeyValueData, len(codes), len(codes))
	for i, code := range codes {
		data[i] = util.TemplateKeyValueData{
			FieldName: code.FieldName,
			Values:    code.Code,
		}
	}
	//将数据源写入文件 生成kv结构文件
	util.JsonToKeyValueGoFile("errorcodes", "error_code", "../common/define/errorcode", "int32", data)
	//
	//获取配置数据
	c := component.GetAllGameConf[*conf.ConstantConf]()
	//初始化结构化kv数据源
	d := make([]util.TemplateKeyValueData, len(c), len(c))
	for i, code := range c {
		d[i] = util.TemplateKeyValueData{
			FieldName: code.Key,
			Values:    code.Key,
		}
	}
	//将数据源写入文件 生成kv结构文件
	util.JsonToKeyValueGoFile("constant", "constant_key", "../common/define/constant", "string", d)

}
```
上面的导出json和生成golang结构文件并没有什么特殊的地方, 只需要填入对应的路径即可, 后面的键值结构化文件生成,这里单独说一下,我们注意看一下代码
```Go
//设置配置源数据路径
	component.WithConfPath("../common/conf/js")
	//初始化游戏配置到内存中
	component.InitGameConfToMem()
	//获取配置数据
	codes := component.GetAllGameConf[*conf.ErrorCodeConf]()
	//初始化结构化kv数据源
	data := make([]util.TemplateKeyValueData, len(codes), len(codes))
	for i, code := range codes {
		data[i] = util.TemplateKeyValueData{
			FieldName: code.FieldName,
			Values:    code.Code,
		}
	}
	//将数据源写入文件 生成kv结构文件
	util.JsonToKeyValueGoFile("errorcodes", "error_code", "../common/define/errorcode", "int32", data)
```
我们可以看到,我们首先加载了配置文件,之后获取到我们需要结构化的配置列表,生成对应的`util.TemplateKeyValueData`结构数据,在这个结构数据中,`FieldName` 字段表示常量名,`Values`字段表示常量值,

我们继续看这行代码`JsonToKeyValueGoFile(packageName, fileName, outPath, fieldType string, data []TemplateKeyValueData)` 这里每个参数都有对应的描述,十分好理解.

最终我们就是依靠这些基础结构和脚本,生成我们所需要的结构化配置和js配置文件.

## 使用配置

我们提供了一套通过完整的配置相关操作的组件工具,开发人员只需要简单的调用,即可获取对应配置,而不需要去关注其他复杂的关系

```Go
c := rpc.NewRPCServer().
		WithService(&ExcelConfigService{}).
		WithGameConfig("../common/conf/js").
		WithRandomServicePort(8040, 8050).
		WithCache(tgf.CacheModuleClose).
		Run()
	<-c
```
首先,我们要保证在启动的service服务中对GameConfig的路径进行配置,这里的路基,为上面自动生成的json文件目录. `WithGameConfig("../common/conf/js").`

接着我们来看看,如何使用

```Go
	// 获取单个配置
	component.GetGameConf[*conf.PropConf]("100001")
	// 获取多个id相同的配置
	component.GetGameConfBySlice[*conf.PropConf]("100002")
	// 修改配置文件之后,调用该函数可以重新刷新配置,做到配置在线热更
	component.InitGameConfToMem()
	//通过键值对配置,生成自定义的常量结构,可以用于错误码,常量Key等配置
	log.Info("print error code %d", errorcodes.SystemErr)
	cs, _ := component.GetGameConf[*conf.ConstantConf](constant.MapLevelInitId)
	log.Info("print constant key %s, value %s", cs.Key, cs.Value)
```

我们可以看到,我们只需要传入需要获取的配置结构类型,还有它对应的主键id,即可获得对应的配置数据,并且我们还提供了实时刷新配置的接口,通过这个接口,我们可以实现动态热更配置的需求.

并且,我们也发现,前面配置文件生成的错误码常量结构和通用配置常量,也在这里出现,并且可以很方便的使用.

至此,配置文件相关工具的使用方式,就全部完成

[示例项目](https://github.com/thkhxm/tgf_example/tree/main/util-list/exceltojson)