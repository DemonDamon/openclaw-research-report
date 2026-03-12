# Q4: System Prompt 构建的分层架构与 PromptMode 策略

## DeepWiki 回答摘要

### PromptMode 三种模式

| 模式 | 用途 | 包含的 Section |
|------|------|---------------|
| **full** | 默认，主 Agent | 所有 Section：Tooling、Tool Call Style、Safety、CLI Reference、Skills、Memory Recall、Self-Update、Model Aliases、Workspace、Documentation、Sandbox、User Identity、Date & Time、Workspace Files、Reply Tags、Messaging、Voice/TTS、Silent Replies、Heartbeats、Reactions、Reasoning Format、Runtime |
| **minimal** | Sub-agent | 仅保留 Tooling、Safety、Workspace、Sandbox、Date & Time、Runtime 和注入的 context。省略 Skills、Memory Recall、Self-Update、Model Aliases、User Identity、Reply Tags、Messaging、Voice/TTS、Silent Replies、Heartbeats |
| **none** | 极简 | 仅返回基础身份行："You are a personal assistant running inside OpenClaw." |

### 精确组装顺序（system-prompt.ts:430-573）

1. Base identity line
2. Tooling + Tool Call Style
3. Safety
4. OpenClaw CLI Quick Reference
5. Skills（minimal/none 省略）
6. Memory Recall（minimal/none 省略）
7. OpenClaw Self-Update（minimal/none 省略）
8. Model Aliases（minimal/none 省略）
9. Timezone hint to use session_status
10. Workspace + workspaceNotes
11. Documentation（minimal/none 省略）
12. Sandbox（如果启用）
13. User Identity（minimal/none 省略）
14. Current Date & Time
15. Workspace Files（注入）
16. Reply Tags（minimal/none 省略）
17. Messaging（minimal/none 省略）
18. Voice/TTS（minimal/none 省略）
19. Subagent Context / Group Chat Context（extraSystemPrompt）
20. Reactions（如果配置）
21. Reasoning Format（如果启用）
22. **Project Context with contextFiles 注入**
23. Silent Replies（minimal/none 省略）
24. Heartbeats（minimal/none 省略）
25. Runtime

### contextFiles（Bootstrap Files）注入方式
contextFiles 在主要 section 之后以 "# Project Context" 标题注入。每个有效文件渲染为 "## <filename>"，然后是其内容。如果存在 SOUL.md，会添加特殊指导以体现其人格。这些文件在调用者加载时已通过 `bootstrapMaxChars`（每文件）和 `bootstrapTotalMaxChars`（总计）预截断。

### 动态裁剪机制
`buildAgentSystemPrompt` 本身**不实现**接近 context window 时的动态裁剪。截断在加载 bootstrap files 时通过配置限制（`agents.defaults.bootstrapMaxChars` 和 `agents.defaults.bootstrapTotalMaxChars`）处理。函数静态组装 section；任何 context-window overflow 由外层 session/compaction 逻辑管理。

## 源码引用
- `src/agents/system-prompt.ts:195-252, 387-388, 425-428, 430-573, 576-579, 608-628, 613-622`
- `docs/system-prompt.md:40-46, 73-79`
- `docs/context.md:123-129`
