# PostPoint

通过 PostPoint 的统一 API，轻松实现发送消息到企业微信群机器人、飞书自定义机器人、钉钉自定义机器人、Slack机器人、Webhook，无需单独开发，立即开始自动化业务通知！

帮助你快速构建消息推送系统，可设置**失败重试**，遵守各平台消息**调用频率**。

## 支持的操作系统

* Windows
* Linux
* macOS
* FreeBSD

## 上手指南

```shell
C:\>postpoint.exe -h
一个 API 请求将消息发送到企业微信、飞书、钉钉、Slack、Webhook

Usage:
  postpoint [command]

Available Commands:
  help        Help about any command
  serve       API / OpenAPI / Swagger UI 服务，消息推送服务
  test        测试已配置的企业微信群机器人、飞书自定义机器人、钉钉自定义机器人、Slack机器人、Webhook

Flags:
  -h, --help      help for postpoint
  -v, --version   version for postpoint

Use "postpoint [command] --help" for more information about a command.
```

### 安装`PostPoint`

`PostPoint`下载 [最新版本](https://github.com/lenye/postpoint/releases/tag/v25.7.6-beta3)

样例配置文件 [config.toml](config.toml)

#### windows 操作系统

1. 解压下载文件`postpoint_v25.7.6-beta3_windows_x86_64.zip`；
2. 创建`config.toml`配置文件，保存到`postpoint.exe`相同目录下，配置一个新通道：**企业微信群机器人**；
    ```toml
    # 企业微信群机器人
    [provider.workweixin_bot]
    # webhook 地址
    endpoint.url = "https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key=xxx"
    ```
   endpoint.url 填写成你的企业微信群机器人 webhook 地址
3. 运行`postpoint.exe test`，测试发送消息到配置的通道；
   ```shell
   C:\>postpoint.exe test
   2025-07-01T22:07:01.788+0800    info    测试    {"text": "postpoint 测试消息\n\n就到这儿吧！我要去睡今天的第三个午觉了。"}
   2025-07-01T22:07:02.081+0800    info    workweixin_bot    成功    {"耗时": "482.784ms"}
   ```
4. 运行`postpoint.exe serve`，开始消息推送 API 服务；
   ```shell
   C:\>postpoint.exe serve
   2025-07-01T22:07:01.627+0800    info    PostPoint Free v25.7.6-beta3 windows/amd64, https://github.com/lenye/postpoint
   2025-07-01T22:07:01.667+0800    info    url    {"API": "http://localhost:39270/text", "Swagger UI": "http://localhost:39270/swagger/", "OpenAPI": "http://localhost:39270/swagger/openapi.yaml"}
   ```   

运行`postpoint.exe test -h`查看通道的测试命令。

单独测试企业微信群机器人，运行`postpoint.exe test workweixin_bot`。

#### 完成

如果你顺利完成了以上步骤，那么恭喜你，属于你的`PostPoint`搭建成功。

## 调用 API 发送消息

API 请求和响应数据编码皆为 UTF-8 格式，发送消息使用 HTTP POST 请求。

数据的提交方式：

1. json 提交：application/json
2. form 提交：application/x-www-form-urlencoded

使用 API 发送消息，发出如下所示的 HTTP POST 请求：

```
POST /text
Content-type: application/json

{
    "msg": "测试，测试，测试"
}
```

收到 HTTP 200 响应，表示消息已成功发布；

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "code": "ok",
    "id": "1234567EC64G97ZRAS211JHHX7"
}
```

其他 HTTP 状态码，表示错误请求，例如: "HTTP 400 Bad Request", "HTTP 404 Not Found", "405 Method Not Allowed"

```
HTTP/1.1 400 Bad Request
Content-Type: application/json; charset=utf-8

{
    "code": "invalid_argument",
    "msg": "msg 是必填项",
    "id": "1234567EC64G97ZRAS211JHHX8"
}
```

### 使用 Swagger UI

`PostPoint`OpenAPI http://localhost:39270/swagger/openapi.yaml

你可以访问 Swagger UI 调用 API 发送消息，http://localhost:39270/swagger/

可以使用代码生成器 [Swagger Codegen](https://swagger.io/tools/swagger-codegen/)，根据 OpenAPI 规范自动生成各种语言的客户端
SDK。

### 使用 curl

可以使用 curl 命令转代码（复制 linux 环境的命令到转码工具），自动生成各种语言的客户端 SDK。

下面是使用 curl 发送一个消息的示例，消息内容：测试，测试，测试

1. json 提交
    ```shell
    # linux 环境
    $ curl -X 'POST' 'http://localhost:39270/text' \
      -H 'Accept: application/json' \
      -H 'Content-Type: application/json' \
      -d '{"msg": "测试，测试，测试"}'
   
   
    # windows 环境
    C:\>curl -X "POST" "http://localhost:39270/text" ^
      -H "Accept: application/json" ^
      -H "Content-Type: application/json" ^
      -d "{\"msg\": \"测试，测试，测试\"}"         
    ```
2. form 提交
    ```shell
    # linux 环境
    $ curl -X 'POST' 'http://localhost:39270/text' \
      -H 'Accept: application/json' \
      -H 'Content-Type: application/x-www-form-urlencoded' \
      -d 'msg=测试，测试，测试'
   
   
    # windows 环境
    C:\>curl -X "POST" "http://localhost:39270/text" ^
      -H "Accept: application/json" ^
      -H "Content-Type: application/x-www-form-urlencoded" ^
      -d "msg=测试，测试，测试"
    ```

日志

```shell
2025-07-01T22:07:55.496+0800    info    api    新消息    {"id": "1234567EC64G97ZRAS211JHHX7", "msg": "测试，测试，测试"}
2025-07-01T22:07:55.837+0800    info    delivery.workweixin_bot    成功    {"id": "1234567EC64G97ZRAS211JHHX7", "span_id": "1.1", "耗时": "339.7873ms"}
```

## 贡献

欢迎创建 [Issue](https://github.com/lenye/postpoint/issues) 来帮助改进项目。

## 免责声明

本软件仅供学习和研究之用，使用此软件的风险由你自行承担，我们对使用该软件时产生的任何损失概不负责。