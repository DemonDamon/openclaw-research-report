# ClawHub Skills 生态深度调研报告

**作者**: Damon Li
**日期**: 2026-03-13

## 1. ClawHub 概述

ClawHub 是 OpenClaw 官方的公共技能注册表（Public Registry），它作为一个版本化的技能包存储库，为开发者提供了技能的发现、搜索、安装和发布功能。ClawHub 的核心价值在于其“Markdown-as-a-Skill”的理念，即技能主要由 `SKILL.md` 文件及相关的支持脚本组成。

## 2. 核心技能类别与热门插件

根据调研，ClawHub 上的技能可以分为以下几个核心类别：

### 2.1 基础设施与系统工具 (Infrastructure & System)
这类技能扩展了 Agent 对底层系统的操作能力。
-   **AgentMail**: 为 Agent 提供邮件发送和接收的基础设施。
-   **Automation Workflows**: 允许用户通过可视化或脚本方式构建复杂的自动化工作流。
-   **Persistent Storage**: 增强 Agent 的长期记忆和结构化数据存储能力。

### 2.2 项目管理与协作 (Project Management & Collaboration)
将 Agent 接入主流的办公和协作平台。
-   **Linear**: 深度集成 Linear 项目管理工具，支持创建任务、更新状态等。
-   **Monday.com**: 接入 Monday 平台，实现团队任务的自动化同步。
-   **Slack/Discord Connect**: 使 Agent 能够作为机器人参与到即时通讯频道的讨论中。

### 2.3 搜索与知识获取 (Search & Knowledge)
增强 Agent 的信息检索深度。
-   **Valyu Search**: 专业的搜索技能，常被设置为默认搜索提供商，提供更精准的网页抓取和分析。
-   **DeepWiki**: 针对特定技术文档（如 OpenClaw 自身文档）的深度查询技能。

### 2.4 开发者工具 (Developer Tools)
辅助代码编写、测试和部署。
-   **GitHub OAuth**: 处理 GitHub 的授权流程，使 Agent 能够代表用户操作仓库。
-   **CI/CD Pipeline**: 监控和触发持续集成/持续部署流程。

## 3. 技能安装与管理机制

ClawHub 主要通过命令行界面（CLI）进行交互，其核心操作流程如下：
1.  **搜索**: `clawhub search <keyword>`
2.  **安装**: `clawhub install <skill-name>`
3.  **更新**: `clawhub update <skill-name>`
4.  **配置**: 技能安装后，其配置通常存储在 `~/.openclaw/openclaw.json` 的 `skills.entries` 字段中。

## 4. 总结

ClawHub 不仅仅是一个插件市场，它是 OpenClaw 生态的“大脑扩展包”。通过标准化的 `SKILL.md` 规约，它降低了技能开发的门槛，同时通过版本控制确保了企业级应用的稳定性。对于南网等企业客户，建设私有化的 ClawHub 镜像将是实现技能受控管理的关键一步。
