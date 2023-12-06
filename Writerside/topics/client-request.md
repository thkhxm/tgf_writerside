# 客户端请求
 c2s的请求文档

## 代码范例
### 注册接口
```Go
func (s *service) BuyItem(ctx context.Context, args *rpc.Args[*pb.BuyItemReq], reply *rpc.Reply[*pb.BuyItemRes]) (err error) {
    //获取用户的userId
    userId =: rpc.GetUserId(ctx)
	//do something
	return
}
```
上面是一个简单的客户端请求协议,开发人员无需对协议进行注册或者导入.只需要遵循[客户端协议](api-standard.md)的规范,并且在业务节点的[service](startup-service.md)中声明这个函数即可

### 调试接口
框架提供了完整的机器人工具,可以使用[机器人](robot-tutorial.md)进行客户端的模拟请求和调试.