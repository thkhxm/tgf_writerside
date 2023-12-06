# 服务器请求
s2s rpc请求
## 代码范例

### 注册一个接口
```Go
func (s *service) GetBattleUserData(ctx context.Context, args *usermodels.LoadBattleUserRequest, reply *usermodels.LoadBattleUserResponse) (err error) {
    //do something
}
```
上述代码,展示了一个简单的rpc接口,在此之前,我们需要遵循[服务器协议](rpc-standard.md)的规范,,并且在业务节点的[service](startup-service.md)中声明这个函数即可.

### 调用接口

我们通过[生成协议接口](generate-rpc.md)工具得到服务器rpc协议请求的结构化代码. `userservice.RegisterUser`

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

我们需要关注这一行代码:`rpc.SendRPCMessage(ctx, userservice.RegisterUser.New(&usermodels.RegisterUserRequest{UserId: accountModel.UserId}, &rpc.EmptyReply{}))`






