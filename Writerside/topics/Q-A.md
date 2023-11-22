# Q&amp;A

挂载vpn代理
: 一般发生在mac系统,服务注册到consul的时候,ip可能会获取异常,导致请求不可达,这时候退出vpn,重启服务即可解决

rpcx.Register: type dungeon has no exported methods of suitable type
: 这是因为您的service没有可用的接口导致,只需要定义一个空的接口即可.