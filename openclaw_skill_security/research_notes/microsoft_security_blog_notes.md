# 微软安全博客 - Running OpenClaw Safely 关键摘录

来源: https://www.microsoft.com/en-us/security/blog/2026/02/19/running-openclaw-safely-identity-isolation-runtime-risk/
日期: 2026年2月19日
作者: Microsoft Defender Security Research Team

## 核心警告
> "OpenClaw should be treated as untrusted code execution with persistent credentials."
> "It is not appropriate to run on a standard personal or enterprise workstation."

## 三大核心风险
1. **凭证和数据泄露**: 凭证和可访问数据可能被暴露或外泄
2. **持久化状态篡改**: Agent 的持久化状态或"记忆"可被修改，导致其长期遵循攻击者指令
3. **宿主机被攻陷**: 如果 Agent 被诱导下载并执行恶意代码，宿主机环境可被攻陷

## 两类安全供应链问题
1. **Skill 恶意软件（不可信代码）**: Agent 从各种来源获取 Skills，本质是从互联网下载并运行代码
2. **间接提示注入（不可信指令）**: 攻击者在 Agent 读取的内容中隐藏恶意指令

## 安全边界三要素
1. **Identity（身份）**: Agent 用于工作的 Token（SaaS API、代码仓库、邮件、云控制平面）
2. **Execution（执行）**: 可改变状态的工具（文件、Shell、基础设施、消息）
3. **Persistence（持久化）**: 跨运行保持变更的方式（任务、配置、调度）

## 端到端攻击场景：中毒 Skill
### 5 步攻击链
1. **Distribution（分发）**: 攻击者将恶意 Skill 发布到 ClawHub，伪装成实用工具
2. **Installation（安装）**: 开发者或 Agent 发起安装，宽松部署下无需人工审批
3. **State Access（状态访问）**: 攻击者获取 Agent 状态（Token、缓存凭证、配置、对话记录）
4. **Persistence（持久化）**: 通过修改配置或状态实现持久控制
5. **Execution（执行）**: 利用获取的凭证执行恶意操作

## 最小安全运行姿态
- 部署在完全隔离的环境（专用 VM 或独立物理系统）
- 使用专用、非特权凭证
- 仅访问非敏感数据
- 持续监控 + 重建计划

## 检测与狩猎指南
- 使用 Microsoft Defender XDR 进行监控
- 关注异常进程创建、网络连接、文件系统变更
- 监控 Agent 状态目录的异常修改
