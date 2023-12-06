# 通讯方式
通讯方式

## 概述
框架提供多种rpc请求方式,为我们的业务提供支撑.所有的函数都在rpc包下.可以任何地方通过`rpc.*`的方式发起请求.当然也有针对用户的推送方式

## 函数详解

- [RPC请求](#rpc-tutorial-rpc)
- [用户请求](#rpc-tutorial-user)

### RPC请求 {id="rpc-tutorial-rpc"}

SendRPCMessage
: 通用远程rpc调用,请求阻塞,等待返回结果.

SendAsyncRPCMessage
: 异步rpc请求,请求非阻塞,返回callback channel,可以通过该结构异步获取到调用结果.
: 使用该接口时,需要确保call中的chan被消费, 避免chan的泄露

SendNoReplyRPCMessage
: 异步rpc请求,请求非阻塞,无返回结果
: 如果你不关注请求的结果,可以使用该函数

BorderRPCMessage
: 推送消息到所有服务节点,请求阻塞,无返回结果,如果任意一个节点的请求异常,会立马返回
: 这里的所有服务节点,表示的是相同模块节点,例如有user和login两个业务节点,我们现在启动了四个服务,其中有三个user业务服务节点,和一个login
业务节点,这时候我们使用该函数广播一条user的rpc请求消息,这时候会命中这三个user的业务节点.不包含login节点.

BorderAllServiceRPCMessageByContext
: 推送消息到所有服务节点,请求非阻塞,无返回结果
: 这里我们会请求所有的业务节点,我们再次以为`BorderRPCMessage`中的例子说明如果这时候使用`BorderAllServiceRPCMessageByContext`
函数广播一条rpc请求消息的话,会命中四个服务节点.因为我们是根据`methodName`来进行节点中的函数命中,所以假如我们在user和login的业务协议中都有一个`LoadData`
的协议接口的话,那么他们都会被调用.

### 用户请求 {id="rpc-tutorial-user"}

SendToGate
: 发送消息到用户所在的网关,请求非阻塞,无返回结果
: context参数可以参考[Context教程](context-tutorial.md#context-tutorial-func)获取,或者传入api接口函数中的`ctx`参数