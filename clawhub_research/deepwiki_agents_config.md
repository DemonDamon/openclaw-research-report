# DeepWiki Agents Configuration 完整摘录（2026.3.7 索引）

## Agent 核心概念
- Agent ID: `agents.<agentId>` 键名，`"default"` 为基线回退
- 每个 Agent 拥有独立的 workspace 目录、模型配置、工具策略、会话历史
- Agent ≠ Session，一个 Agent 可以有多个并发 Session

## agents.defaults 配置项

| Config path | Purpose |
| :--- | :--- |
| agents.defaults.workspace | 文件操作的工作区根目录 |
| agents.defaults.userTimezone | 注入系统提示词的时区字符串 |
| agents.defaults.timeFormat | auto / 12 / 24 — 时间显示格式 |
| agents.defaults.heartbeat.prompt | Cron 服务发送的周期性心跳轮询消息 |
| agents.defaults.bootstrapMaxChars | 注入工作区上下文文件的单文件大小限制（默认: 20,000） |
| agents.defaults.bootstrapTotalMaxChars | 所有注入工作区文件的总大小上限（默认: 150,000） |

## Session 隔离机制

| 隔离维度 | 机制 |
| :--- | :--- |
| 会话历史 | 每个 session 独立的 `.jsonl` 转录文件 |
| 文件访问 | 基于 agent ID 和 session key 解析的独立 workspaceDir |
| 并发控制 | Per-session command lane（串行化）+ global lane（共享队列） |
| 子代理 | 自动分配 promptMode: "minimal" 并过滤 bootstrap 文件 |

## Workspace 目录
- 由 `resolveRunWorkspaceDir` 解析
- Docker sandbox 模式下分为 Host path 和 Container path
- 上下文文件: AGENTS.md, SOUL.md, MEMORY.md 等
- 大小受 bootstrapMaxChars 和 bootstrapTotalMaxChars 限制

## Embedded Runner 分层调用栈

| Layer | Function | File |
| :--- | :--- | :--- |
| 1. Turn orchestration | runReplyAgent | agent-runner.ts |
| 2. Fallback loop | runAgentTurnWithFallback | agent-runner-execution.ts |
| 3. Model fallback | runWithModelFallback | model-fallback.ts |
| 4. Auth and retry | runEmbeddedPiAgent | run.ts |
| 5. Single attempt | runEmbeddedAttempt | run/attempt.ts |
| 6. Streaming handler | subscribeEmbeddedPiSession | pi-embedded-subscribe.ts |

## 关键代码实体映射
- resolveSessionAgentIds → agent-scope.ts
- SessionManager → pi-coding-agent SDK
- resolveRunWorkspaceDir → workspace-run.ts
- buildAgentSystemPrompt → system-prompt.ts
- createOpenClawCodingTools → pi-tools.ts
- resolveModel → pi-embedded-runner/model.ts
- getApiKeyForModel → model-auth.ts

## 待深挖（需从源码和其他 Wiki 页面获取）
1. tools section 完整配置（tool policy pipeline）
2. models section（auth profiles, fallback chains）
3. sandbox section（mode: off/non-main/all, Docker 配置）
4. cron section（定时任务配置）
5. approvals section（审批流配置）
6. secrets section（SecretRef 机制）
7. bindings section（多代理路由规则）
