# Q9: 多 Provider 适配层的差异化处理

## DeepWiki 回答摘要

### 多 Provider 适配概述
OpenClaw 通过集中式 transcript 卫生管线和 provider 特定的流处理来适配多个 provider，每个 provider 有验证和清理层。使用 `resolveTranscriptPolicy` 选择 provider 特定的清理/修复规则，通过 `sanitizeSessionHistory` 在每次运行和压缩前应用。策略矩阵位于 `src/agents/transcript-policy.ts`。

### scrubAnthropicRefusalMagic
替换提示词中已知的拒绝触发魔术字符串，防止 Anthropic 的测试拒绝 token 污染会话 transcript。将 `ANTHROPIC_MAGIC_STRING_TRIGGER_REFUSAL` 替换为已编辑的占位符。

### sanitizeSessionHistory
中央 transcript 卫生函数，执行以下操作：
1. 为 provider/model 解析 TranscriptPolicy
2. 注释跨 session 的用户消息
3. 清理图像、工具调用输入和工具结果详情
4. 为 Copilot Claude 丢弃 thinking 块
5. 启用时修复 tool_use/tool_result 配对
6. 为 Responses/Codex API 降级 OpenAI reasoning 块
7. 需要时应用 Google 轮次排序修复
8. 写入模型快照以检测跨运行的模型变更

### sanitizeToolsForGoogle
Google 模型的 provider 特定工具 schema 清理。确保工具定义符合 Google 的期望（如严格的字母数字工具调用 ID）。在工具准备期间调用。

### createOllamaStreamFn
为 Ollama 的原生 `/api/chat` 端点创建自定义流函数，绕过 SDK 的 `streamSimple` 以确保可靠的流式传输和工具调用。当 `model.api === "ollama"` 时使用。

### validateAnthropicTurns 和 validateGeminiTurns

| 验证器 | 功能 |
|--------|------|
| **validateAnthropicTurns** | 通过合并连续的 user 轮次确保 Anthropic 的严格角色交替 |
| **validateGeminiTurns** | 强制 Gemini 风格的轮次交替 |

两者对空或单消息输入都是 no-op。

### isReasoningTagProvider
返回 true 的 provider（需要将 reasoning 包装在标签中而非使用原生 API reasoning 字段）：
- `google-gemini-cli`、`google-generative-ai`
- 包含 `google-antigravity` 的 provider 字符串
- 包含 `minimax` 的 provider 字符串
- **Ollama 被明确排除**（因为它通过 reasoning 字段原生处理）

## 源码引用
- `src/agents/transcript-policy.ts:77-131`
- `src/agents/pi-embedded-runner/run.ts:64-76`
- `src/agents/pi-embedded-runner/google.ts:424-502`
- `src/agents/pi-embedded-runner/run/attempt.ts:81-85, 648-662, 722-727`
- `src/utils/provider-utils.ts:10-36`
