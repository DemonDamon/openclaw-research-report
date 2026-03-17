# DeepWiki Tools System 完整配置摘录（2026.3.7 索引）

## Tool 组装入口
- `createOpenClawCodingTools` in `pi-tools.ts`
- 接受 agent identity, model provider, sandbox context, filesystem policy, message routing, policy overrides

## Tool 类别

### Base Coding Tools（来自 @mariozechner/pi-coding-agent）
| Tool name | Replacement | Source |
| :--- | :--- | :--- |
| read | createOpenClawReadTool / createSandboxedReadTool | pi-tools.read.ts |
| write | createHostWorkspaceWriteTool / createSandboxedWriteTool | pi-tools.read.ts |
| edit | createHostWorkspaceEditTool / createSandboxedEditTool | pi-tools.read.ts |
| bash | **Removed entirely** | — |
| exec | Re-added via createExecTool | bash-tools.ts |

### OpenClaw-Native Tools
| Tool group | Tools included |
| :--- | :--- |
| group:runtime | exec, bash, process |
| group:fs | read, write, edit, apply_patch |
| group:sessions | sessions_list, sessions_history, sessions_send, sessions_spawn, session_status |
| group:memory | memory_search, memory_get |
| group:web | web_search, web_fetch |
| group:ui | browser, canvas |
| group:automation | cron, gateway |
| group:messaging | message |
| group:nodes | nodes |
| group:openclaw | All built-in tools |
| 其他 | image, agents_list, tts |

## Tool Policy Pipeline（七层过滤链）

### Policy 优先级（从宽到窄）
1. **Profile policy** (`tools.profile`) — 基础白名单
2. **Provider profile policy** (`tools.byProvider[provider].profile`) — 按 provider 进一步收窄
3. **Global policy** (`tools.allow` / `tools.deny`)
4. **Global provider policy** (`tools.byProvider[provider].allow/deny`)
5. **Agent policy** (`agents.list[].tools.allow/deny`)
6. **Agent provider policy** (`agents.list[].tools.byProvider`)
7. **Group policy** — channel 级别限制
8. **Sandbox tools policy** (`sandbox.tools.allow/deny`)
9. **Subagent policy** — 基于深度的限制

### 额外策略
- **Owner-only policy**: 某些工具仅 senderIsOwner === true 时可用
- **Message provider policy**: 如 tts 在 messageProvider=voice 时排除

## Tool Profiles
| Profile | Included tools |
| :--- | :--- |
| minimal | session_status only |
| coding | group:fs, group:runtime, group:sessions, group:memory, image |
| messaging | group:messaging, sessions_list, sessions_history, sessions_send, session_status |
| full | No restriction (same as unset) |

## Workspace Root Guards
- `tools.fs.workspaceOnly`: true 时限制文件操作在 workspace 内
- Host mode: `wrapToolWorkspaceRootGuard`
- Sandbox mode: `wrapToolWorkspaceRootGuardWithOptions`（含 containerWorkdir 映射）

## Exec Tool 配置
- `tools.exec.approvals`: 审批流配置
- Sandbox 模式下 exec 在 Docker 容器内执行
