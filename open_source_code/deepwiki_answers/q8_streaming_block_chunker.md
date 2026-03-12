# Q8: 流式响应处理与 Block Chunking 机制

## DeepWiki 回答摘要

### EmbeddedBlockChunker 概述
`EmbeddedBlockChunker` 管理将流式文本分割为离散的回复块。它缓冲文本 delta 并根据配置的边界发射 chunk，同时处理 thinking 块等特殊标签。

### blockReplyBreak: "text_end"
`blockReplyBreak` 决定流式输出期间何时发射块回复：

| 值 | 行为 |
|---|------|
| **"text_end"** | 在每个 `text_end` 事件时刷新缓冲的 chunk，实现更即时的流式输出 |
| **"message_end"** | 等待整个消息完成后才刷新 |

### Thinking 标签的流式解析
系统通过 `stripBlockTags` 函数解析 thinking 标签：
1. 使用 `THINKING_TAG_SCAN_RE` 检测 `<thinking>`/`</thinking>` 边界
2. 跨 chunk 维护状态以跟踪是否在 thinking 块内
3. 从可见输出中剥离 thinking 内容，同时为 reasoning 流保留

在流式 delta 期间，部分 thinking 标签通过以下方式处理：
```typescript
ctx.emitReasoningStream(extractThinkingFromTaggedStream(ctx.state.deltaBuffer));
```

### reasoningMode 三种模式

| 模式 | 对输出的影响 |
|------|------------|
| **"off"** | reasoning 不包含在输出中；`includeReasoning` 为 false |
| **"on"** | reasoning 作为单独消息包含；`includeReasoning` 为 true，部分回复被抑制 |
| **"stream"** | reasoning 通过 `onReasoningStream` 实时流式传输；`streamReasoning` 为 true |

### compactionInFlight 协调机制
`compactionInFlight` 是一个布尔标志，跟踪消息压缩是否正在进行，防止与流式操作干扰。系统包含重试逻辑（`pendingCompactionRetry` 和关联的 promise 处理）来管理活跃流期间的压缩失败。

### 补充说明
- Chunker 尊重代码围栏，永远不在其中分割
- 延迟的 `text_end` 事件被优雅处理以防止重复
- Thinking 标签解析在 chunk 边界间是有状态的，正确处理跨 delta 分割的标签

## 源码引用
- `src/agents/pi-embedded-subscribe.ts:44, 46-48, 52-53, 67-71, 300-301, 364-396`
- `src/agents/pi-embedded-subscribe.handlers.messages.ts:171-174, 243-249`
