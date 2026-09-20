# Extractor attributions

Zhen Capture's adapters are mobile-safe TypeScript implementations informed by these open-source projects. No browser bridge, Python runtime, or source from an all-rights-reserved project is bundled.

- Generic, registry, Zhihu, WeChat, and lazy-image handling: [markdownload-zh](https://github.com/yuevthins/markdownload-zh) (MIT metadata) and [Defuddle](https://github.com/kepano/defuddle) (MIT).
- WeChat, Xiaohongshu, Bilibili, and Douyin structured extraction patterns: [obsidian-clipper-wechat-feishu](https://github.com/destineylu/obsidian-clipper-wechat-feishu) (MIT).
- WeChat article scoping and Xiaohongshu full-image handling: [Obsidian-Share-to-Save](https://github.com/chenxiccc/Obsidian-Share-to-Save) (MIT).
- Coolapk feed endpoint and field mapping: [coolapk-mcp](https://github.com/Lniosy/coolapk-mcp) (MIT). Zhen Capture uses the public feed page on mobile and does not bundle its Python/MCP runtime or APK-derived authentication blob.
- Xiaohongshu public-share SSR parsing and Zhihu Tardis SSR routes: [ShareXtract](https://github.com/wuaishare/sharextract) (Apache-2.0). The mobile-safe TypeScript adaptation is modified for Zhen Capture; its license is included in `licenses/ShareXtract-LICENSE.txt`.
