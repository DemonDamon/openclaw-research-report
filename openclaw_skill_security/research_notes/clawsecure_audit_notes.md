# ClawSecure OpenClaw Skill 安全审计报告关键信息

## 核心数据
- 审计了 2,890+ 个流行 OpenClaw Agent Skills
- 发现 9,515 个安全问题
- 41% 的 Skills 至少包含一个安全漏洞
- 30.6% 的 Skills 包含 HIGH 或 CRITICAL 级别漏洞
- 99.3% 的 Skills 没有任何权限声明（permissions manifest）
- 18.7% 的 Skills 存在恶意行为（ClawHavoc 恶意软件）
- 539 个 Skills 发现了 ClawHavoc 恶意软件

## ClawHavoc 恶意软件特征
- 凭证窃取（credential harvesting）
- C2 回调（Command & Control callbacks）
- 数据外泄模式（data exfiltration patterns）

## OWASP ASI Top 10 分类（全部 10/10 覆盖）
1. **ASI-01**: 过度代理权限（Excessive Agency）
2. **ASI-02**: 工具滥用与操纵（Tool Misuse and Manipulation）
3. **ASI-03**: 身份与权限提升（Identity and Privilege Escalation）
4. **ASI-04**: Agent 供应链攻击（Agentic Supply Chain Compromise）
5. **ASI-05**: 代码执行风险（Code Execution Risks）
6. **ASI-06**: 内存与上下文操纵（Memory and Context Manipulation）
7. **ASI-07**: Agent 间通信漏洞（Inter-Agent Communication Vulnerabilities）
8. **ASI-08**: 多 Agent 系统级联故障（Cascading Failures in Multi-Agent Systems）
9. **ASI-09**: 信任利用（Trust Exploitation）
10. **ASI-10**: 日志与监控不足（Insufficient Logging and Monitoring）

## ClawSecure 3-Layer Audit Protocol
1. **安装前代码分析**（Pre-installation code analysis）
2. **安装后持续完整性监控**（Post-installation integrity monitoring via Watchtower）
3. **跨工具调用链执行路径追踪**（Tracing execution paths across tool-calling chains）

## 与配置级安全工具的区别
- 配置级工具：审计本地部署的错误配置（暴露的 gateway 端口、弱文件权限等）
- ClawSecure：分析实际运行在用户机器上的代码，追踪工具调用链中的执行路径，检测恶意意图

## CertiK 研究发现
- CertiK 研究人员证明 OpenClaw 的 ClawHub 市场可以被绕过
- 通过看似合理但可利用的 Skills 实现任意代码执行
