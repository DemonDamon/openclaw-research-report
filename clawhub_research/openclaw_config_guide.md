---
author: Damon Li
---

# OpenClaw 最新版配置项与环境变量详解 (2026版)

本文档基于 OpenClaw 最新版（2026年3月）的源码 Zod Schema，对 `openclaw.json` 的核心配置项进行深度解析，旨在为企业级和生产环境部署提供一份精准、权威的参考指南。

## 核心配置模块概览

OpenClaw 的配置体系围绕 20 多个顶级模块构建，覆盖了从模型、工具、沙箱到安全、网络、UI 的方方面面。以下是核心模块的分类与说明：

| 模块分类 | 核心模块 | 说明 |
| :--- | :--- | :--- |
| **Agent 核心** | `agents` | 定义所有 Agent 的身份、模型、工具、沙箱及内存。 |
| | `models` | 配置所有可用的大语言模型（LLM）及 Embedding 模型。 |
| | `tools` | 全局工具策略、Web 搜索、文件系统等工具的默认配置。 |
| | `skills` | Skill 的加载、安装、限制及自定义入口配置。 |
| **安全与沙箱** | `sandbox` | Docker 沙箱的镜像、网络、资源限制及安全策略。 |
| | `approvals` | 工具执行审批流的配置。 |
| | `auth` | 模型提供商的认证 Profile、轮换及冷却策略。 |
| **网络与服务** | `gateway` | Gateway 服务的端口、认证、TLS 及 Tailscale 集成。 |
| | `browser` | CDP 浏览器服务的配置、SSRF 防护及 Profile 管理。 |
| | `cron` | 定时任务的启用、存储、重试及失败告警。 |
| **其他** | `log` | 日志级别、驱动及文件轮转策略。 |
| | `secrets` | 密钥管理，支持从文件、环境变量或 1Password 加载。 |

---

## 关键配置模块深度解析

### 1. `agents` - Agent 定义

这是 OpenClaw 的核心，定义了每个 Agent 的具体行为。

```json
"agents": {
  "defaults": { ... },
  "list": [
    {
      "id": "my-coding-agent",
      "name": "代码助手",
      "model": { "primary": "gpt-4.1-mini" },
      "tools": {
        "profile": "coding",
        "deny": ["browser"]
      },
      "sandbox": {
        "workspaceAccess": "rw",
        "docker": {
          "image": "ubuntu:22.04"
        }
      }
    }
  ]
}
```

-   **`defaults`**: 所有 Agent 的默认配置，可被 `list` 中的具体 Agent 覆盖。
-   **`list`**: Agent 列表，每个对象定义一个独立的 Agent。
    -   **`id`**: Agent 的唯一标识符。
    -   **`model`**: Agent 使用的模型，支持 `primary` 和 `fallbacks`。
    -   **`tools`**: Agent 的工具策略。
        -   **`profile`**: 工具预设配置，可选 `minimal`, `coding`, `messaging`, `full`。
        -   **`allow` / `deny`**: 工具的白名单/黑名单。
    -   **`sandbox`**: Agent 的沙箱配置。
        -   **`workspaceAccess`**: 工作区访问权限 (`none`, `ro`, `rw`)。
        -   **`docker`**: Docker 沙箱的具体配置（见下文）。

### 2. `sandbox` - Docker 沙箱安全

新版 OpenClaw 对沙箱安全做了大量加固，所有危险配置项都加上了 `dangerously` 前缀，并默认禁用。

```json
"sandbox": {
  "docker": {
    "network": "bridge",
    "readOnlyRoot": true,
    "capDrop": ["ALL"],
    "pidsLimit": 1024,
    "memory": "4g",
    "seccompProfile": "default",
    "apparmorProfile": "docker-default",
    "dangerouslyAllowReservedContainerTargets": false
  }
}
```

-   **`network`**: 网络模式，`host` 模式已被禁用，推荐使用 `bridge` 或 `none`。
-   **`readOnlyRoot`**: 将容器的根文件系统设为只读。
-   **`capDrop`**: 移除容器的 Linux Capabilities，`ALL` 表示移除所有。
-   **`pidsLimit`**: 限制容器内的进程数。
-   **`memory`**: 限制容器的内存使用。
-   **`seccompProfile` / `apparmorProfile`**: Seccomp 和 AppArmor 安全配置文件，`unconfined` 已被禁用。

### 3. `auth` - 模型认证与轮换

新版 `auth` 模块引入了更精细的 `cooldowns` 策略，用于处理 API Key 失效或计费问题。

```json
"auth": {
  "profiles": {
    "openai-1": { "provider": "openai", "mode": "api_key" }
  },
  "order": {
    "gpt-4.1-mini": ["openai-1"]
  },
  "cooldowns": {
    "billingBackoffHours": 24,
    "failureWindowHours": 1
  }
}
```

-   **`profiles`**: 定义所有模型提供商的认证信息。
-   **`order`**: 为每个模型指定认证 Profile 的使用顺序。
-   **`cooldowns`**: 冷却策略。
    -   **`billingBackoffHours`**: 因计费问题（如 429）失败后，该 Profile 的冷却时间（小时）。
    -   **`failureWindowHours`**: 在多长时间内的连续失败会计入冷却计算。

### 4. `gateway` - 网关服务

Gateway 是 OpenClaw 对外提供服务的入口，新版增加了丰富的认证和安全选项。

```json
"gateway": {
  "port": 5100,
  "auth": {
    "mode": "token",
    "token": "${GATEWAY_TOKEN}",
    "rateLimit": {
      "maxAttempts": 5,
      "windowMs": 60000
    }
  },
  "tls": {
    "enabled": true,
    "autoGenerate": true
  }
}
```

-   **`auth.mode`**: 认证模式，支持 `none`, `token`, `password`, `trusted-proxy`。
-   **`auth.rateLimit`**: 认证失败的速率限制，防止暴力破解。
-   **`tls.enabled`**: 启用 HTTPS。

---

## 环境变量

除了 `openclaw.json`，OpenClaw 还支持通过环境变量进行配置，优先级高于配置文件。所有配置项都可以转换为环境变量，规则如下：

-   层级用双下划线 `__` 分隔。
-   数组用索引表示，如 `OPENCLAW__AGENTS__LIST__0__ID`。
-   所有字母大写。

**示例**：

```bash
# 相当于在 openclaw.json 中配置 agents.list[0].id
export OPENCLAW__AGENTS__LIST__0__ID="my-agent"

# 配置 Gateway Token
export OPENCLAW__GATEWAY__AUTH__TOKEN="your-secret-token"
```

这份文档旨在提供一份与最新版本同步的权威参考。随着 OpenClaw 的快速迭代，建议在生产部署前，再次对照官方文档或源码进行最终确认。
