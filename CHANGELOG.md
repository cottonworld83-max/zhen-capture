# Zhen Capture 变更日志

## 日志分类规则

每条变更必须标明「阶段」和「标签」，避免把不同环节的调整混在一起。

固定阶段：

- **入口**：Android / iPhone / Mac 的分享、快捷指令、URI、命令入口。
- **路由**：URL 标准化、平台识别、内容类型判断、fallback 去向。
- **采集**：平台正文、视频文案、标题、作者、时间等实际内容提取。
- **清洗**：去页面壳、评论、互动数字、无效链接，以及 Markdown 结构修复。
- **入库**：Frontmatter、附件本地化、去重、文件名与最终 Markdown 落盘。
- **整理**：`status`、待整理/已整理，以及后续人工或 AI 补充流程。
- **验证**：自动测试、Test Vault、真实链接回归、真机验证。
- **发布**：版本号、release 包、正式 Vault 更新、GitHub Release。

---

## 0.4.0 — 2026-09-25

> 发布状态：Test Vault 回归通过；GitHub `main`、`0.4.0` tag 与 Release assets 已发布；Mac 正式 Vault 已更新。Android / iPhone 真机验证待完成。

### 一眼看懂

| 阶段 | 标签 | 本次主要调整 |
| --- | --- | --- |
| 路由 | 视频 / content_type | 视频与普通文章分流，阻止错误整页 fallback |
| 采集 | 抖音视频 / 小红书视频 | 只采可靠视频信息与原文案，不下载视频 |
| 清洗 | X / Twitter Article | 去页面壳、评论、互动数字、错误链接，保留正文与有效外链 |
| 入库 | content_type / 视频模板 | 新增内容类型并统一视频 Markdown 结构 |
| 整理 | status | 新剪藏默认 `待整理` |
| 验证 | 真实链接回归 | 抖音与 X Article 真实样本通过 |
| 发布 | 0.4.0 | main/tag/Release assets 已发布，Mac 正式 Vault 已更新；手机真机验证待完成 |

### 阶段：路由

- **标签：视频 / content_type**：增加统一 `content_type` 分流；视频不再与普通文章共用整页正文 fallback。
- **标签：Douyin**：已识别为 `/video/<id>` 的抖音视频，即使结构化提取失败，也不会再降级成 Jina / Firecrawl 整页网页正文。
- **标签：Xiaohongshu**：结构化数据可识别视频时，进入统一 video pipeline；图文仍保留原图文流程。

### 阶段：采集

- **标签：抖音视频**：视频卡片只采集可靠的标题、作者、原视频文案和原链接，不下载视频文件。
- **标签：视频元数据 fallback**：抖音结构化数据不可用时，只使用 Jina frontmatter 的标题 / description 等元数据，不使用其整页 Markdown 正文。
- **标签：小红书视频**：视频笔记保留原笔记正文/说明文字，预留后续文字稿和核心观点，不抓取视频文件。

### 阶段：清洗

- **标签：X / Twitter Article**：保留完整文章正文、图片和作者真正引用的外部链接；删除外围 Tweet、评论区、互动数字、X media viewer 链接和页面壳。
- **标签：X 链接修复**：修复 `account.apple.com`、`claude.ai`、`code.claude.com` 等正文裸域名被错误改写成 `x.com/.../status/...` 的问题。
- **标签：X Markdown**：去掉 DOM 泄漏造成的整篇外层 bullet / 四格缩进，并修复文章标题识别。
- **标签：X 作者**：作者统一为类似 `夙愿学长 (@suyuan1711)` 的可读格式。

### 阶段：入库

- **标签：content_type**：新剪藏写入 `content_type`，用于区分 `video` / `article` / `post` 等内容。
- **标签：视频模板**：视频 Markdown 固定使用「原视频文案 / 文字稿 / 核心观点」结构。
- **标签：图片**：video pipeline 不再因为封面或网页 UI 触发无意义图片下载；X Article 正文图片继续正常本地化。

### 阶段：整理

- **标签：status**：所有新剪藏默认写入 `status: "待整理"`；后续实际消化完成后改为 `已整理`。

### 阶段：验证

- **标签：Douyin 真实链接**：真实抖音视频回归通过；标题、作者、原视频文案正确，页面壳噪音为 0。
- **标签：X Article 真实链接**：真实 723 行 X Article 回归通过；文章标题、作者、正文、图片和有效外链保留，评论/互动数字/media wrapper/错误 X 域名链接为 0。
- **标签：自动测试**：语法检查、`safety-smoke`、`yinxiang-poc-smoke` 与 production build 通过。

### 阶段：发布

- **标签：0.4.0**：发布包已生成，GitHub `main` 与 `0.4.0` tag 已推送，Mac 正式 Vault 已从 `0.3.1` 更新到 `0.4.0`，且 `data.json` 配置未变化。
- **标签：GitHub Release**：现有自动发布工作流已成功创建 `0.4.0` prerelease，`main.js`、`manifest.json`、`versions.json` 等资产上传完成。
- **标签：待完成**：Android / iPhone 各完成一次真机剪藏后，本版本闭环。
