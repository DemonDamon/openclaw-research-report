# DeepWiki OpenClaw 配置系统关键信息（2026.3.7 索引）

## 核心配置文件
- 路径: `~/.openclaw/openclaw.json` (JSON5 格式，支持注释和尾逗号)
- Schema: `OpenClawSchema` (Zod 验证)

## 顶层配置 Section
| Section | Schema | Purpose |
| :--- | :--- | :--- |
| gateway | Gateway settings | Port, bind address, auth mode, reload behavior |
| agents | AgentsSchema | Agent list, defaults, workspace paths |
| channels | ChannelsSchema | Channel-specific config |
| models | ModelsConfigSchema | Model providers, auth profiles, fallbacks |
| tools | ToolsSchema | Tool policy, sandbox settings, browser config |
| session | SessionSchema | Session scoping, reset policy, thread bindings |
| messages | MessagesSchema | Message delivery, chunking, media handling |
| bindings | BindingsSchema | Multi-agent routing rules |
| memory | MemorySchema | Memory backend selection (QMD or builtin) |
| plugins | Plugin entries | Extension config and hooks |
| hooks | HooksSchema | Webhook endpoints and mappings |
| cron | Cron settings | Job scheduling and execution |
| browser | Browser config | Playwright/CDP settings, profiles |
| secrets | SecretsConfigSchema | SecretRef definitions (env/file/exec) |

## 配置生命周期
- File Watcher (chokidar) → loadConfig() → parseConfigJson5() → $include resolver → validateConfigObjectWithPlugins() → Secret resolver (SecretRef) → Runtime snapshot
- gateway.reload.mode: hybrid, hot, restart, off

## 需要深挖的页面
- Configuration System (index 14)
- Configuration Reference (index 15/16)
