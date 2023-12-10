# 数据管理
    数据管理器,提供了托管式的数据管理和常规数据操作功能

## 概述
    我们在日常开发中,经常需要对业务数据进行缓存. 框架提供了这方面需求的工具类.
    我们可以通过简单的Get,Set,Push,Remove等函数对数据进行常规操作.
    无需关心数据的存储和清除.
    
## 数据托管
    将数据放入托管中,根据实例化托管结构的参数,实现自动化管理.
    数据托管分有三级缓存机制.
    本地缓存,redis二级缓存,mysql冷数据储存
    不仅如此,我们提供了两种结构的缓存

常规结构
: 常规结构,一个key对应一个结构数据.通过主键存储数据

数组结构
: 数组结构, 数据操作时,需要传入一个主key+n个辅key, 数据类似redis的HSet,HGet操作.
: 通过主键和辅key组合使用

> 我们会在后面详细介绍这两种方式

{style="note"}

### 创建数据托管

默认创建函数

| 函数 | 本地缓存 | redis缓存 | mysql储存 |
|----|------|---------|---------|
|NewDefaultAutoCacheManager    | 支持   |    支持     | 不支持     |
|NewLongevityAutoCacheManager    |  支持    |    支持     | 支持      |
|NewAutoCacheManager    |   支持   |     不支持    |   不支持      |

自定义创建函数

{style="wide"}
NewAutoCacheBuilder()
: 创建一个常规结构的数据构造器

NewHashAutoCacheBuilder()
: 创建一个哈希结构的数据构造器

## 常规数据操作
    我们可以使用db包下的基础函数,操作redis,提供了Value,Map,List结构的数据操作函数.

```Go
    userKey := "user:123"
    data := db.Get[*model.UserData](userKey)
    log.DebugTag("user","user name %s",data.GetName())
    data.SetName("hello")
    db.Set(userKey,data,time.Hour*24)
```

上面是一段简单的Value数据操作,其他更多操作可以查看后面的文档.

