# Nebius OpenClaw 安全架构与加固指南 - 关键摘录

来源: https://nebius.com/blog/posts/openclaw-security (2026年3月5日)

## 核心架构组件
- **Gateway**: 路由请求和管理编排，单端口 127.0.0.1:18789
- **Clients**: 用户界面（CLI、Web UI）
- **Nodes**: 执行 Agent 工作负载
- **Sessions**: 维护会话状态
- **Tools and Skills**: 扩展 Agent 能力
- **Memory**: 跨交互存储上下文

## 关键安全警告
> "Installing a ClawHub skill is effectively running third-party code on your host, so OpenClaw should be treated as untrusted code execution with persistent credentials."

## 沙箱配置三级别
1. **Off**: 关闭沙箱
2. **Non-main**（默认）: 群聊和次要线程在隔离容器中运行，主会话在宿主机
3. **All**: 所有工具调用都在容器中运行

## 工具策略（Tool Policy）
- 基于白名单（allowlist）控制
- deny 规则优先于 allow 规则
- 多 Agent 场景下，每个 Agent 独立策略
- 一个 Agent 可以只有 read + web_search，另一个可以有 exec 权限

## Elevated 模式
- 标记为 elevated 的工具始终在宿主机运行，即使 Agent 被沙箱化
- 不绕过工具策略，只让特定命令逃逸容器
- 设计上的有意"逃逸口"

## 状态目录
- 默认: ~/.openclaw
- 包含: config, credentials, session histories, agent workspaces, tool caches, memory snapshots
- Agent 会话历史: agents/<agentId>/sessions/

## 安全对比表
| 方面 | OpenClaw (自托管) | 托管平台 | Agent 框架 |
|------|-------------------|----------|------------|
| 部署 | 自己运行 Gateway | 厂商运行一切 | 嵌入库到应用 |
| 更新 | 自己处理升级 | 厂商处理 | 自己管理依赖 |
| 工具控制 | Tool Policy + Sandbox + Elevated | 厂商定义 | 取决于运行时 |
| 生态 | ClawHub Skills | 内置云连接器 | NPM/PyPI 包 |
| 安全责任 | 完全自己 | 与厂商共担 | 完全自己 |

## ClawHub Skills 安全
- Skill 本质上是在宿主机上运行的第三方代码
- 没有内置的代码审计或签名验证机制
- 需要外部工具（如 ClawSecure）进行安全扫描

## 加固建议
1. 启用沙箱模式（sandbox: all）
2. 配置严格的工具策略白名单
3. 设置 Gateway 认证 token
4. 限制网络访问
5. 使用 Docker 隔离
6. 定期审计 Skill 代码
7. 监控异常行为
