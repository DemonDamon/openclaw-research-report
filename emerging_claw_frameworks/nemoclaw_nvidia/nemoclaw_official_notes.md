# NVIDIA NemoClaw 官方信息 — 调研笔记

> Source: https://www.nvidia.com/en-us/ai/nemoclaw/ | Date: 2026-03-17

## 核心定位
"Deploy more secure, always-on AI assistants with a single command."
NVIDIA NemoClaw 是一个开源软件栈，为 OpenClaw 添加隐私和安全控制。

## 安装方式
```bash
$ curl -fsSL https://nvidia.com/nemoclaw.sh | bash
$ nemoclaw onboard
```

## 核心组件

### 1. NVIDIA OpenShell
- 开源运行时，使自主 Agent 更安全、更快地运行和适应
- 执行基于策略的隐私和安全护栏（Guardrails）
- 让用户控制 Agent 的行为方式和数据处理方式

### 2. NVIDIA Agent Toolkit
- 为自主 Agent 带来信任和安全
- 包含 OpenShell（增强安全和隐私）和 AI-Q（构建推理 Agent）
- 将企业数据转化为可解释的结果

### 3. NVIDIA Nemotron 模型
- 评估可用计算资源，本地运行高性能开放模型
- 增强隐私和成本效率

## 三大核心特性

### Run Claws More Safely
- OpenClaw 已成为个人 AI 的操作系统
- NemoClaw 添加安全和隐私控制
- 开发者可以更有信心地构建和运行 AI 助手

### Use Any Coding Agent
- 利用 NVIDIA Nemotron 等开放模型在本地专用系统上运行
- **隐私路由器（Privacy Router）**：连接 Agent 到云端前沿模型
- Agent 在定义的隐私和安全护栏内开发新技能

### Deploy Anywhere
- 始终在线的 Agent 需要专用计算来运行工具、编写代码、完成任务
- 支持平台：
  - NVIDIA GeForce RTX PCs/laptops
  - NVIDIA RTX PRO workstations
  - NVIDIA DGX Station / DGX Spark

## 关键技术亮点
1. **一键安装**：单个命令即可部署
2. **隐私路由器**：智能路由敏感数据到本地模型，非敏感数据到云端
3. **策略引擎**：基于策略的安全护栏，控制 Agent 行为边界
4. **本地优先**：优先使用本地 GPU 运行模型，减少数据外泄风险
5. **自进化能力**：Agent 可在安全护栏内自我进化和学习新技能

## 对南网的价值
- **隐私路由器**：完美匹配内网场景，敏感数据永远不出内网
- **策略引擎**：可定制安全策略，满足电力行业合规要求
- **本地部署**：支持 RTX 工作站和 DGX，适合内网算力部署
- **限制**：依赖 NVIDIA GPU 生态，国产化替代（昇腾）需要额外适配
