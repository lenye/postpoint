# postpoint

一个 API 请求将消息发送到企业微信、飞书、钉钉。

帮助你快速构建**消息推送**系统，发送`企业微信群机器人`、`飞书自定义机器人`、`钉钉自定义机器人`消息，
可设置**失败重试**，遵守各平台消息**频率限制**。

## 上手指南

请参考 [快速上手指南](docs/快速上手指南.md)

```shell
C:\>postpoint.exe -h
一个 API 请求将消息发送到企业微信、飞书、钉钉

Usage:
  postpoint [command]

Available Commands:
  help        Help about any command
  serve       消息推送 API 服务
  test        测试配置的企业微信群机器人，飞书自定义机器人，钉钉自定义机器人

Flags:
  -h, --help      help for postpoint
  -v, --version   version for postpoint

Use "postpoint [command] --help" for more information about a command.
```

## 支持的操作系统

* Windows
* Linux
* macOS
* FreeBSD

## 贡献

欢迎创建 [Issue](https://github.com/lenye/postpoint/issues) 来帮助改进项目。
