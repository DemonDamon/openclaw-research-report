# DeepWiki Configuration Reference 完整摘录（2026.3.7 索引）

## 配置文件路径
`~/.openclaw/openclaw.json` (JSON5 格式)

## 根 Schema 结构 (ConfigSchema)
- meta: MetaSchema
- env: EnvSchema
- wizard: WizardSchema
- diagnostics: DiagnosticsSchema
- logging: LoggingSchema
- cli: CliSchema
- update: UpdateSchema
- gateway: GatewaySchema
- channels: ChannelsSchema
- agents: AgentsSchema
- tools: ToolsSchema
- browser: BrowserSchema
- web: WebSchema
- discovery: DiscoverySchema
- session: SessionSchema
- messages: MessagesSchema
- commands: CommandsSchema
- approvals: ApprovalsSchema
- acp: AcpSchema
- talk: TalkSchema
- bindings: BindingsSchema[]

## Gateway Section
| Field | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| gateway.port | number | 18789 | TCP port |
| gateway.mode | "local"/"remote" | "local" | 操作模式 |
| gateway.bind | "auto"/"lan"/"loopback"/"custom"/"tailnet" | "auto" | 网络绑定 |
| gateway.channelHealthCheckMinutes | number | 5 | 通道健康检查间隔 |

### Gateway Auth
| Field | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| gateway.auth.mode | "none"/"token"/"password"/"trusted-proxy" | "none" | 认证模式 |
| gateway.auth.token | SecretInput | - | Bearer token |
| gateway.auth.password | SecretInput | - | 密码 |
| gateway.auth.allowTailscale | boolean | false | 允许 Tailscale 身份 |

### Gateway TLS
| Field | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| gateway.tls.enabled | boolean | false | 启用 TLS |
| gateway.tls.autoGenerate | boolean | false | 自动生成证书 |
| gateway.tls.certPath | string | - | 证书路径 |
| gateway.tls.keyPath | string | - | 私钥路径 |

### Remote Gateway
| Field | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| gateway.remote.transport | "direct"/"ssh" | "direct" | 连接方式 |
| gateway.remote.url | string | - | 远程 WebSocket URL |
| gateway.remote.tlsFingerprint | string | - | TLS 指纹 |

## Environment Section
| Field | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| env.shellEnv.enabled | boolean | true | 加载 shell 环境变量 |
| env.shellEnv.timeoutMs | number | 5000 | 超时时间 |
| env.vars | Record<string,string> | {} | 显式环境变量覆盖 |

## Channels Section
- 支持: telegram, discord, slack, whatsapp, signal, googlechat, mattermost, imessage, bluebubbles, irc, msteams
- channels.defaults.groupPolicy: "open"/"allowlist"/"disabled" (默认 "allowlist")
- channels.modelByChannel: 按通道 ID 指定模型
- DM Policies: "open"/"pairing"/"allowlist"/"disabled"

## 新增发现的 Section（之前旧版文档未覆盖）
- **wizard**: WizardSchema — 配置向导
- **diagnostics**: DiagnosticsSchema — 诊断配置
- **logging**: LoggingSchema — 日志配置
- **cli**: CliSchema — CLI 配置
- **update**: UpdateSchema — 更新配置
- **browser**: BrowserSchema — Playwright/CDP 浏览器配置
- **web**: WebSchema — Web 相关配置
- **discovery**: DiscoverySchema — 发现服务配置
- **commands**: CommandsSchema — 命令配置
- **approvals**: ApprovalsSchema — 审批配置
- **acp**: AcpSchema — Agent Control Plane (子代理) 配置
- **talk**: TalkSchema — 语音对话配置

## 待深挖问题
1. agents section 完整字段（sandbox.mode, workspace, defaults）
2. tools section 完整字段（tool policy pipeline, sandbox config）
3. models section（auth profiles, fallback chains, cooldown）
4. cron section（定时任务配置）
5. approvals section（审批流配置）
6. acp section（子代理配置）
7. secrets section（SecretRef 机制）
8. bindings section（多代理路由规则）
