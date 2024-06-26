---
id: protocol
title: 协议
---

:::info
插件 SDK 封装了协议细节，插件开发者按照 SDK 文档进行开发即可。
:::

主服务（`Sephirah`）与插件（`Porter`）采取双向通信，默认插件可信并且处于可信网络内。通过 consul 服务发现机制获取实例列表。

## 建立连接

1. `Sephirah` 设置 `Porter` 类型的特殊用户
1. 每个 `Porter` 实现应当有自己硬编码的`PorterName`（字符串），`Porter` 启动前由服务器管理员分配一个`PorterID`，`PorterName`相同视为源代码相同，`PorterID`不允许重复，不支持不同版本的 `Porter` 同时存在
1. `Porter` 注册实例时将`PorterName`和`PorterID`设为[node meta](https://developer.hashicorp.com/consul/docs/agent/config/cli-flags#_node_meta)
1. `Sephirah` 定时从服务发现获取 `Porter` 实例列表，遍历实例调用信息获取接口，将有效实例显示在管理员面板上
1. 管理员根据信息确认是否启用新 `Porter`
1. 管理员确认后服务端调用 `Porter` 启用接口分配 accessToken 和 refreshToken
1. `Porter` 应自行维护 accessToken，过期后可以要求 `Sephirah` 重新分配
1. 在之后的调用过程中
   - `Porter` 的信息获取接口中包含其支持的接口列表，`Sephirah` 根据列表在需要时调用 `Porter` 的接口
   - `Porter` 能够调用特定接口获取用户模拟 token，该 token 应包含 `Porter` 和用户的信息，能够以该用户的身份调用接口，必要时与真实用户调用做出不同的响应逻辑
   - 非用户模拟状态限制可调用接口

## 识别功能

`Porter` 能够扩展的功能由接口定义硬编码，`Sephirah` 通过调用接口获取功能列表，功能的关键字段包括：

- `id`：功能在对应接口中的唯一标识，不同功能接口中的 id 可以相同
- `region`：功能所属的可用区，`Sephirah` 会在同一可用区内随机选择一个 `Porter` 实例调用
- `config_json_schema` 和 `config_json`：功能的自定义字段，`Porter` 通过 [JSON Schema](https://json-schema.org/) 定义功能的配置字段，客户端构造相应的 UI 以便用户填写配置，之后将配置序列化为 JSON 字符串传递给 `Porter`
