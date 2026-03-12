# Q7: 会话压缩（Compaction）的完整流程与安全超时

## DeepWiki 回答摘要

### 压缩算法类型：摘要式（Summary-based）
`compactEmbeddedPiSessionDirect` 最终调用 Pi SDK 的 `session.compact(params.customInstructions)`。这是摘要式压缩，创建持久化的 compaction 条目并保留 `firstKeptEntryId` 之后的消息。**不是**滑动窗口——旧消息被摘要而非简单丢弃。

### EMBEDDED_COMPACTION_TIMEOUT_MS
该常量定义在 `compaction-safety-timeout.ts` 中，用于限制压缩运行时间和 session lock 持有时间，防止死锁。

### compactWithSafetyTimeout 工作机制
`compactWithSafetyTimeout` 将异步压缩函数包装在 AbortSignal 强制的超时中。如果底层 `session.compact` 超过超时时间，操作将被中止并失败。

### overflow vs manual 触发差异

| 触发方式 | 场景 | 说明 |
|---------|------|------|
| **overflow** | context overflow 错误时自动触发 | run.ts 中 overflow 恢复设置 `trigger: "overflow"` |
| **manual** | `/compact` 命令或显式调用 | 默认值，用户主动触发 |

trigger 值用于诊断日志记录。

### 保持工具调用上下文与语义一致性
压缩前的完整处理链：
1. **清理与验证**：`sanitizeSessionHistory` 和 provider 特定验证（`validateGeminiTurns`、`validateAnthropicTurns`）清理历史
2. **历史限制**：`limitHistoryTurns` 截断到配置的轮次限制（可能产生孤立的工具结果）
3. **修复工具配对**：`sanitizeToolUseResultPairing` 在截断后重新配对孤立的工具结果，维护工具调用上下文
4. **持久化摘要**：压缩摘要被持久化，近期消息保持完整，确保后续轮次的语义连续性

### 补充说明
- 压缩算法依赖 Pi SDK 内部的摘要器；OpenClaw 配置设置和安全保障但不实现摘要器本身
- 工具结果截断是 run.ts 中的独立回退路径

## 源码引用
- `src/agents/pi-embedded-runner/compact.ts:61-63, 115, 250-251, 515-519, 585-614, 660-662`
- `src/agents/pi-embedded-runner/compaction-safety-timeout.ts`
- `src/agents/pi-embedded-runner/run.ts:668-669, 682-702`
