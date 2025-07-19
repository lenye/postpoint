## Webhook

Webhook 允许将 PostPoint 收到的消息转发到任何支持 HTTP 请求的自定义系统，例如你自己的内部 API、自动化工作流（如Zapier/n8n）或日志聚合服务。

### 工作原理

1. 当 PostPoint API 收到一条发往 Webhook 渠道的消息时。
2. PostPoint 会根据你的配置，构造一个 HTTP 请求。
3. PostPoint 将此 HTTP 请求发送到你指定的 `endpoint.url`。
4. 你的 Webhook 服务器需要返回 `HTTP 200 OK` 状态码，PostPoint 才认为本次推送成功。

### 配置 (`config.toml`)

可以在配置文件中定义 Webhook 的行为。

```toml
# Webhook Provider 配置段
[provider.webhook]

# [必填] Webhook 服务器地址。
endpoint.url = "https://your-service.com/webhook"

# [可选] HTTP 请求方法。
# 支持: "GET", "POST", "PUT", "PATCH", "DELETE"。
# 默认值: "POST"
endpoint.method = "POST"

# [可选] 发送数据时使用的 Content-Type。
# 支持: "json" (application/json) 或 "form" (application/x-www-form-urlencoded)。
# 当 method = "GET" 时，此选项无效，数据将以 URL 查询参数形式发送。
# 默认值: "json"
content_type = "json"

# [可选] 自定义 HTTP 请求头。
# 可用于身份验证，例如传递一个固定的 Api-Key。
[provider.webhook.headers]
X-Api-Key = "your-secret-auth-token"
X-Source = "PostPoint"
```

### 请求负载 (Payload)

PostPoint 在调用 Webhook 时，会发送一个包含消息上下文的结构化数据。

#### 负载结构

| 字段名        | 类型     | 描述                     |
|:-----------|:-------|:-----------------------|
| `id`       | string | 原始 API 请求的唯一 ID，可用于追踪。 |
| `msg_type` | string | 原始消息类型 (`text`)。       |
| `msg`      | string | 原始消息内容。                |

#### 数据格式

* 当 `content_type = "json"` 时，PostPoint 会发送一个 json 对象作为请求体。
* 当 `content_type = "form"` 时，PostPoint 会将上述字段作为表单数据在请求体中发送。
* 当 `endpoint.method = "GET"` 时，PostPoint 会将上述字段作为 URL 查询参数附加到 `endpoint.url` 之后。

### 示例场景

假设原始 API 收到的消息为：`{"msg": "今天我要做俯卧撑！……今天先俯卧，明天再撑。"}`；
原始 API 请求的唯一 ID：`{"id": "1234567EC64G97ZRAS211JHHX7"}`。

#### 场景 1: 使用 `POST` 发送 `json` 数据

配置:

```toml
[provider.webhook]
endpoint.url = "https://my-system.com/alerts"
endpoint.method = "POST"
content_type = "json"

[provider.webhook.headers]
X-Api-Key = "your-secret-auth-token"
```

PostPoint 发出的请求:

```http
POST /alerts
Host: my-system.com
Content-Type: application/json
X-Api-Key: your-secret-auth-token

{
    "id": "1234567EC64G97ZRAS211JHHX7",
    "msg": "今天我要做俯卧撑！……今天先俯卧，明天再撑。",
    "msg_type": "text"
}
```

你的服务器应响应:

```http
HTTP/1.1 200 OK
Content-Type: text/plain

ok
```

#### 场景 2: 使用 `POST` 发送 `form` 数据

配置:

```toml
[provider.webhook]
endpoint.url = "https://my-system.com/alerts"
endpoint.method = "POST"
content_type = "form"
```

PostPoint 发出的请求:

```http
POST /alerts
Host: my-system.com
Content-Type: application/x-www-form-urlencoded

id=1234567EC64G97ZRAS211JHHX7&msg=%E4%BB%8A%E5%A4%A9%E6%88%91%E8%A6%81%E5%81%9A%E4%BF%AF%E5%8D%A7%E6%92%91%EF%BC%81%E2%80%A6%E2%80%A6%E4%BB%8A%E5%A4%A9%E5%85%88%E4%BF%AF%E5%8D%A7%EF%BC%8C%E6%98%8E%E5%A4%A9%E5%86%8D%E6%92%91%E3%80%82&msg_type=text
```

#### 场景 3: 使用 `GET` 发送数据 (作为查询参数)

配置:

```toml
[provider.webhook]
endpoint.url = "https://my-system.com/alerts"
endpoint.method = "GET"
# content_type 在此场景下被忽略
```

PostPoint 发出的请求:
*(注意：数据被 URL 编码并附加到了 URL 后面)*

```http
GET /alerts?id=1234567EC64G97ZRAS211JHHX7&msg=%E4%BB%8A%E5%A4%A9%E6%88%91%E8%A6%81%E5%81%9A%E4%BF%AF%E5%8D%A7%E6%92%91%EF%BC%81%E2%80%A6%E2%80%A6%E4%BB%8A%E5%A4%A9%E5%85%88%E4%BF%AF%E5%8D%A7%EF%BC%8C%E6%98%8E%E5%A4%A9%E5%86%8D%E6%92%91%E3%80%82&msg_type=text
Host: my-system.com
```