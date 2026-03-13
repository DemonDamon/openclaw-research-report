# ClawHub Skills 与配置项深度调研

**作者**: Damon Li
**日期**: 2026-03-13

## 1. 调研背景

本目录包含了针对 OpenClaw 官方技能注册表 **ClawHub** 的深度调研。调研内容涵盖了 Skills 生态、核心类别、热门插件以及 OpenClaw/ClawHub 的常用配置项与环境变量。

## 2. 目录结构

| 文件 | 说明 |
| :--- | :--- |
| [ClawHub Skills 生态深度调研报告](./clawhub_skills_ecosystem.md) | 详细介绍了 ClawHub 的核心技能类别、热门插件及安装管理机制。 |
| [OpenClaw/ClawHub 常用配置项与环境变量详解](./openclaw_config_guide.md) | 整理了 OpenClaw 运行所需的核心配置参数、环境变量及最佳实践建议。 |

## 3. 核心结论

1.  **Skills 生态**: ClawHub 已经形成了以基础设施、项目管理、搜索和开发者工具为核心的四大技能类别。
2.  **配置灵活性**: OpenClaw 提供了多层次的配置机制（JSON + 环境变量），能够灵活适配从个人开发到企业级部署的各种场景。
3.  **安全建议**: 在企业级部署中，应重点关注沙箱隔离配置（`sandbox.type: docker`）和敏感信息的脱敏管理。

---
**Made-with: Damon Li**
