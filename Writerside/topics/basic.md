# 基础教程

> 下面我们会一步一步构建一个相对完整的项目
> 
> [示例项目](https://github.com/thkhxm/tgf_example/tree/main/rpc-start)

## 项目结构
一个标准项目中我们通常会包含以下几个包,我们会分别介绍他们的功能

common
: 主要存放自动生成的代码文件,pb,项目通用结构参数,通用错误码,通用异常,rpc和api接口等,我们通常会在这个项目中对tgf框架进行引入,这样在整个work环境中,就无需多次引入了.

kit
: 工具项目,主要负责存放pb执行相关文件脚本,api,rpc,excel导出json,配置文件导出 等自动生成脚本,或者文档和sql脚本等.

service
: 业务逻辑服务项目,项目名称根据业务而定. 主要是业务核心逻辑,对外只提供少量接口,逻辑在internal路径下进行开发

## 开发流程

- [准备工作](Build-workspace.md)
- [启动一个网关](startup-gate.md)
- [启动一个服务](startup-service.md)
  - [生成协议接口](generate-rpc.md)
- [机器人自测](robot-tutorial.md)

完整调用流程图

![basic3](basic_3.png)


> 如果需要完整代码,可以在示例项目中查看.