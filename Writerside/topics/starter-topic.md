# About tgf

## 项目介绍

​	tgf框架是使用golang开发的一套游戏分布式框架,支持全球一服.项目采用了rpcx做为底层rpc的通讯,consul提供服务注册发现.定义了一整套的模块开发规范.[开发文档](https://github.com/thkhxm/tgf/tree/develop/doc)

## 技术选型

**go1.21.1**

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

![image-20230228031100624](http://oss.yamigame.net/picgo/image-20230228031100624.png)