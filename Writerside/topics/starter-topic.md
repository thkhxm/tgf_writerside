# 关于 tgf

> tgf框架是使用golang开发的一套游戏分布式框架.
> 
> 项目采用了[rpcx](https://github.com/smallnest/rpcx )做为底层rpc的通讯,通过consul提供服务注册发现.
> 
> 提供了一整套开发工具,并且定义了模块开发规范.

[示例项目](https://github.com/thkhxm/tgf_example.git)

## 技术选型

Golang开发版本:  **1.21.1**

| 技术       | 说明           | 仓库地址                                 |
| ---------- | -------------- | ---------------------------------------- |
| rpcx       | 底层rpc的实现  | https://github.com/smallnest/rpcx        |
| redis      | 提供数据缓存   | https://redis.io/                        |
| hashmap    | 线程安全的集合 | https://github.com/cornelk/hashmap       |
| ants       | 高性能go协程池 | https://github.com/panjf2000/ants        |
| redislock  | 分布式redis锁  | https://github.com/bsm/redislock         |
| snowflake  | 雪花算法       | https://github.com/bwmarrin/snowflake    |
| doublejump | 一致性hash     | https://github.com/edwingeng/doublejump  |
| godotenv   | 环境变量工具   | https://github.com/joho/godotenv         |
| zap        | 日志框架       | https://go.uber.org/zap                  |
| lumberjack | 日志切割工具   | https://gopkg.in/natefinch/lumberjack.v2 |
| excelize   | Excel工具      | https://github.com/qax-os/excelize       |
| sonic      | json高性能工具 | https://github.com/bytedance/sonic/      |

## 基础架构图
![img](jiagoutu.png)