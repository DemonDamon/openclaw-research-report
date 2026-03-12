# Q3: Auth Profile 轮换与 Cooldown 指数退避

## DeepWiki 回答摘要

### advanceAuthProfile 选择逻辑
`advanceAuthProfile` 从当前索引向前迭代，跳过处于 cooldown 的 profile（`isProfileInCooldown`）。对每个候选者尝试 `applyApiKeyInfo`；成功则更新 `profileIndex` 并重置 thinking 状态。如果候选者失败且不是 locked profile，则继续下一个。如果没有可用 profile，返回 false 触发 failover。此逻辑在 rate_limit、auth、billing 失败后运行，当前 profile 已通过 `markAuthProfileFailure` 标记。

### 精确退避公式

| 失败类型 | 公式 | 序列 | 上限 |
|---------|------|------|------|
| Rate-limit/Auth/General | `60_000 * 5^min(errorCount-1, 3)` | 1m → 5m → 25m → 1h | 1 小时 |
| Billing | `baseMs * 2^min(errorCount-1, 10)`，baseMs 默认 5h | 5h → 10h → 20h → ... | 24 小时 |

### 所有 Profile 都在 Cooldown 时的行为
在模型 fallback 期间，`runWithModelFallback` 检查是否有任何 profile 可用。如果没有，可能会探测主 provider 的 profile（如果最近的 cooldown 即将到期或已过期，按 provider 限流）。如果不适合探测，记录 rate_limit 原因并跳到下一个模型候选。

### Profile 排序与 Cooldown 排序
`resolveAuthProfileOrder` 将可用 profile 放在前面（按类型偏好和 lastUsed 排序），然后附加 cooldown profile（按最近到期排序）。确保轮换优先选择非 cooldown profile，同时保持顺序。

### Cooldown 存储
Cooldown 和 billing disable 存储在 `auth-profiles.json` 的 `usageStats` 中，包含 `cooldownUntil`、`disabledUntil`、`errorCount` 和 `failureCounts` 字段。`clearExpiredCooldowns` 在所有 cooldown 过期时自动清除并重置错误计数器。

### 关于 BASE_RUN_RETRY_ITERATIONS 常量
DeepWiki 表示 `BASE_RUN_RETRY_ITERATIONS=24` 和 `RUN_RETRY_ITERATIONS_PER_PROFILE=8` 在提供的代码片段中未找到，无法确认其设计依据。

## 源码引用
- `src/agents/pi-embedded-runner/run.ts:420-445`
- `src/agents/auth-profiles/usage.ts:177-183, 191-233, 235-246, 259-298, 74-125`
- `src/agents/model-fallback.ts:319-358`
- `src/agents/auth-profiles/order.ts:143-189`
