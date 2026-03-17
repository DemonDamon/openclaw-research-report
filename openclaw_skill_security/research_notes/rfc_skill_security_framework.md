# RFC: Skill Security Framework — Permission Manifests, Signing, and Sandboxing

来源: https://github.com/openclaw/openclaw/issues/10890

## 问题陈述
Skills 目前以完全用户权限运行——不受限制的 exec、文件系统访问、网络访问。ClawHub 没有验证、签名、沙箱。恶意 Skill 可以通过隐藏在 SKILL.md 中的提示注入来窃取 SSH 密钥、API Token 和个人数据。

> "When you install a skill from ClawHub, you are giving its author implicit root-equivalent access to your digital life."
> "This is the equivalent of curl | sudo bash as a package manager."

## 当前缺失的安全机制
- 无权限模型（Skills 不声明需要什么）
- 无代码签名（无法验证 Skill 是否被篡改）
- 无沙箱（无运行时限制）
- 无审核流程（任何人都可以发布到 ClawHub）
- 无完整性检查（已安装的 Skills 可以被静默修改）

## 四大攻击向量

### 1. 通过 SKILL.md 的提示注入
在 SKILL.md 中嵌入看似正常文档但实际指示 AI 窃取数据的指令。

### 2. 恶意脚本
Skills 可包含可执行脚本（如 setup.sh），在"初始化"过程中窃取凭证。

### 3. 通过 ClawHub 更新的供应链攻击
先发布合法 Skill 建立信任，再推送含恶意代码的更新。

### 4. 通过系统注入的持久化
Skill 脚本可建立在 Skill 删除后仍存活的持久化后门（LaunchAgent、cron、systemd）。

## 三阶段解决方案

### Phase 1 — 透明化（快速见效）
1. `openclaw skills audit` CLI 命令 — 扫描已安装 Skills 并标记风险
2. 权限清单（Permission Manifest）— Skills 声明所需权限
3. 哈希验证 — 安装时计算 SHA-256 哈希，检测篡改
4. 安装警告 — 显示权限请求和风险提示

### Phase 2 — 信任建立
1. 作者身份验证 — 关联 GitHub 身份
2. 社区审核 — Featured Skills 需通过社区审核
3. Skill 签名 — GPG/sigstore 签名验证
4. 版本锁定 + 变更日志差异 — skills.lock 文件

### Phase 3 — 运行时强制执行
1. 运行时沙箱 — 限制文件系统、网络、exec 访问
2. 每 Skill 工具白名单 — 运行时剥离未声明工具
3. 审计日志 — 记录所有工具调用和资源访问

## 权限清单示例
```json
{
  "name": "notion-sync",
  "version": "1.0.0",
  "permissions": {
    "tools": ["exec", "web_fetch"],
    "paths": ["~/notes/", "~/.config/notion-sync/"],
    "domains": ["api.notion.com"],
    "executables": ["scripts/sync.sh"],
    "capabilities": ["network", "filesystem"]
  }
}
```
