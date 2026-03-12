# PI Agent 深度技术问题清单（基于源码分析）

基于对 `src/agents/` 目录下 6000+ 行核心代码的深入分析，提出以下 10 个深度技术问题：

## Q1: 双层队列串行化与 Lane 隔离机制
`runEmbeddedPiAgent` 中使用了 `enqueueSession` 和 `enqueueGlobal` 双层嵌套队列。`resolveSessionLane` 为每个 sessionKey 创建独立的 `session:xxx` lane，而 `resolveGlobalLane` 使用 `CommandLane.Main`。这种双层队列的设计意图是什么？Session Lane 和 Global Lane 之间的并发语义是什么——是串行化还是并行？当多个 session 同时活跃时，Global Lane 是否会成为瓶颈？`CommandLane` 的底层实现是基于 mutex、semaphore 还是 async queue？

## Q2: Context Overflow 三级恢复策略
代码中存在三种处理 context overflow 的策略：(1) `truncateOversizedToolResultsInSession` 截断过大的工具结果；(2) `compactEmbeddedPiSessionDirect` 显式压缩会话；(3) SDK 内部的自动压缩（`attemptCompactionCount`）。这三种策略的触发顺序和优先级是什么？`MAX_OVERFLOW_COMPACTION_ATTEMPTS=3` 的三次尝试中，每次尝试的策略选择逻辑是什么？当三种策略都失败时，系统如何优雅降级？

## Q3: Auth Profile 轮换与 Cooldown 指数退避的精确机制
`advanceAuthProfile` 在遇到 rate_limit、auth、billing 等不同类型的失败时如何选择下一个 profile？`markAuthProfileFailure` 中的指数退避公式是什么（代码中提到 1m→5m→25m→1h cap 和 billing 的 5h→24h cap）？当所有 profile 都处于 cooldown 状态时，`throwAuthProfileFailover` 的行为是什么——是立即抛出还是等待最短 cooldown 到期？`resolveMaxRunRetryIterations` 中 `BASE_RUN_RETRY_ITERATIONS=24` + `RUN_RETRY_ITERATIONS_PER_PROFILE=8` 的设计依据是什么？

## Q4: System Prompt 构建的分层架构与 PromptMode 策略
`buildAgentSystemPrompt` 接收大量参数构建系统提示词，`PromptMode` 有 `full/minimal/none` 三种模式。`minimal` 模式用于 subagent，具体省略了哪些 section？系统提示词中 Skills、Memory Recall、Reply Tags、Messaging、Workspace Notes 等 section 的组装顺序是什么？`contextFiles`（bootstrap files）如何被注入到提示词中？当提示词总长度接近 context window 时，是否有动态裁剪机制？

## Q5: Tool Policy Pipeline 的七层过滤链
`buildDefaultToolPolicyPipelineSteps` 定义了 7 层工具策略过滤：profilePolicy → providerProfilePolicy → globalPolicy → globalProviderPolicy → agentPolicy → agentProviderPolicy → groupPolicy。每一层的 `deny always wins` 语义是如何实现的？`stripPluginOnlyAllowlist` 的作用是什么？当 plugin 注册了自定义工具时，这些工具如何通过 7 层过滤链？`group:fs`、`group:runtime` 等工具组是如何展开的？

## Q6: Subagent 生命周期管理与深度限制
`subagent-spawn.ts` 中 Subagent 有 `run` 和 `session` 两种模式。两种模式在生命周期管理上有什么区别？`DEFAULT_SUBAGENT_MAX_SPAWN_DEPTH` 的值是多少？当 Subagent 嵌套达到最大深度时如何处理？`registerSubagentRun` 和 `countActiveRunsForSession` 如何防止 Subagent 无限增殖？Subagent 的 workspace 是否与父 agent 共享？Subagent 完成后的 cleanup 策略（`delete` vs `keep`）分别在什么场景下使用？

## Q7: 会话压缩（Compaction）的完整流程与安全超时
`compact.ts` 中的 `compactEmbeddedPiSessionDirect` 实现了会话压缩。压缩的具体算法是什么——是摘要式压缩还是滑动窗口裁剪？`compactWithSafetyTimeout` 和 `EMBEDDED_COMPACTION_TIMEOUT_MS` 的超时值是多少？压缩过程中如何保证不丢失关键的工具调用上下文？`trigger: "overflow" | "manual"` 两种触发方式在压缩策略上有什么区别？压缩后的会话如何保证与原始会话的语义一致性？

## Q8: 流式响应处理与 Block Chunking 机制
`pi-embedded-subscribe.ts` 中的 `subscribeEmbeddedPiSession` 实现了复杂的流式响应处理。`EmbeddedBlockChunker` 的分块策略是什么？`blockReplyBreak: "text_end"` 的含义是什么？系统如何处理 `<thinking>` 标签的流式解析（`blockState.thinking`）？`reasoningMode` 的 `off/on/stream` 三种模式分别如何影响流式输出？`compactionInFlight` 状态如何与流式输出协调——压缩期间是否暂停流式输出？

## Q9: 多 Provider 适配层的差异化处理
代码中对 Anthropic、Google/Gemini、OpenAI、Ollama 等不同 provider 有大量差异化处理：`scrubAnthropicRefusalMagic` 清理 Anthropic 的魔术字符串；`sanitizeSessionHistory`/`sanitizeToolsForGoogle` 处理 Google 的特殊要求；`createOllamaStreamFn` 为 Ollama 创建自定义流函数。每个 provider 的适配层具体处理了哪些差异？`validateAnthropicTurns` 和 `validateGeminiTurns` 分别验证什么约束？`isReasoningTagProvider` 判断哪些 provider 支持 reasoning tag？

## Q10: Session Write Lock 与并发安全
`acquireSessionWriteLock` 和 `resolveSessionLockMaxHoldFromTimeout` 实现了会话级别的写锁。这个锁的粒度是什么——per session file 还是 per session key？锁的实现机制是什么——文件锁、内存锁还是分布式锁？`resolveSessionLockMaxHoldFromTimeout` 如何根据 timeout 计算锁的最大持有时间？当锁超时时如何处理——强制释放还是等待？在 compaction 和 normal run 同时竞争同一个 session 时，锁如何保证数据一致性？`guardSessionManager` 的 wrapper 模式如何防止 session transcript 的并发写入损坏？
