# NemoClaw 调研笔记

> Source: https://zhuanlan.zhihu.com/p/2017168789637186234
> Date: 2026-03-17

## 核心定位
NemoClaw 不是新 AI Agent，而是 **OpenClaw 的企业级安全发行版**。
类比：OpenClaw = Linux 内核，NemoClaw = Red Hat Enterprise Linux。

## 三大核心组件

### 1. OpenShell — 安全沙箱运行时（灵魂组件）
- 龙虾只能访问被允许的文件，不能偷看照片、聊天记录
- 所有网络访问经过策略引擎审查，未授权连接直接拦截
- 敏感凭证（API密钥、密码）不进入沙箱文件系统，以环境变量形式运行时注入
- 开发者用 YAML 语法定义安全策略，部分规则支持热更新

### 2. Nemotron — 优化的本地AI模型族
- Nemotron 3 Super：1200亿参数，专为长上下文 Agent 任务优化
- Nemotron 3 Nano：300亿参数，百万级 Token 上下文窗口，混合 MoE 架构
- **隐私路由器（Privacy Router）**：自动判断哪些数据可发送云端，哪些必须留在本地

### 3. Agent Toolkit — 企业级工具包
- AI-Q 开源研究蓝图：Nemotron做研究 + 前沿模型做编排
- 查询处理成本砍半
- DeepResearch Bench I & II 排行榜第一

## 部署体验
- 一条命令安装：Nemotron 模型 + OpenShell 运行时 + 安全策略
- 不到两分钟完成
- 不锁定英伟达硬件，AMD/Intel 一样能跑

## 硬件适配
| 平台 | 适用场景 |
| :--- | :--- |
| GeForce RTX PC/笔记本 | 个人开发者、小团队 |
| RTX PRO 工作站 | 专业用户、中型企业 |
| DGX Spark | 企业级原型验证 |
| DGX Station | 企业级生产部署 |
| 云端/本地服务器 | 大规模部署 |

## OpenClaw 安全问题背景
- 82个已披露CVE漏洞，33个高危
- CVE-2026-25253（CVSS 8.8）：点击恶意网页即可接管龙虾
- 46.9万实例暴露公网，27.2%存在高危漏洞
- ClawHub 近4000个Skills中36.8%存在安全问题
- 22%受监控企业发现员工私自安装（影子部署）

## 国产龙虾大乱斗
- 腾讯元宝 Claw：微信生态深度绑定
- 字节 ClawX：桌面端，集成豆包
- 阿里通义 CoPaw：钉钉生态
- 百度 App 接入 OpenClaw
- MiniMax MaxClaw / Kimi Claw

## 黄仁勋战略
- 从卖GPU到卖"Agent操作系统"
- NemoClaw 免费开源，但最佳体验需要 NVIDIA GPU
- 类似当年 CUDA 策略：先用开源吸引开发者，工作负载上量后回到 GPU 生态
