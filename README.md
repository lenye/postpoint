# PostPoint 一个 API，连接所有核心工作群

PostPoint 提供了一个统一、高可用的 API 接口，你无需关心各平台的接口差异和复杂的频率限制，
只需一次集成，即可将业务系统的关键通知，稳定、即时地发送到任何主流工作平台和通用Webhook。

* 极致简化： 单一 API，告别重复开发和维护。
* 智能可靠： 内置失败自动重试，并智能遵守各平台发送频率，确保消息100%稳定触达。
* 全面覆盖： 无缝支持企业微信、飞书、钉钉、Slack、Discord 及通用Webhook。

## 支持的操作系统

* Windows
* Linux
* macOS
* FreeBSD

## 上手指南

```shell
C:\>postpoint.exe -h
一个 API 请求将消息发送到企业微信、飞书、钉钉、Slack、Discord、Webhook

Usage:
  postpoint [command]

Available Commands:
  help        Help about any command
  serve       API / OpenAPI / Swagger UI 服务，消息推送服务
  test        测试已配置的企业微信群机器人、飞书自定义机器人、钉钉自定义机器人、Slack机器人、Discord机器人、Webhook

Flags:
  -h, --help      help for postpoint
  -v, --version   version for postpoint

Use "postpoint [command] --help" for more information about a command.
```

### 安装`PostPoint`

`PostPoint`下载 [最新版本](https://github.com/lenye/postpoint/releases/tag/v25.8.1-beta1)

#### Windows 操作系统

1. 解压下载文件`postpoint_v25.8.1-beta1_windows_x86_64.zip`；
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
   2025-07-01T22:07:01.627+0800    info    PostPoint Free v25.7.8-beta1 windows/amd64, https://github.com/lenye/postpoint
   2025-07-01T22:07:01.667+0800    info    url    {"API": "http://localhost:39270/text", "Swagger UI": "http://localhost:39270/swagger/", "OpenAPI": "http://localhost:39270/swagger/openapi.yaml"}
   ```   

运行`postpoint.exe test -h`查看通道的测试命令。

单独测试企业微信群机器人，运行`postpoint.exe test workweixin_bot`。

#### 完成

如果你顺利完成了以上步骤，那么恭喜你，属于你的`PostPoint`搭建成功。

### 通道集成指南

`PostPoint`配置文件的详细定义，请参考[通道集成指南](provider/README.md)，样例配置文件 [config.toml](config.toml)

## 调用 API 发送消息

* HTTP 方法: POST
* Endpoint: http://localhost:39270/text
* 数据格式: 请求和响应数据编码均为 UTF-8。支持 application/json 和 application/x-www-form-urlencoded 两种提交方式。

### 请求参数

请求体 (Body)

| 参数名   | 类型     | 必填 | 描述   |
|:------|:-------|:---|:-----|
| `msg` | string | 是  | 消息内容 |

### 响应数据

| 字段名    | 类型     | 描述                      |
|:-------|:-------|:------------------------|
| `code` | string | 结果代码，`ok` 表示成功          |
| `msg`  | string | 结果描述                    |
| `id`   | string | 本次请求的唯一 ID，可用于问题排查和日志跟踪 |

使用 API 发送消息，发出如下所示的 HTTP POST 请求：

```
POST /text
Content-type: application/json

{
    "msg": "测试，测试，测试"
}
```

#### 成功响应

当消息成功进入 PostPoint 的发送队列时，将收到 `HTTP 200 OK` 响应。

```
HTTP/1.1 200 OK
Content-Type: application/json

{
    "code": "ok",
    "msg": "success",
    "id": "1234567EC64G97ZRAS211JHHX7"
}
```

#### 失败响应

当请求本身存在问题时（如参数错误、Token 无效），将收到 `4xx` 或 `5xx` 的 HTTP 状态码。

```
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
    "code": "invalid_argument",
    "msg": "msg 是必填项",
    "id": "1234567EC64G97ZRAS211JHHX8"
}
```

#### 通用错误码

| HTTP 状态码                    | `code` 值             | 描述                        |
|:----------------------------|:---------------------|:--------------------------|
| `400 Bad Request`           | `invalid_argument`   | 请求参数无效或缺失。`msg` 字段会提供详细信息 |
| `401 Unauthorized`          | `unauthenticated`    | 无效的令牌，请检查 token 是否正确      |
| `404 Not Found`             | `not_found`          | url 不存在或已被删除              |
| `405 Method Not Allowed`    | `method_not_allowed` | 请求方法错误，请使用 `POST` 方法      |
| `429 Too Many Requests`     | `resource_exhausted` | 请求频率过高，请稍后重试              |
| `500 Internal Server Error` | `internal`           | PostPoint 服务器内部错误，请联系我们处理 |
| `503 Service Unavailable`   | `unavailable`        | PostPoint 许可证到期，请联系我们处理   |

### 使用 Swagger UI

`PostPoint`OpenAPI http://localhost:39270/swagger/openapi.yaml

你可以访问 Swagger UI 调用 API 发送消息，http://localhost:39270/swagger/

可以使用代码生成器 [Swagger Codegen](https://swagger.io/tools/swagger-codegen/)，根据 OpenAPI 规范自动生成各种语言的客户端
SDK。

### 使用 curl

可以使用 curl 命令转代码（复制 linux 环境的命令到转码工具），自动生成各种语言的客户端 SDK。

下面是使用 curl 发送一个消息的示例，消息内容：测试，测试，测试

1. 发送 json 数据
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
2. 发送 form 数据
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

### 使用 Python

```python
import requests
import json

apiUrl = f"http://localhost:39270/text"

payload = {
    "msg": "来自 Python 的监控告警：\n> 服务 **API-Gateway** 在 5 分钟内错误率超过 5%。"
}

headers = {
    'Content-Type': 'application/json'
}

try:
    response = requests.post(apiUrl, headers=headers, data=json.dumps(payload), timeout=10)
    response.raise_for_status()  # 如果状态码不是 2xx，则会抛出异常

    # 打印成功响应
    print("请求成功!")
    print(f"状态码: {response.status_code}")
    print(f"响应内容: {response.json()}")

except requests.exceptions.RequestException as e:
    # 打印错误响应
    print(f"请求失败: {e}")
    if e.response:
        print(f"状态码: {e.response.status_code}")
        print(f"响应内容: {e.response.text}")

```

### 使用 PHP

```PHP
<?php

/**
 * 使用 PostPoint API 发送消息。
 *
 * @param string $message 要发送的消息内容。
 * @param string $apiUrl API 的完整 URL 地址。
 * @return array 包含响应结果的关联数组 ['success' => bool, 'data' => array|string]
 */
function sendMessage(string $message, string $apiUrl): array
{
    // 1. 准备请求数据
    // 要发送的数据 (PHP 关联数组)
    $postData = [
        'msg' => $message
    ];

    // 将数据编码为 JSON 字符串
    $jsonData = json_encode($postData);

    // 2. 设置请求头，完全符合文档要求
    $headers = [
        'Content-Type: application/json',
        'Accept: application/json' // 最好也加上 Accept 头，以表明期望接收 JSON 响应
    ];

    // 3. 初始化 cURL 并设置选项
    $ch = curl_init();

    // 设置请求的 URL
    curl_setopt($ch, CURLOPT_URL, $apiUrl);
    // 设置为 POST 请求
    curl_setopt($ch, CURLOPT_POST, true);
    // 设置 POST 的数据体 (JSON 字符串)
    curl_setopt($ch, CURLOPT_POSTFIELDS, $jsonData);
    // 设置自定义请求头
    curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
    // 将执行结果以字符串返回，而不是直接输出
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    // 设置一个合理的超时时间 (例如：10秒)
    curl_setopt($ch, CURLOPT_TIMEOUT, 10);

    // 4. 执行请求
    $responseBody = curl_exec($ch);
    // 获取 HTTP 响应状态码
    $httpStatusCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);

    // 5. 检查 cURL 本身是否出错 (例如网络连接失败)
    if (curl_errno($ch)) {
        $error_msg = curl_error($ch);
        curl_close($ch);
        return ['success' => false, 'data' => 'cURL 请求错误: ' . $error_msg];
    }

    // 6. 关闭 cURL
    curl_close($ch);

    // 7. 根据 HTTP 状态码处理响应
    $responseData = json_decode($responseBody, true); // 将 JSON 响应体解码为 PHP 数组

    if ($httpStatusCode == 200) {
        // 成功响应 (HTTP 200 OK)
        return ['success' => true, 'data' => $responseData];
    } else {
        // 失败响应 (4xx 或 5xx)
        return ['success' => false, 'data' => $responseData];
    }
}


// --- 使用示例 ---

// 请将这里的 URL 替换为您的实际 API 地址
// 注意：文档中的 `/text` 是路径，需要拼接上主机地址
$apiUrl = 'http://localhost:39270/text'; // 假设 API 服务运行在本地的 39270 端口

echo "--- 正在尝试发送消息 ---\n";
$result = sendMessage('测试，测试，测试', $apiUrl);

if ($result['success']) {
    echo "消息发送成功！\n";
    echo "状态: " . $result['data']['code'] . "\n";
    echo "信息: " . $result['data']['msg'] . "\n";
    echo "消息 ID: " . $result['data']['id'] . "\n";
} else {
    // 即使逻辑上成功，但如果 API 端返回错误，也会进入这里
    echo "API 返回错误！\n";
    echo "HTTP 状态码: " . ($result['http_code'] ?? 'N/A') . "\n"; // http_code 不一定存在
    echo "错误码: " . ($result['data']['code'] ?? '未知') . "\n";
    echo "错误信息: " . ($result['data']['msg'] ?? '无法解析的错误') . "\n";
    echo "请求 ID: " . ($result['data']['id'] ?? 'N/A') . "\n";
}
?>
```

### 使用 JavaScript

```javascript
/**
 * 使用 API 发送消息。
 * @param {string} message 要发送的消息内容。
 * @param {string} apiUrl API 的完整 URL 地址。
 * @returns {Promise<object>} 一个包含响应结果的对象 { success: boolean, data: object | string }
 */
async function sendMessage(message, apiUrl) {
   // 1. 准备请求数据和请求头
   const postData = {
      msg: message
   };

   const requestOptions = {
      method: 'POST',
      headers: {
         'Content-Type': 'application/json',
         'Accept': 'application/json'
      },
      body: JSON.stringify(postData) // 必须将 JavaScript 对象转换为 JSON 字符串
   };

   try {
      // 2. 发送 HTTP 请求
      const response = await fetch(apiUrl, requestOptions);

      // 3. 将响应体解析为 JSON
      // response.json() 总是会尝试解析，即使是 4xx/5xx 错误
      const responseData = await response.json();

      // 4. 根据 HTTP 状态码判断成功或失败
      // response.ok 为 true 表示 HTTP 状态码为 200-299
      if (response.ok) {
         // 成功响应 (HTTP 200 OK)
         return {success: true, data: responseData};
      } else {
         // 失败响应 (HTTP 4xx or 5xx)
         return {success: false, data: responseData, status: response.status};
      }

   } catch (error) {
      // 捕获网络错误或 JSON 解析错误等
      console.error('Request failed:', error);
      return {success: false, error: error.message};
   }
}

// --- 使用示例 ---

// 请将这里的 URL 替换为您的实际 API 地址
// 注意：文档中的 `/text` 是路径，需要拼接上主机地址
const apiUrl = 'http://localhost:39270/text';

console.log("--- 正在尝试发送消息 ---");
sendMessage('测试，测试，测试', apiUrl).then(result => {
   if (result.success) {
      console.log("消息发送成功！");
      console.log("状态:", result.data.code);
      console.log("信息:", result.data.msg);
      console.log("消息 ID:", result.data.id);
   } else {
      // 处理 API 返回的业务错误
      console.log("API 返回错误！");
      console.log("HTTP 状态码:", result.status);
      console.log("错误码:", result.data.code);
      console.log("错误信息:", result.data.msg);
      console.log("请求 ID:", result.data.id);
   }
   console.log("\n----------------------------------------\n");
});

```

## 贡献

如果你发现此软件存在问题，欢迎创建 [Issue](https://github.com/lenye/postpoint/issues) 来帮助改进项目。

## 免责声明

本软件仅供学习和研究之用，使用本软件的风险由你自行承担，我们对使用本软件时产生的任何损失概不负责。