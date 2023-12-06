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
: `(ctx context.Context, args *rpc.Args[*pb.LoginReq], reply *rpc.Reply[*pb.LoginRes]) (err error)`
: 不可修改参数数量和类型.


自定义参数
: `[*pb.LoginReq]` 表示客户端的请求参数
: `[*pb.LoginRes]` 表示回调给客户端的参数
: 上面这两个结构都是可变参数,可以根据自身业务需求替换为所需的结构
: 注意,这里的泛型参数,都需要使用*, 例如 [*pb.LoginReq] ,  bad case [pb.LoginReq]
: 我们可以根据自身的客户端协议需求,修改其中的泛型`*pb.LoginReq`和`*pb.LoginRes`,他们分别`LoginReq`和我们`LoginRes`.
: 客户端的请求协议参数,需要为protobuf数据.
 

当我们接口定义完成之后,可以使用框架内置的工具,转换成标准的协议接口类. [文档](generate-rpc.md)

