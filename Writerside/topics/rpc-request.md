# 服务器请求
s2s rpc请求
## 代码范例

### RPC接口
```Go
func (s *service) GetBattleUserData(ctx context.Context, args *usermodels.LoadBattleUserRequest, reply *usermodels.LoadBattleUserResponse) (err error) {
    //do something
}
```
上述代码,展示了一个简单的rpc接口,在此之前,我们需要遵循[服务器协议](rpc-standard.md)的规范,,并且在业务节点的[service](startup-service.md)中声明这个函数即可.

### 调用接口

我们通过[生成协议接口](generate-rpc.md)工具得到服务器rpc协议请求的结构化代码. `user_service.RegisterUser`

我们模拟登陆成功之后,调用注册用户的rpc请求,以下是逻辑伪代码:
```Go
func (m *Manager) DoLogin(ctx context.Context, account, password string) (errorCode uint32, userId string) {
	accountModel, _ := m.accountDataManager.Get(account)

	defer func() {
		userId = accountModel.UserId
	}()

	if accountModel == nil || accountModel.Password != password {
		//errorCode = errorcodes.AccountNotFound
		log.DebugTag("login", "user login account not found")
		accountModel = &logic.AccountModel{
			Account:  account,
			Password: password,
			UserId:   util.GenerateSnowflakeId(),
		}
		m.accountDataManager.Set(accountModel, account)
		rpc.SendRPCMessage(ctx, userservice.RegisterUser.New(&usermodels.RegisterUserRequest{UserId: accountModel.UserId}, &rpc.EmptyReply{}))
		return
	}
	return
}
```

关注这一行代码
:
```Go
`rpc.SendRPCMessage(ctx, userservice.RegisterUser.New(&usermodels.RegisterUserRequest{UserId: accountModel.UserId}, &rpc.EmptyReply{}))`
```

使用`rpc.SendRPCMessage`函数发起了一次rpc请求,传入用户的`context`,和生成的rpc请求协议`userservice.RegisterUser`.

> 函数默认返回rpc请求的结果和error.可以根据返回结果处理后续逻辑

更多高级用法可以查看[RPC通讯方式](rpc-request-tutorial.md)文档



