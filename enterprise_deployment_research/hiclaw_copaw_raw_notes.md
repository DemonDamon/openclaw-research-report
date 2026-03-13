# HiClaw + CoPaw 微信公众号文章核心信息提取

**来源**: https://mp.weixin.qq.com/s/JG4QiKsUys6j1rXL1VAuMw
**标题**: 阿里龙虾组合来了：HiClaw + CoPaw，内存占用大幅降低
**作者**: 澄潭 (阿里云开发者)
**日期**: 2026年3月13日

## 核心要点

### 1. HiClaw V1.0.4 引入 CoPaw 作为轻量化 Worker
- CoPaw 是基于 Python 的轻量级 AI Agent 开源项目
- 内存占用只有 OpenClaw Worker 的 1/5（~150MB vs ~500MB）
- 基于 Python，不需要 Node.js 全家桶
- 冷启动时间短
- 内置 Web 控制台，管理频道、技能、定时任务、工作区文件、环境变量
- 基于 OpenAI SDK 的工具定义，上手成本低
- 内置 ReMe 记忆管理：对话自动压缩，重要信息持久保存

### 2. Manager-Worker 架构核心价值
- HiClaw 把通信层统一到 Matrix 协议上
- 内置 Tuwunel Matrix Server（开箱即用）
- Worker 只需实现 Matrix Channel，即可复用所有 Channel 生态（Discord、Telegram、Slack、飞书、钉钉、微信等）
- CoPaw 接入 HiClaw 核心代码只有两个文件：
  - matrix_channel.py (~450行)：实现 Matrix 协议通信
  - bridge.py (~230行)：桥接 openclaw.json 到 CoPaw 配置

### 3. 两种部署模式

**模式一：Docker 容器模式（更省内存）**

| 对比项 | OpenClaw Worker | CoPaw Worker (Docker) |
|--------|----------------|----------------------|
| 基础镜像 | Node.js 全家桶 | Python 3.11-slim |
| 内存占用 | ~500MB | ~150MB |
| 启动速度 | 较慢 | 较快 |
| 安全性 | 容器隔离 | 容器隔离 |

**模式二：本地模式（直接操作本地环境）**
- CoPaw Worker 直接在宿主机运行
- 可以访问本地文件系统、操作浏览器、运行桌面应用
- 适合需要操作本地环境的场景

### 4. CoPaw 控制台
- 可视化的调试体验
- 管理频道、技能、定时任务
- 工作区文件管理
- 环境变量配置

### 5. 社区痛点优化
- 围绕社区反馈进行了多项优化

## 南网场景适配分析（待深度调研）
- 内存占用降低 → 适合资源受限的内网服务器
- 本地模式 → 适合内网隔离环境，可直接操作本地文件系统
- Matrix 协议统一通信 → 适合内网统一通信平台建设
- Python 轻量级 → 适合国产化环境适配
- CoPaw 控制台 → 适合运维可视化管理
