---
id: deeplink
title: 深层链接（Deeplink）
---

深层链接是一项通过 URI 调用应用程序的技术，在不同的操作系统有不同的实现方式和名称

- [Android](https://developer.android.com/training/app-links/deep-linking?hl=zh-cn)
- [Windows (win32)](https://learn.microsoft.com/zh-cn/previous-versions/windows/internet-explorer/ie-developer/platform-apis/aa767914(v=vs.85))
- [Windows (UWP)](https://docs.microsoft.com/en-us/windows/uwp/launch-resume/launch-app-with-uri)

本项目使用`tuihub`作为 deeplink 的 scheme，当前支持的 deeplink 如下

## 连接至服务器

`tuihub://connect/{host, required}?port={port, default 443}&tls={tls, default true}`

- 当客户端未连接至服务器时，检查服务器状态并执行登录流程
- 当客户端已连接至服务器时，忽略或提示用户切换服务器
