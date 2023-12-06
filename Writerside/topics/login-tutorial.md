# 用户登录

为什么要有登录行为
: 在框架中,我们在处理客户端的请求都需要验证用户context上下文中的UserId是否存在.
: 只有调用了登录接口,才能拥有非白名单接口请求的权限.

框架提供了用户登录的接口,开发人员在对登录的行为进行自定义的验证和判断之后,只需要调用框架提供的登录协议即可完成用户的登录行为.  
下面展示一段伪代码：
```Go
func (s *service) Login(ctx context.Context, args *rpc.Args[*pb.LoginReq], reply *rpc.Reply[*pb.LoginRes]) (err error) {
	var (
		//将字节数组转换为pb数据
		req = args.GetData()
		res = &pb.LoginRes{}
	)
	defer func() {
		reply.SetData(res)
	}()
	//执行登录逻辑，如果登录成功返回用户userId
	res.Error, res.UserId = s.m.DoLogin(ctx, req.Account, req.Password)
	//登录成功，调用网关同步用户userId
	if res.Error == 0 {
		r, e := rpc.SendRPCMessage(ctx, rpc.Login.New(&rpc.LoginReq{
			UserId:         res.UserId,
			TemplateUserId: rpc.GetTemplateUserId(ctx),
		}, &rpc.LoginRes{}))
		if e != nil {
			log.DebugTag("login", "user login fail account=%v password=%v code=%v ", req.Account, req.Password, r.ErrorCode)
			res.Error = uint32(r.ErrorCode)
			return
		}
	}
	log.InfoTag("login", "user login account=%v password=%v userId=%v", req.Account, req.Password, res.UserId)
	return
}
```

我们需要关注这一行代码  
`rpc.SendRPCMessage(ctx, rpc.Login.New(&rpc.LoginReq{ UserId:res.UserId, TemplateUserId: rpc.GetTemplateUserId(ctx), }, &rpc.LoginRes{}))`  
这里我们使用rpc的方式发送了Login接口到网关服务中,我们会绑定用户的userId到context上下文中.在后续的业务接口请求中,可以通过`rpc.GetUserId(ctx)`函数获取到用户的UserId.




