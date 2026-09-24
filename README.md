# Zhen Capture

手机与 Mac 共用一套 Capture Core：URL → 平台路由 → 平台/generic 提取 → Normalize → 图片本地化 → 配置的采集目录。提取失败时仍保存分享文案或 URL。

## Mac CLI

```bash
pnpm install
pnpm capture "https://example.com/article" \
  --vault ./test-vault \
  --note "为什么保存这篇文章"
```

默认写入 Vault 内的 `Clippings`。同一 URL 再次运行会返回已有笔记，不会重复创建。CLI 必须显式传入 `--vault`。

## Obsidian 插件

```bash
pnpm install
pnpm build
```

把 `main.js`、`manifest.json`、`versions.json` 放入测试 Vault 的 `.obsidian/plugins/zhen-capture/`，然后启用插件。

### 本地移动端开发验证

仓库自带独立的 `./test-vault`，开发时不要使用正式 Vault。一次核心回归、开发构建和安装到该 Test Vault：

```bash
pnpm dev:verify
```

随后在 Test Vault 重载插件，运行命令面板的 `Zhen Capture: 开发验证：模拟 URL 剪藏`；它与 Android 分享、iOS `obsidian://zhen-capture` 和普通 URI 入口都调用同一个 capture handler。可选的入口标识只用于开发调用链标记，不改变提取或写入规则。桌面可在开发者控制台执行 `this.app.emulateMobile(true)` 模拟 Obsidian 移动界面，结束后执行 `this.app.emulateMobile(false)`；这不模拟 Android/iOS 的系统分享菜单或 URI 唤起。

开发阶段仅修改源码、测试与 Test Vault 开发构建；仅在本地验证完成后，才进入 `release/github-repository` 同步、版本号、tag 或 GitHub Release 流程。

插件设置中可修改 `Capture folder`；默认为 `Clippings`，附件写入该目录下的 `attachments`子目录。

- Mac：命令面板运行 `Zhen Capture: 采集 URL`，或点下载图标。
- Android：系统分享给 Obsidian，在菜单选择 `Zhen Capture` 或 `Zhen Capture + 备注`。
- iPhone：用快捷指令接收分享 URL，然后打开 `obsidian://zhen-capture?url=<URL 编码后的链接>`；备注版再附加 `&note=<编码后的备注>`。

插件 `isDesktopOnly: false`，提取、图片下载和首次落盘都在当前手机的 Obsidian 内完成，不等待 Mac。iOS 原生后台 Share Extension 不运行社区插件，因此统一管线使用快捷指令唤起 Obsidian。

### WeChat Browser Worker（RC，可选）

当微信的静态 HTTP 提取为 partial 时，插件可仅对微信公众号文章调用一个受保护的 Browser Worker。部署前，在 `cloudflare-wechat-worker/` 目录依次执行：`pnpm exec wrangler login`（会要求用户授权）、`pnpm exec wrangler secret put ACCESS_TOKEN`（不要把 token 写进仓库；此操作可能创建部署版本，须先确认）、再执行 `pnpm exec wrangler deploy`。将 Browser binding 命名为 `BROWSER`，并把部署后的 HTTPS `/extract` URL 与同一 token 填入插件设置。Worker 只接受 `https://mp.weixin.qq.com/s/...`，每次使用新的匿名浏览器页；普通网页和其他平台不会调用它。未配置或 Worker 失败时，插件保留原有 partial/分享文案降级。

## 变更日志

每次调整按「入口 → 路由 → 采集 → 清洗 → 入库 → 整理 → 验证 → 发布」阶段记录，详见 [CHANGELOG.md](CHANGELOG.md)。

## 平台能力

- 普通网页：Defuddle。
- 知乎专栏：Defuddle + 页面 JSON 元数据兜底；受限回答降级保存。
- 微信公众号：Defuddle + `#js_content` 校验与元数据兜底。
- 小红书：区分图文与视频；图文保留正文和图片，视频保存标题、作者、原笔记文案并进入统一 video pipeline，不下载视频文件。Mac 上带新鲜 `xsec_token` 的链接可复用 Redbook CLI。
- Bilibili：免登录公开信息 API，保存标题、作者、时间、简介和封面。
- Douyin：视频优先走结构化提取；结构化数据不可用时只补充可靠的标题/description 元数据，不再把整页网页正文作为视频内容保存。
- X / Twitter Article：保留完整长文、正文图片和有效外链，并清除外围 Tweet、评论、互动数字、media viewer 与页面壳。

## 小红书 Backfill

Backfill 复用已安装的 Redbook CLI，只取少量收藏并调用统一 Capture Save：

```bash
pnpm backfill:xhs --vault ./test-vault --limit 3
```

需要 Chrome 中现有的小红书登录态。短期 token 只用于详情请求，不写入 Markdown 或日志；持久化 URL 始终不含 token。
