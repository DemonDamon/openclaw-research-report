# Q5: Tool Policy Pipeline 七层过滤链

## DeepWiki 回答摘要

### 七层管线架构

`buildDefaultToolPolicyPipelineSteps` 返回七个有序策略步骤，依次过滤工具：

| 层级 | 标签 | 来源 |
|------|------|------|
| 1 | tools.profile (或带 profile 名) | profilePolicy |
| 2 | tools.byProvider.profile | providerProfilePolicy |
| 3 | tools.allow | globalPolicy |
| 4 | tools.byProvider.allow | globalProviderPolicy |
| 5 | agents.{agentId}.tools.allow | agentPolicy |
| 6 | agents.{agentId}.tools.byProvider.allow | agentProviderPolicy |
| 7 | group tools.allow | groupPolicy |

在七层之后，`createOpenClawCodingTools` 中还会追加 Sandbox 和 Subagent 策略。

### Deny Always Wins 语义
在评估过程中，deny 列表优先检查；如果工具匹配任何 deny 模式，无论 allow 条目如何都会被阻止。这在每个策略步骤和整个管线中强制执行。

### stripPluginOnlyAllowlist 机制
目的是防止仅包含插件工具的 allowlist 隐式阻止所有核心工具。当检测到 allowlist 中没有核心工具条目时，将 allowlist 剥离为 undefined 并记录警告。每个步骤默认 `stripPluginOnlyAllowlist: true`。

### 插件注册的自定义工具如何通过七层过滤
插件注册工具时带有 `pluginId` 元数据。`buildPluginToolGroups` 按 pluginId 聚合工具并提供 all 列表。在管线评估期间，`expandPolicyWithPluginGroups` 将 `group:plugins` 和 plugin-name 条目展开为实际工具名。`applyToolPolicyPipeline` 使用 toolMeta 区分插件工具和核心工具并应用展开。

### 工具组展开
`expandToolGroups` 将 `group:*` 条目替换为 `TOOL_GROUPS` 中预定义的工具数组：
- `group:fs` → `["read", "write", "edit", "apply_patch"]`
- `group:runtime` → `["exec", "process"]`

### alsoAllow 机制
`mergeAlsoAllowPolicy` 将 alsoAllow 条目合并到现有策略的 allow 数组中而不替换它，用于向 profile 的基础 allowlist 添加工具。在 `createOpenClawCodingTools` 中，`profilePolicyWithAlsoAllow` 通过将 alsoAllow 合并到 profile 策略中构建。

### 补充说明
- 工具名规范化和别名（如 `bash` → `exec`）在展开和评估期间应用
- Sandbox 和 subagent 策略在七层之后追加

## 源码引用
- `src/agents/tool-policy-pipeline.ts:17-63, 71-106`
- `src/agents/tool-policy.ts:16-33, 84-87, 133-145, 166-185, 187-226, 228-272, 291-299`
- `src/agents/pi-tools.ts:465-485`
