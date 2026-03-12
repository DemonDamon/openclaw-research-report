# Q6: Subagent 生命周期管理与深度限制

## DeepWiki 回答摘要

### Subagent 生命周期
`spawnSubagentDirect` 验证深度（maxSpawnDepth，默认 1）和每 session 活跃子代理数（maxChildrenPerAgent，默认 5），然后创建子 session key 并通过 `callGateway` 以 lane `subagent` 生成子代理。成功后调用 `registerSubagentRun` 记录运行并启动生命周期监控。

### Run vs Session 模式
DeepWiki 指出代码中**没有**定义明确的 "run vs session" 模式。每次 subagent spawn 都创建一个新 session（childSessionKey）和一个由 runId 跟踪的运行。

### 最大生成深度
没有名为 `DEFAULT_SUBAGENT_MAX_SPAWN_DEPTH` 的常量。默认最大深度为 `cfg.agents?.defaults?.subagents?.maxSpawnDepth ?? 1`。

### 防止无限增殖机制

| 机制 | 说明 |
|------|------|
| `registerSubagentRun` | 记录运行（含时间戳）并启动 sweeper/monitor |
| `countActiveRunsForSession` | 统计给定请求者 session 中没有 endedAt 的运行数 |
| `maxChildrenPerAgent` | 默认 5，达到上限时 `spawnSubagentDirect` 拒绝新请求 |

### Workspace 共享
Subagent 在隔离的 session 中运行，拥有自己的 session key。生成逻辑中**没有**自动与父 agent 共享 workspace。

### Cleanup 策略

| 策略 | 行为 |
|------|------|
| **delete** | 完成/归档后通过 `sessions.delete` 删除子 session 和 transcript |
| **keep** | 保留子 session；registry 标记 `cleanupCompletedAt` 但不删除 |

`sessions_spawn` 工具默认使用 `keep`。

## 源码引用
- `src/agents/subagent-spawn.ts:109-125, 148-268, 284-296`
- `src/agents/subagent-registry.ts:256-263, 357-365, 507-552, 766-782`
- `src/tools/sessions-spawn-tool.ts:46-47`
