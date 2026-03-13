# 调研笔记：OpenClaw 与云盘/文档 Skill 生态

> Author: Damon Li | Date: 2026-03-13

## 一、微信公众号文章核心内容

### 文章主题：百度 Skill 生态矩阵与 OpenClaw App 排行

- 百度推出 PaddleOCR 文档解析能力，以 Skill 形式上架 ClawHub
- 可直接嵌入 OpenClaw Agent 工作流
- ClawHub 当前聚合 5700+ 技能模块，周增长率 40%
- 阿里、百度、华为、腾讯四大云厂商已完成平台级布局，合计占据约 75% 的市场

## 二、谷歌 Google Workspace CLI（2026.3.7）

- 谷歌开源 Google Workspace CLI，整合现有云 API
- 内置超 40 项 Agent Skills，完美支持结构化 JSON 输出
- 让 OpenClaw 等 AI 智能体直接读取并自动操作 Gmail、Drive 云盘等核心办公数据
- 支持：加载/创建云端硬盘文件、自动发送邮件、创建/修改日历约会、发送聊天消息
- 谷歌云总监 Addy Osmani 明确指出该工具完美支持结构化 JSON 输出
- 注意：非官方正式支持的商业产品，用户需自行承担风险

## 三、腾讯文档 Skill（2026.3.13）

### 核心定位
腾讯文档面向 AI Agent 平台推出的能力插件，集成于 OpenClaw 框架

### 接入方式
1. 通过云服务安装 OpenClaw（如腾讯轻量云 Lighthouse）或本地部署
2. 微信/QQ 扫码登录腾讯文档网页版
3. 在腾讯文档列表页-更多操作-开放平台中获取配置指令和专属 Token
4. 将配置发送到 QQ、飞书等 IM 消息通道即可激活

### 四大实战场景
1. **资料自动归位**：发送"把这个内容收进腾讯文档"，自动识别内容并归档至指定目录
2. **精准检索**：直接问 Agent，快速定位原文段落并给出关键结论
3. **场景化表格生成**：一句话生成报名表、签到表等，链接一键发群
4. **自动化数据整合**：跨表合并、占比计算、异常检测一次搞定

### 用户反馈问题
- Token 鉴权失败（Header[Mcp_Token] 无效）
- 无法识别个人空间
- 不能创建云盘文件夹（权限问题）

## 四、火山引擎 TOS 网盘工具

- volcengine-storage-tos Skill：管理火山引擎 TOS 存储任务
- 支持策略验证和生成签名 URL
- ArkClaw 使用 TOS 对象存储作为文件系统
- 支持上传本地文件到云端供 AI 读取和分析
- TTS 语音合成 + TOS 存储的联合 Skill

## 五、腾讯云 COS Skill

- TencentCloudCOS Skill：让 OpenClaw 成为云存储管家
- 支持对象存储的完整操作

## 六、ClawHub 热门文件管理类 Skills

- file-manager：文件管理，批量重命名、分类
- doc-coauthoring：文档协作，结构化撰写报告、文案
- skill-vetter：安全审计，扫描技能安全性
- FilesystemManager：本地文件管理，支持文件创建/删除/移动/重命名、批量操作
- Fast.io：文件管理和 RAG（排名第一）
- Email Daily Summary Skill：自动发送/接收邮件、归档、搜索
