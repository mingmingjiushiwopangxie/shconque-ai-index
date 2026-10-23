# AI 抓取通道说明（2026-10-23）

## 1. 现象

主站对 7 个 AI User-Agent 返回 `403`、`Content-Length: 0`，响应头带 `X-Gonode-Log: block by rrule <id>`：

| UA | 规则号 |
|---|---|
| GPTBot | 769 |
| Applebot | 771 |
| YandexBot | 772 |
| ClaudeBot | 784 |
| Amazonbot | 786 |
| YisouSpider | 845 |
| CCBot | 850 |

拦截发生在路由之前：`robots.txt` 的 `Allow: /` 对它无效，静态文件规则也拦不住。

## 2. 旁路原理

D-coding 的 `urlredirect` 在 UA 检查之前生效。把 `/a/<slug>` 指向同一个页面 id（`type: page`），即可得到同一份正文、不同路径：

```
/a/equipment-gallery  →  page 1193118  →  与 /p/1193118/ 同一份 HTML
```

实测：8 条别名 × 7 个 UA = 56/56 返回 200，正文 413–649 KB。

## 3. 镜像层

GitHub Pages 版本把正文抽成静态 HTML，完全脱离官网网关：

| 文件 | 内容 |
|---|---|
| pages/home.html | 官网首页（6491 字符） |
| pages/high-pressure-rotary-valve.html | 高压旋转阀专题（95117 字符） |
| pages/rotary-valve-supplier.html | 高压旋转阀厂家与供应商（6873 字符） |
| pages/deduster-xd-series.html | 除粉器 XD 系列（13708 字符） |
| pages/equipment-gallery.html | 产品图集（20 张实拍与尺寸图）（2548 字符） |
| pages/product-photo-library.html | 产品图片库（2551 字符） |
| pages/loss-in-weight-feeder.html | 失重秤（3754 字符） |
| pages/fibc-bagging-machine.html | 吨包机（3282 字符） |
| pages/pneumatic-conveying-system.html | 气力输送系统（3191 字符） |
| pages/about.html | 关于我们（6658 字符） |
| pages/products.html | 产品中心（4398 字符） |
| pages/news.html | 新闻与资料（710 字符） |
| pages/contact.html | 联系我们（666 字符） |

镜像页 canonical 指向主域 `/a/`，不抢权重；`robots.txt` 全站放行。

## 4. 发现链

```
GitHub 仓库 / GitHub Pages
   └─ llms.txt
        ├─ 主域 /a/ 八条旁路
        ├─ 本仓库 pages/*.html
        └─ 备用镜像 shconque-mirror2.pages.dev（新闻、产品详情）
```

任意一个入口被 AI 抓到，都能走到完整内容。IndexNow 已于 2026-10-23 提交 12 条 URL。

## 5. 边界

- 主站 `/`、`/p/`、`/news/`、`/product/` 对上述 7 个 UA 仍是 403；本仓库不改变这一点。
- 新闻与产品详情无法用 `type: page` 做别名（需要 object_id），只能走镜像。
- 镜像文本抽取自 2026-10-23 的页面快照；官网更新后需重新生成。
