## provider.webhook

PostPoint 发起 Webhook 请求，收到 HTTP 200 响应，表示请求已成功；

其他 HTTP 状态码，表示错误请求，例如: "HTTP 400 Bad Request", "HTTP 404 Not Found", "405 Method Not Allowed"

### json 提交

config.toml 配置

```toml
[provider.webhook]
# webhook 地址
endpoint.url = "http://localhost/xxxxxx"
# webhook http method，可选用一个: GET、POST、PUT、PATCH、DELETE，默认=POST
endpoint.method = "POST"
# 可选用一个: json、form，默认=json
content_type = "json"
```

PostPoint 发起请求，内容：今天我要做俯卧撑！……今天先俯卧，明天再撑。

```
POST /xxxxxx
Content-Type: application/json

{"text": "今天我要做俯卧撑！……今天先俯卧，明天再撑。"}
```

Webhook 响应

```
HTTP/1.1 200 OK
Content-Type: text/plain; charset=utf-8

ok
```

### form 提交

config.toml 配置

```toml
[provider.webhook]
# webhook 地址
endpoint.url = "http://localhost/xxxxxx"
# webhook http method，可选用一个: GET、POST、PUT、PATCH、DELETE，默认=POST
endpoint.method = "GET"
# 可选用一个: json、form，默认=json
content_type = "form"
```

PostPoint 发起请求，内容：今天我要做俯卧撑！……今天先俯卧，明天再撑。

```
GET /xxxxxx
Content-Type: application/x-www-form-urlencoded

text=%E4%BB%8A%E5%A4%A9%E6%88%91%E8%A6%81%E5%81%9A%E4%BF%AF%E5%8D%A7%E6%92%91%EF%BC%81%E2%80%A6%E2%80%A6%E4%BB%8A%E5%A4%A9%E5%85%88%E4%BF%AF%E5%8D%A7%EF%BC%8C%E6%98%8E%E5%A4%A9%E5%86%8D%E6%92%91%E3%80%82
```

Webhook 响应

```
HTTP/1.1 200 OK
Content-Type: text/plain; charset=utf-8

ok
```