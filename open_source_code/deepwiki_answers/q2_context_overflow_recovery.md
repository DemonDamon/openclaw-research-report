# Q2: Context Overflow 三级恢复策略

## DeepWiki 回答摘要

PI Agent 使用三级恢复策略处理 context overflow，优先级从非破坏性截断到显式压缩再到 SDK 自动压缩。

### 触发顺序与优先级

**第一级：Tool-result 截断（truncateOversizedToolResultsInSession）**
- 当检测到 overflow 且会话中可能存在超过 context window 的工具结果时触发
- 每次 overflow 事件最多运行一次（toolResultTruncationAttempted 守卫）
- 成功后重置 overflowCompactionAttempts = 0 并重试
- 实现在 `src/agents/pi-embedded-runner/tool-result-truncation.ts`

**第二级：显式压缩（compactEmbeddedPiSessionDirect）**
- 当 overflow 持续且当前 attempt 未进行 SDK 自动压缩时触发
- 最多运行 MAX_OVERFLOW_COMPACTION_ATTEMPTS（默认 3）次
- 成功后递增 autoCompactionCount 并重试
- 使用 `trigger: "overflow"` 调用

**第三级：SDK 自动压缩（attempt-level）**
- 在 runEmbeddedAttempt 内部，Pi SDK 在返回 overflow 错误前自动压缩
- 通过 attemptCompactionCount > 0 检测
- 如果 overflow 跟随 in-attempt 压缩，循环可能重试而不进行额外显式压缩

### MAX_OVERFLOW_COMPACTION_ATTEMPTS=3 重试行为
- 循环跟踪 overflowCompactionAttempts 和 toolResultTruncationAttempted
- 每次显式压缩递增计数器；3 次失败后不再尝试
- SDK 自动压缩不递增显式计数器
- 测试验证最多 3 次显式压缩后返回最终 overflow 错误

### 优雅降级
- 当 overflow 无法解决时，返回 `kind: "context_overflow"` 或 `"compaction_failure"` 错误
- 上层（agent-runner-execution.ts）可能重置会话并通知用户增加 reserveTokensFloor
- 用户看到："Context overflow: prompt too large for the model. Try /reset (or /new) to start a fresh session, or use a larger-context model."

## 源码引用
- `src/agents/pi-embedded-runner/run.ts:615-628, 631-677, 685-724, 744-764`
- `src/agents/pi-embedded-runner/compact.ts`
- `src/agents/pi-embedded-runner/tool-result-truncation.ts`
- `src/agents/agent-runner-execution.ts:432-445`
