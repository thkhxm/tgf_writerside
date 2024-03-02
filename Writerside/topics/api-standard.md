# 客户端协议
    c2s的接口规范

## 代码示例

```Go
type ILoginService interface {
	Login(ctx context.Context, args *rpc.Args[*pb.LoginReq], reply *rpc.Reply[*pb.LoginRes]) (err error)
}
```
## 结构说明

固定的格式
: `(ctx context.Context, args *rpc.Args[*A], reply *rpc.Reply[*R]) (err error)`
: 其中A为客户端的请求参数,R为回调给客户端的结果
: 需要注意的是,入参类型必须为Proto结构数据.

当我们接口定义完成之后,可以使用框架内置的工具,转换成标准的协议接口类. [文档](generate-rpc.md)

