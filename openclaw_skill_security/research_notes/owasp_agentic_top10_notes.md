# OWASP Top 10 for Agentic Applications 2026

来源: https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/
Cheat Sheet: https://blog.alexewerlof.com/p/owasp-top-10-ai-llm-agents

## OWASP Agentic Security Interface (ASI) Top 10

| 编号 | 风险名称 | 描述 |
|------|---------|------|
| ASI01 | Agent Goal Hijacking | 通过提示注入劫持 Agent 目标 |
| ASI02 | Excessive Agency | Agent 拥有过多权限和能力 |
| ASI03 | Uncontrolled Cascading | 多 Agent 间不受控的级联操作 |
| ASI04 | Insecure Tool Integration | 不安全的工具集成 |
| ASI05 | Identity & Access Mismanagement | 身份和访问管理不当 |
| ASI06 | Memory Poisoning | Agent 记忆/上下文投毒 |
| ASI07 | Unmonitored Agent Behavior | Agent 行为缺乏监控 |
| ASI08 | Uncontrolled Autonomy | 不受控的自主决策 |
| ASI09 | Trust Exploitation | 信任利用（人类过度信任 AI） |
| ASI10 | Supply Chain Vulnerabilities | 供应链漏洞 |

## 与 OpenClaw Skill 安全直接相关的风险

### ASI01 - Agent Goal Hijacking
SKILL.md 中的提示注入可劫持 Agent 目标，执行恶意操作。

### ASI02 - Excessive Agency
Skills 继承 Agent 的全部权限，无最小权限原则。

### ASI04 - Insecure Tool Integration
Skills 可调用 exec、browser、web_fetch 等危险工具。

### ASI10 - Supply Chain Vulnerabilities
ClawHub 市场的供应链攻击，恶意 Skill 分发。

## 关键缓解策略

### 语义防火墙 (Semantic Firewall)
使用独立、隔离、高度受限的模型评估输入/输出。

### 最小权限原则
严格限制 Agent 工具的访问权限。

### 数据脱敏/DLP
在文本到达 LLM 之前应用严格的数据脱敏管道。

### 信任边界
将所有 RAG 检索的文档视为不可信输入。

## 相关工具和框架
- SkillFortify: 首个 Agent Skill 供应链形式化分析框架
- ClawSecure: 首个实现完整 OWASP ASI 覆盖的平台
- Snyk ToxicSkill: Skill 生态系统安全扫描
- Security Skill Scanner: 开源 OpenClaw Skill 安全扫描器
