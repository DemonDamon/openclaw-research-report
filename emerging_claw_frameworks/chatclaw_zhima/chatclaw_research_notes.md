# ChatClaw 调研笔记（芝麻开源）

> Source: https://github.com/zhimaAi/ChatClaw | Date: 2026-03-17

## 核心定位
"5 分钟拥有类 OpenClaw 本地知识库个人 AI 智能体"，沙箱安全防护，30MB 极小安装包。

## 基本信息

| 维度 | 详情 |
| :--- | :--- |
| GitHub Stars | 119 |
| 开发语言 | Go 41.2% / Vue 28.1% / TypeScript 27.0% / Python 2.1% |
| 许可证 | GPL-3.0 |
| 版本 | v0.5.0 (Latest) |
| Commits | 1,191 |
| 贡献者 | 4 人 |

## 技术栈

| 层级 | 技术选型 |
| :--- | :--- |
| 桌面框架 | Wails v3 (Go + WebView) |
| 后端语言 | Go 1.26 |
| 前端框架 | Vue 3 + TypeScript |
| UI 组件 | shadcn-vue + Reka UI |
| AI 框架 | Eino (ByteDance CloudWeGo) |
| 数据库 | SQLite + sqlite-vec (向量搜索) |
| 模型支持 | OpenAI / Claude / Gemini / Ollama / DeepSeek / Doubao / Qwen / Zhipu / Grok |

## 核心功能

### 1. 知识库系统
- 文档向量化存储（TXT/PDF/Word/Excel/CSV/HTML/Markdown）
- 自动解析、分片、向量嵌入
- 私有知识库精准检索

### 2. Skill 市场
- 内置技能市场，可安装扩展插件
- 支持 MCP 协议

### 3. 多渠道连接
- WhatsApp、Telegram、Slack、Discord、Gmail
- 钉钉、企业微信、QQ、飞书

### 4. 计划任务
- 内置定时任务功能

### 5. 桌面特色功能
- 文本选中即时问答（屏幕取词）
- 智能贴靠窗口（Snap Window）
- 一问多答对比（Multi-Ask）
- 浮球快速唤醒
- PPT 一键生成

### 6. 部署方式
- macOS/Windows 桌面安装（30MB）
- Linux Server 二进制部署
- Docker / Docker Compose 部署
- 支持 Server 模式（无 GUI，浏览器访问）

## 项目结构亮点
- `internal/eino/` — AI/LLM 集成层，基于字节跳动 Eino 框架
- `internal/eino/agent/` — Agent 编排
- `internal/eino/filesystem/` — 文件系统工具
- `internal/eino/sandbox/` — 沙箱安全
- `internal/eino/skill/` — Skill 管理
- `internal/eino/memory/` — 记忆系统
- `internal/eino/mcp/` — MCP 协议支持

## 对南网的价值评估
- **极轻量**：30MB 安装包 + SQLite，无需复杂基础设施
- **Go 语言**：编译为单二进制，易于内网分发
- **本地优先**：天然适合离线/内网环境
- **知识库内置**：直接替代外网搜索，用本地文档回答问题
- **国产模型支持**：Qwen/DeepSeek/Doubao/Zhipu 全覆盖
- **限制**：社区较小（119 Stars），功能相对简单，缺少企业级权限管控
