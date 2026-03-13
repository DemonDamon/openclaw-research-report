# HiClaw + CoPaw GitHub 仓库核心信息

## HiClaw (alibaba/hiclaw)
- **Stars**: 1.8k | **Forks**: 201 | **Commits**: 246
- **定位**: An open-source Collaborative Multi-Agent OS for transparent, human-in-the-loop task coordination via Matrix rooms.
- **最新版本**: v1.0.5 (v1.0.4 引入 CoPaw Worker)
- **核心目录**: manager/, copaw/, openclaw-base/, install/, hack/, scripts/, tests/
- **License**: Apache-2.0

### 核心特性
1. **Customizable "Claws"**: 支持 OpenClaw, CoPaw, NanoClaw, ZeroClaw, 或自定义企业 Agent
2. **Manager Claw Role**: 专用 Manager 角色，消除人工管理每个 Worker 的需要
3. **Native Matrix Communication**: 使用 Element IM 客户端和 Tuwunel IM 服务器
4. **Shared File System (MinIO)**: 集成 MinIO 作为共享文件系统
5. **Secure Entry via Higress AI Gateway**: 集中入口管理凭证，降低安全风险

### 企业级安全
- Worker Agents 永远不持有真实 API Keys 或 GitHub PATs
- 只使用 consumer token（类似"工牌"）
- 即使 Worker 被攻破，攻击者也无法获取真实凭证

### Skills 生态
- Workers 可从 skills.sh (80,000+ 社区 skills) 按需拉取
- 安全使用，因为 Workers 无法访问真实凭证

---

## CoPaw (agentscope-ai/CoPaw)
- **Stars**: 11.1k | **Forks**: 1.3k | **Commits**: 287
- **定位**: Your Personal AI Assistant; easy to install, deploy on your own machine or on the cloud
- **核心目录**: src/copaw/, console/, deploy/, scripts/, tests/, website/
- **License**: Apache-2.0

### 核心能力
1. **Every channel**: DingTalk, Feishu, QQ, Discord, iMessage 等多渠道支持
2. **Under your control**: 记忆和个性化在你的控制下，支持本地或云部署
3. **Skills**: 内置 cron，自定义 skills 在 workspace 中自动加载
4. **tool_guard**: 安全特性，确认工具执行前需要审批
5. **支持从 LobeHub 导入 Skills**: feat(skills): support importing skills from lobehub

### 关键安全特性
- `feat(security): add tool_guard to confirm tools` — 工具执行前确认机制
