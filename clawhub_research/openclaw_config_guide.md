# OpenClaw/ClawHub 常用配置项与环境变量详解

**作者**: Damon Li
**日期**: 2026-03-13

## 1. 配置文件概览

OpenClaw 的核心配置通常存储在用户主目录下的 JSON 文件中：
-   **主配置文件**: `~/.openclaw/openclaw.json`
-   **环境变量文件**: `.env` (位于项目根目录或运行目录)

## 2. 核心配置项详解

### 2.1 基础运行配置 (Core Runtime)
这些配置项决定了 OpenClaw 的基本运行行为。

| 配置项 | 说明 | 示例值 |
| :--- | :--- | :--- |
| `port` | 服务监听端口 | `3000` |
| `host` | 服务监听地址 | `0.0.0.0` |
| `workspace` | Agent 操作的根目录 | `/home/ubuntu/workspace` |
| `logLevel` | 日志记录级别 | `info`, `debug`, `error` |

### 2.2 模型提供商配置 (Model Providers)
用于配置 LLM 的 API 密钥和基础 URL。

| 环境变量 | 说明 | 备注 |
| :--- | :--- | :--- |
| `OPENAI_API_KEY` | OpenAI API 密钥 | 必填 (如果使用 OpenAI) |
| `ANTHROPIC_API_KEY` | Anthropic API 密钥 | 必填 (如果使用 Claude) |
| `GEMINI_API_KEY` | Google Gemini API 密钥 | 必填 (如果使用 Gemini) |
| `OLLAMA_BASE_URL` | Ollama 本地模型地址 | `http://localhost:11434` |

### 2.3 技能配置 (Skills Configuration)
技能的配置通常位于 `openclaw.json` 的 `skills` 字段下。

```json
{
  "skills": {
    "entries": {
      "valyu-search": {
        "enabled": true,
        "env": {
          "VALYU_API_KEY": "sk-xxxx"
        }
      },
      "github-oauth": {
        "enabled": true,
        "config": {
          "clientId": "ov-xxxx",
          "clientSecret": "xxxx"
        }
      }
    }
  }
}
```

### 2.4 安全与授权配置 (Security & Auth)
对于企业级部署至关重要的配置项。

| 配置项 | 说明 | 示例值 |
| :--- | :--- | :--- |
| `auth.enabled` | 是否启用身份验证 | `true` |
| `auth.secret` | 用于签名 Token 的密钥 | `long-random-string` |
| `cors.origin` | 允许跨域请求的来源 | `https://your-domain.com` |
| `sandbox.type` | 沙箱隔离类型 | `docker`, `process`, `none` |

## 3. 环境变量优先级

OpenClaw 遵循以下环境变量加载优先级（从高到低）：
1.  **系统环境变量**: 直接在 Shell 中导出的变量。
2.  **`.env` 文件**: 运行目录下的环境配置文件。
3.  **`openclaw.json` 中的 `env` 块**: 配置文件中显式定义的变量。

## 4. 最佳实践建议

1.  **敏感信息脱敏**: 永远不要将 API 密钥直接写入 `openclaw.json` 并提交到版本控制系统，应优先使用环境变量或 `.env` 文件。
2.  **沙箱隔离**: 在生产环境下，务必将 `sandbox.type` 设置为 `docker`，以防止 Agent 意外破坏宿主机系统。
3.  **日志审计**: 启用 `debug` 级别的日志，并将其重定向到持久化存储，以便进行事后审计。
