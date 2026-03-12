# Q1: 双层队列串行化与 Lane 隔离机制

## DeepWiki 回答摘要

**CommandLane** 是一个命名的 FIFO 队列，具有可配置的并发度，按 lane 串行化工作。

### 双层队列工作原理
- `runEmbeddedPiAgent` 从 session key 解析出 `sessionLane`，从 lane 参数解析出 `globalLane`
- 先将 agent run 入队到 session lane，然后在该任务内部再入队到 global lane
- `compactEmbeddedPiSession` 也使用相同模式
- 嵌套保证了 per-session 串行化，同时遵守全局并发上限

### 并发模型
- 每个 lane 最多并行运行 `maxConcurrent` 个任务；默认为 1，可通过 `setCommandLaneConcurrency` 增加
- Session lanes 默认并发度为 1，实际上是 per-session 串行
- Global lane 并发度在启动时配置（如通过 `resolveAgentMaxConcurrent`）
- **结论：per-session 工作是串行的；跨 session 最多并行到 global limit**

### 底层实现
- 实现为进程内 async queue，**不是 mutex 或 semaphore**
- 每个 lane 维护 `queue: QueueEntry[]` 和 `activeTaskIds: Set<number>`
- `drainLane` 在 `activeTaskIds.size < maxConcurrent` 时运行任务
- generation counter 允许 `resetAllLanes` 忽略 pre-reset 任务的完成，避免进程内重启后永久阻塞

### 竞态条件防护
- 通过先入队 per-session lane，每个 session 同一时间只有一个任务执行，防止并发写入 session files/history
- Global lane 限制总并发 agent runs，避免跨 session 的资源耗尽
- 队列模式（collect, steer, followup, interrupt）在 auto-reply 中进一步控制入站消息与活跃 run 的交互

### 补充信息
- `CommandLaneClearedError` 在 lane 被清除时抛出，允许调用者优雅处理取消
- Sub-agents 使用专用的 subagent lane，有自己的并发限制
- 队列通过 OpenTelemetry 发出诊断指标（enqueue/dequeue 计数器、等待直方图）

## 源码引用
- `src/agents/pi-embedded-runner/run.ts:177-180, 193-194`
- `src/agents/pi-embedded-runner/compact.ts:750-756`
- `src/process/command-queue.ts:15-18, 29-36, 73-86, 120-125, 207-221`
- `src/gateway/server-lanes.ts:6-9`
