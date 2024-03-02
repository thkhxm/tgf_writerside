# 服务器协议

    s2s的接口规范

## 代码示例

```Go
type IUserRPCService interface {
	GetUserData(ctx context.Context, args *usermodels.LoadLoginUserRequest, reply *usermodels.LoadLoginUserResponse) (err error)
	RegisterUser(ctx context.Context, args *usermodels.RegisterUserRequest, reply *rpc.EmptyReply) (err error)
}
```

## 结构说明

固定的格式
: `(ctx context.Context, args *A, reply *R) (err error)`
: 其中A为请求的参数,R为响应的结果
: 需要注意的是,入参类型必须为引用类型,且不可为接口.

当我们接口定义完成之后,可以使用框架内置的工具,转换成标准的协议接口类. [文档](generate-rpc.md)