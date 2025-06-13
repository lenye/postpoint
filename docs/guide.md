## 快速上手指南

`postpoint`可通过一个 API 请求将消息发送到企业微信、飞书、钉钉的系统。

帮助你快速构建`消息推送`系统，发送`企业微信群机器人`、`钉钉自定义机器人`、`飞书自定义机器人`消息，可设置**失败重试**
机制，遵守各平台消息**频率限制**。

## 安装`postpoint`

`postpoint`下载 [最新版本](https://github.com/lenye/postpoint/releases)

配置文件下载 [config.toml](https://github.com/lenye/postpoint/tree/main/config.toml)

**windows 系统**

1. 解压下载文件`postpoint_v26.6.1_windows_x86_64.zip`；
2. 创建`config.toml`配置文件，保存到`postpoint.exe`相同目录下，配置**机器人参数**；
3. 运行`postpoint.exe test`，测试配置是否正确；
4. 运行`postpoint.exe serve`，开始消息推送 API 服务；

### 支持的操作系统

* Windows
* Linux
* macOS
* FreeBSD

## 完成！调用 API 发送消息

如果你顺利完成了以上步骤，那么恭喜你，属于你的`postpoint`搭建成功，你可以访问 **swagger** 调用 **API**
推送消息，http://localhost:39270/swagger/
