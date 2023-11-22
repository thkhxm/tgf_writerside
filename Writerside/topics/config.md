# 配置文件
配置文件方便我们在不同的环境下,使用不同的配置进行开发.框架在启动的时候,会先读取全局的环境变量,如果没有的话会读取配置文件,最后才是取框架默认值.我们可以看看下面完整的配置文件

```Text
#全局配置环境读取,取TGFMODULE常量,如果环境变量中不存在,默认会取dev的配置
#可以在启动的时候传入该常量,动态运行不同环境的配置文件
#示例:go run main.go --env TGFMODULE=dev

#日志输出路径
LogPath=./log
#日志最低输出级别
LogLevel=info
#日志忽略标签
LogIgnoredTags=trace,login

#运行环境,有以下可选运行环境 dev test release
RuntimeModule=dev

#consul地址
ConsulAddress=127.0.0.1
#consul路径,默认使用/tgf,如需区分不同环境可以使用自定义的不同的路径 例如 /test 或者 /dev /tim
ConsulPath=/tgf

#redis地址 127.0.0.1:6379
RedisAddr=127.0.0.1:6379
#redis密码
RedisPassword=123456
#redis的db
RedisDB=1

#mysql用户名
MySqlUser=root
#mysql密码
MySqlPwd=123456
#mysql地址
MySqlAddr=127.0.0.1
#mysql端口
MySqlPort=3306
#mysql库名
MySqlDB=tgf_game

#当前服务提供的服务端口
ServicePort=8021
#绑定自定义服务地址
ServiceAddress=0.0.0.0
```
每一个配置项目都做了详细的描述,这里需要额外说的一点是,我们一般会根据当前的环境为这个文件命名.例如默认的开发环境为.env.dev 这里的 `.env`为默认名,后面的`.dev`为环境名,我们通过修改这里进行不同环境的配置文件读取
