# OpenClaw v2026.3.13-1 Release Notes - 安全相关变更摘要

## 直接安全相关 PR
1. **security(docker): prevent gateway token leak in Docker build context** - @xingsy97 in #44956
   - 防止 Docker 构建上下文中的 gateway token 泄露
2. **fix(telegram): thread media transport policy into SSRF** - @obviyus in #44639
   - Telegram 媒体传输策略中的 SSRF 修复
3. **fix(ui): keep shared auth on insecure control-ui connects** - @velvet-shark in #45088
   - 修复不安全连接下的共享认证问题
4. **macOS: respect exec-approvals.json settings in gateway prompter** - @sliekens in #13707
   - macOS 上尊重 exec-approvals.json 设置（执行审批配置）
5. **fix(agents): rephrase session reset prompt to avoid Azure content filter** - @xingsy97 in #43403
   - 避免 Azure 内容过滤器触发
6. **fix(gateway/ui): restore control-ui auth bypass and classify connect failures** - @sallyom in #45512
   - 修复 control-ui 认证绕过问题
7. **fix(macos): prevent PortGuard from killing Docker Desktop in remote mode** - @teslamint in #13798
   - 防止 PortGuard 误杀 Docker Desktop

## Skill/Plugin 相关
1. **Plugins: fail fast on channel and binding collisions** - @vincentkoc in #45628
   - 插件通道和绑定冲突时快速失败
2. **perf(build): deduplicate plugin-sdk chunks to fix ~2x memory regression** - @TarasShyn in #45426
   - 插件 SDK 内存优化
3. **[codex] Polish sidebar status, agent skills, and chat rendering** - @BunsDev in #45451
   - Agent Skills 界面优化

## 浏览器安全加固
1. **fix(browser): normalize batch act dispatch for selector and batch support** - @vincentkoc in #45457
2. **fix(browser): follow up batch failure and limit handling** - @vincentkoc in #45506
3. **fix(browser): harden existing-session driver validation and session lifecycle** - @odysseus0 in #45682

## 其他安全相关
1. **fix(agents): preserve blank local custom-provider API keys after onboarding** - @frankekn in #45631
   - 保护本地自定义 API Keys
2. **fix(agents): avoid injecting memory file twice on case-insensitive mounts** - @Lanfei in #26054
   - 防止内存文件重复注入
3. **docker: add apt-get upgrade to all Dockerfiles** - @jacobtomlinson in #45384
   - Docker 镜像安全更新
4. **Gateway: treat scope-limited probe RPC as degraded reachability** - @joshavant in #45622
5. **fix(gateway): bound unanswered client requests** - @Takhoffman in #45689

## 关键发现
- exec-approvals.json 是一个执行审批配置文件，可能与 Skill 执行权限控制相关
- Plugin 通道冲突检测机制（fail fast）是新增的安全防护
- 浏览器会话生命周期加固可能影响 Skill 使用浏览器的安全性
- Docker 构建上下文 token 泄露修复直接关系到容器化部署安全
