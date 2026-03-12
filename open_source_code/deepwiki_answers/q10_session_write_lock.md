# Q10: Session Write Lock 与并发安全

## DeepWiki 回答摘要

### acquireSessionWriteLock 机制
`acquireSessionWriteLock` 在给定 sessionFile 旁创建排他的 `.lock` 文件，写入 `{pid, createdAt}`，并在进程内跟踪已持有的锁以支持可重入。冲突时读取锁负载，如果过期（死 pid 或太旧）则移除并重试。返回 release 函数，递减可重入计数并清理文件和句柄。

### resolveSessionLockMaxHoldFromTimeout
从 `timeoutMs` 加上默认 grace（2 分钟）计算 `maxHoldMs`，受最小值和最大值约束。用于设置看门狗强制释放锁的时间阈值。

### 锁粒度与类型

| 属性 | 说明 |
|------|------|
| **粒度** | Per session file（锁路径为 `${sessionFile}.lock`），不是 per sessionKey |
| **类型** | 文件锁（非内存锁或分布式锁），使用排他文件创建（`wx`）和 FileHandle 确保排他性 |
| **可重入** | 默认允许（`allowReentrant: true`），同一进程可多次获取同一锁 |

### 超时语义
- **获取超时**：带重试等待直到 `timeoutMs`。如果锁过期（缺失 pid、死 pid 或太旧），移除并继续获取；否则延迟重试直到超时
- **看门狗强制释放**：周期性运行，强制释放持有时间超过 `maxHoldMs` 的锁（与获取超时是独立机制）

### Compaction vs Normal Run 竞争

| 操作 | 锁获取 | maxHoldMs 来源 |
|------|--------|---------------|
| **Normal Run** | 使用 sessionFile 和从运行 timeoutMs 派生的 maxHoldMs | `attempt.ts:498-503` |
| **Compaction** | 使用相同 sessionFile，用 `EMBEDDED_COMPACTION_TIMEOUT_MS` 计算 maxHoldMs | `compact.ts:514-518` |

两者获取同一个 session-file 锁，因此**串行化访问**。

### guardSessionManager 包装模式
`guardSessionManager` 包装 `SessionManager` 实例以强制工具结果安全性并防止并发 transcript 损坏。通过包装，它在两个逻辑操作在不同时间持有同一文件锁时，序列化或验证对 transcript JSONL 的写入，避免损坏。

### 补充说明
- 锁是进程本地的，不是分布式锁。在多主机部署中，只有 Gateway 主机应拥有 session 文件
- 过期锁清理可通过 `cleanStaleLockFiles` 进行维护

## 源码引用
- `src/agents/session-write-lock.ts:100-112, 114-158, 182-200, 316-371, 373-407, 414-417, 430-444`
- `src/agents/pi-embedded-runner/run/attempt.ts:498-503, 1321-1322`
- `src/agents/pi-embedded-runner/compact.ts:514-520, 531-535`
