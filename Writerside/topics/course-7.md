# 数据管理
    使用数据管理工具,对玩家数据进行自动化的缓存管理.

## 需求描述
    用户登录成功之后,我们需要根据用户的账号,创建用户的数据,并且将数据存放到mysql和redis中.之后我们通过接口,修改玩家的昵称,然后重新登录.观察数据是否准确

## 准备工作
    在开始服务器的代码编程之前,我们需要先创建对应的mysql表.我们需要用到账号,密码,用户id,昵称,还有统一的state数据状态字段(用于标记数据是否被删除)
    
## 代码
    展示了所需代码和操作

Mysql
: 使用`account`和`password`做为联合主键,`user_id`记录玩家id(除首字母外,遇到大写字母加下划线,所有字母都使用小写)

结构类
: 与mysql对应,字段需要大写,实现`db.IModel`接口
```Go
type UserAccount struct {
	db.Model //内置State字段,标识用户删除状态
	Account  string `orm:"pk"` //tag标记该字段为主键
	Password string `orm:"pk"`
	UserId   string
}
func (u *UserAccount) GetTableName() string {
	return "t_account"
}
```

数据管理
: 创建初始化数据管理
```Go
type Manager struct {
    //申明一个key为string类型,值为UserAccount的数据管理
	accountCache db.IAutoCacheService[string, *entity.UserAccount]
}
func (m *Manager) InitStruct() {
    //初始化一个带持久化缓存的数据管理实例,其中redis的主key为 account
    //假如,redis的key设计为"account:admin" 那么这里传入account
	m.accountCache = db.NewLongevityAutoCacheManager[string, *entity.UserAccount]("account")
}
```

逻辑修改
: 使用数据管理操作
```Go
m.accountCache.Get("admin") //这里可以获取到前面定义的泛型数据, 不需要再进行数据类型转换
m.accountCache.Set(account, "admin") //这里的account为数据实例,admin为唯一key
                                    //一般在创建数据的时候使用
m.accountCache.Push("admin") //这个函数是对数据进行持久化操作,
                             //会立即执行redis缓存,并且改变数据的状态,
                             //异步存储到mysql,一般在数据有变动的时候使用
```

## 总结
    总体来说,数据管理的使用十分简单. 只需要构建数据结构,遵循结构规范,之后实例化管理工具,
    就可以直接使用了.除了提供持久化结构数据以外,我们还提供自定义缓存机制的管理器,
    也就是说,所有的数据都可以使用这个工具管理他们的本地缓存和生命周期.
    后续也会根据需求,不断的拓展和优化.


> tgf系列基础课程到这里就结束了. 后续大家可以关注我的b站信息. 不定期的直播slg游戏开发.
> 有任何使用的问题和疑惑建议,都可以在github上提issues,或者加入qq群联系我.
> 
> 
> 开源不易,共同努力.


## 交流群
    QQ群:7400585

## 视频教程

[golang游戏服务器 - tgf系列课程07](https://www.bilibili.com/video/BV1Mp4y1f7bF/)  
[B站教程合集](https://space.bilibili.com/64497732/channel/seriesdetail?sid=3815364)

## 项目地址

[Github项目地址](https://github.com/thkhxm/tgf)  
[Gitee项目地址](https://gitee.com/timgame/tgf)  
[项目案例](https://github.com/thkhxm/tgf-tutorial)  
[项目文档](https://thkhxm.github.io/tgf_writerside/starter-topic.html)  
[知乎博客](https://www.zhihu.com/people/tim-30-83/posts)  
[CSDN专栏](https://blog.csdn.net/thkhxm/category_12520142.html)  