# 路由教程
    路由功能介绍

## 源码 
代码所在位置:[rpc/plugins.go](https://github.com/thkhxm/tgf/blob/master/rpc/plugins.go)
```Go
func (c *CustomSelector) Select(ctx context.Context, servicePath, serviceMethod string, args interface{}) (selected string) {
	if sc, ok := ctx.(*share.Context); ok {
		size := c.servers.Len()
		switch size {
		case 0:
			return ""
		default:
			reqMetaData := sc.Value(share.ReqMetaDataKey).(map[string]string)
			selected = reqMetaData[servicePath]
			//用户级别的请求
			uid := reqMetaData[tgf.ContextKeyUserId]
			rpcTip := reqMetaData[tgf.ContextKeyRPCType]
			broadcasts := make([]string, 1)

			if uid != "" {
				broadcasts[0] = uid
			}
			var bindNode bool
			if rpcTip == tgf.RPCBroadcastTip {
				ids := reqMetaData[tgf.ContextKeyBroadcastUserIds]
				broadcasts = strings.Split(ids, ",")
				if !c.checkServerAlive(selected) {
					key := client2.HashString(fmt.Sprintf("%v", time.Now().UnixNano()))
					selected, _ = c.h.Get(key).(string)
				}
				bindNode = true
			}
			if len(broadcasts) > 0 && broadcasts[0] != "" {
				for _, uid := range broadcasts {
					var key uint64
					//先判断携带节点信息是否存活
					if c.checkServerAlive(selected) {
						if bindNode {
							c.processNode(ctx, uid, selected, reqMetaData, servicePath)
						}
						continue
					}

					//从本地缓存中获取用户的节点数据
					selected, _ = c.cacheManager.Get(uid)
					if c.checkServerAlive(selected) {
						continue
					}
					//如果上面的用户节点获取，没有命中，那么取当前请求模式
					//如果是rpc推送请求，
					//if rpcTip == tgf.RPCTip {
					//从数据缓存中获取用户的节点数据
					reqMetaDataKey := fmt.Sprintf(tgf.RedisKeyUserNodeMeta, uid)
					reqMetaCacheData, suc := db.GetMap[string, string](reqMetaDataKey)
					if !suc {
						reqMetaCacheData = make(map[string]string)
					}
					selected = reqMetaCacheData[servicePath]
					if c.checkServerAlive(selected) {
						//将节点数据，放入本地缓存
						if reqMetaData[tgf.ContextKeyCloseLocalCache] == "" {
							c.cacheManager.Set(uid, selected)
						}
						continue
					} else {
						//通过一致性hash的方式,命中一个活跃的业务节点
						key = client2.HashString(uid)
						selected, _ = c.h.Get(key).(string)
						reqMetaData[servicePath] = selected
						c.processNode(ctx, uid, selected, reqMetaData, servicePath)
					}
				}
			} else {
				if c.checkServerAlive(selected) {
					//key := client2.HashString(fmt.Sprintf("%v", time.Now().Unix()))
					//selected, _ = c.h.Get(key).(string)
					return
				}
				key := client2.HashString(fmt.Sprintf("%v", time.Now().UnixNano()))
				selected, _ = c.h.Get(key).(string)
			}
			return
		}
	}

	return ""
}
func (c *CustomSelector) processNode(ctx context.Context, uid string, selected string, reqMetaData map[string]string, servicePath string) {
	reqMetaDataKeyTemp := fmt.Sprintf(tgf.RedisKeyUserNodeMeta, uid)
	db.PutMap(reqMetaDataKeyTemp, servicePath, selected, reqMetaDataTimeout)
	if reqMetaData[tgf.ContextKeyCloseLocalCache] == "" {
		c.cacheManager.Set(uid, selected)
	}
	if UploadUserNodeInfo.ModuleName != servicePath {
		util.Go(func() {
			if _, err := SendRPCMessage(ctx, UploadUserNodeInfo.New(&UploadUserNodeInfoReq{
				UserId:      uid,
				NodeId:      selected,
				ServicePath: servicePath,
			}, &UploadUserNodeInfoRes{ErrorCode: 0})); err != nil {
				log.Warn("[rpc] 节点更新异常 %v", err)
			}
		})
	}
}
```
{collapsible="true"}
