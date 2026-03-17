# MAESTRO 框架对 OpenClaw 的威胁模型分析

来源: https://cloudsecurityalliance.org/blog/2026/02/20/openclaw-threat-model-maestro-framework-analysis
作者: Ken Huang, CEO & Chief AI Officer, DistributedApps.ai
日期: 2026年2月20日

## MAESTRO 7 层模型

### Layer 1 - Foundation Models
- LM-001: 通过消息通道的对抗性提示注入（Critical）
- LM-002: 多轮上下文越狱（High）
- LM-003: 模型提供商 API Key 暴露（Critical）
- LM-004: 通过文件上传的提示注入（High）
- LM-005: 系统提示泄露（Medium）

### Layer 2 - Data Operations
- DO-001: 明文存储凭证（Critical）
- DO-002: 全局可读状态目录（Critical）
- DO-003: 消息历史泄露（High）
- **DO-004: Skill 代码注入（Critical）** ← 核心 Skill 安全威胁
  - Skills 从 ~/.openclaw/workspace/skills/ 加载，以 Agent 权限执行任意代码
  - 缓解: src/security/skill-scanner.ts 实现逐行规则检查
  - 检测: 危险 exec 函数、环境变量窃取、文件系统操作、网络请求
- DO-005: 向量存储投毒（High）

### Layer 3 - Agent Frameworks
- AF-001: 工具调用操纵（Critical）
  - 缓解: src/tools/tool-policy.ts 实现白名单/黑名单
- AF-002: 内存注入攻击（High）
- AF-003: Agent 配置篡改（Critical）

### Layer 4 - Deployment & Infrastructure
- DI-001: Docker 沙箱逃逸（Critical）
  - 沙箱容器配置了 Docker socket 访问可逃逸隔离
- DI-002: Gateway 端口暴露（High）
  - 默认端口 18789 绑定到 127.0.0.1
- DI-003: 更新机制劫持（High）

### Layer 5 - Multi-Agent Orchestration
- MA-001: Agent 间通信劫持
- MA-002: 权限升级通过 Agent 链

### Layer 6 - Ecosystem
- EC-001: ClawHub Skill 供应链攻击（Critical）
  - 恶意 Skills 通过 ClawHub 分发
  - 缓解: VirusTotal + 静态分析 + AI 审核

### Layer 7 - Monitoring & Compliance
- MC-001: 日志和监控不足

## Skill 安全关键源码文件
- `src/security/skill-scanner.ts` — Skill 扫描器
- `src/security/audit.ts` — 安全审计
- `src/tools/tool-policy.ts` — 工具策略
- `src/agents/bootstrap-files.ts` — Agent 引导文件
- `src/config/config.ts` — 配置验证
