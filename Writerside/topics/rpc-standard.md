# 服务器协议

    s2s的接口规范

## 代码示例

```Go
type IUserRPCService interface {
	GetUserData(ctx context.Context, args *user_param.LoadLoginUserArgs, reply *user_param.LoadLoginUserReply) (err error)
}
```

## 命名规范

    接口参数的命名, 一般使用Args结尾来表示入参,Reply结尾表示响应结果. rpc相关的参数,我们会在param文件夹中创建.

## 结构说明

固定的格式
: `(ctx context.Context, args *A, reply *R) (err error)`
: 其中A为请求的参数,R为响应的结果
: 需要注意的是,入参类型必须为引用类型,且不可为接口.

当我们接口定义完成之后,可以使用框架内置的工具,转换成标准的协议接口类. [文档](generate-rpc.md)