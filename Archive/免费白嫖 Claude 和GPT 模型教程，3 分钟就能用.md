# 注册

打开 https://replit.com/ 注册账号，选择 免费计划。

# 输入 Prompt

在首页输入下面的 Prompt，按提示继续操作。

```
目标：在 Replit pnpm monorepo 中建立 OpenAI 兼容反代 API 并发布。

【环境准备】
1. 在 Replit Integrations 中添加 OpenAI 和 Anthropic AI 集成
2. 设置 Secret：PROXY_API_KEY（任意字符串，作为 Bearer Token）、SESSION_SECRET

【API Server 修改】
文件：artifacts/api-server/src/routes/proxy.ts
- 创建 GET /v1/models，返回模型列表（gpt-5.2, gpt-5-mini, gpt-5-nano, o4-mini, o3, claude-opus-4-6, claude-sonnet-4-6, claude-haiku-4-5）
- 创建 POST /v1/chat/completions：
  - 验证 Authorization: Bearer ${PROXY_API_KEY}，失败返回 401
  - 按 model 前缀分发：gpt/o 开头→OpenAI client，claude 开头→Anthropic client
  - Anthropic 需转换格式：提取 system 消息，messages 只保留 user/assistant
  - 支持 stream=true：设置 Content-Type: text/event-stream，X-Accel-Buffering: no，res.flushHeaders()
  - OpenAI 流：直接透传 chunk，每块 res.flush()
  - Anthropic 流：将 content_block_delta 转为 OpenAI chunk 格式，每块 res.flush()
  - 每 5 秒发送 keepalive (": keepalive\n\n")，req.on("close") 时 clearInterval，用 try/finally 防 500
  - 非流：等待完整响应后返回 OpenAI 格式 JSON
  - Express body limit 设为 50mb

文件：artifacts/api-server/src/app.ts
- app.use("/v1", proxyRouter)

文件：artifacts/api-server/.replit-artifact/artifact.toml
- paths 数组加入 "/v1"

【前端门户】
用 createArtifact({ artifactType: "react-vite", slug: "api-portal", previewPath: "/", title: "API Portal" }) 创建 artifact

App.tsx 功能：
- fetch(`${window.location.origin}/api/healthz`) 检测在线状态
- 展示 Base URL、/v1/models、/v1/chat/completions、API Key 说明
- 复制按钮
- CherryStudio 4 步设置指引
- 模型列表
- 深色主题

【发布】
- 启动两个 workflow：api-server 和 api-portal
- presentArtifact({ artifactId: "artifacts/api-portal" })
- suggestDeploy()
- 发布后域名即为 CherryStudio 的 Base URL


```



# 获取地址

创建完成后，会显示一个接口地址，例如：

```text
https://xxx--username.replit.app
```

- **Base URL**：上面的地址
- **API Key**：你刚才填写的那个 Key
