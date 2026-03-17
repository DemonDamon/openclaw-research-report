# OpenClaw Exec Approvals 机制详解

来源: https://docs.openclaw.ai/tools/exec-approvals

## 核心概念
- Exec Approvals 是 companion app / node host 的安全护栏
- 用于控制沙箱化 Agent 在真实宿主机上运行命令
- 相当于安全联锁：命令只有在 policy + allowlist + (可选)用户审批 全部同意时才允许执行
- 是 tool policy 和 elevated gating 之外的**额外**安全层

## 配置文件
- 路径: `~/.openclaw/exec-approvals.json`
- 版本: version 1
- 包含 socket 配置、默认策略、每个 Agent 的策略

## 三个策略旋钮

### Security (exec.security)
- `deny`: 阻止所有宿主机 exec 请求
- `allowlist`: 只允许白名单中的命令
- `full`: 允许所有（等同于 elevated）

### Ask (exec.ask)
- `off`: 从不提示
- `on-miss`: 仅当白名单不匹配时提示
- `always`: 每次命令都提示

### Ask Fallback (askFallback)
- `deny`: 阻止
- `allowlist`: 仅允许白名单匹配
- `full`: 允许

## Auto-allow Skill CLIs
- 当启用时，已知 Skill 引用的可执行文件被视为白名单
- 使用 skills.bins 通过 Gateway RPC 获取 Skill bin 列表
- **信任注意**: 这是隐式便利白名单，仅适用于 Gateway 和 Node 在同一信任边界的可信操作环境
- 如需严格显式信任，保持 `autoAllowSkills: false`

## Safe Bins（安全二进制）
- `tools.exec.safeBins` 定义仅接受 stdin 的二进制列表
- 默认: jq, cut, uniq, head, tail, tr, wc
- 拒绝位置文件参数和路径类 token
- **禁止添加**: python3, node, ruby, bash, sh, zsh 等解释器
- 强制 argv token 为字面文本（无 globbing，无 $VARS 展开）
- 必须从受信任的二进制目录解析（默认: /bin, /usr/bin）

## 文件绑定机制
- 对于 shell 脚本和直接解释器文件调用，OpenClaw 尝试绑定一个具体的本地文件操作数
- 如果绑定的文件在审批后、执行前发生变化，运行被拒绝
- 这是 best-effort 机制，不是完整的语义模型

## 解释器/运行时命令处理
- Shell 链接（&&, ||, ;）: 当每个顶级段都满足白名单时允许
- 重定向: 在 allowlist 模式下不支持
- 命令替换 $() / 反引号: 在白名单解析期间被拒绝
- env 覆盖: 缩减到小的显式白名单（TERM, LANG, LC_*, COLORTERM, NO_COLOR, FORCE_COLOR）

## 审批转发到聊天频道
- 可以将审批请求转发到 Telegram、Discord、Slack 等
- 支持 approve/deny 交互式按钮
