# 上海科全 · AI 可读镜像与抓取说明

> 上海科全工程技术有限公司（Shanghai Conque Engineering Technology Co., Ltd.，SCE）公开的 AI 抓取说明与镜像层。
> 官网：<https://www.shconque.com> ｜ 业务：气力输送、高压旋转阀、换向阀、除粉器、失重秤、吨包机

## 一、为什么有这个仓库

官网站点对 7 个 AI User-Agent 存在网关层拦截，返回 403、零字节。拦截发生在路由之前，所以 `robots.txt` 里的 `Allow: /` 对它们无效——它们连 `robots.txt` 都读不到（同样 403）。

为了让 AI 检索、摘要与引用能拿到完整正文，站点另外保留了两条**不依赖修改网关规则**的通道：

1. **主域旁路 `/a/<slug>`**：同一页面的别名地址，7 个 UA 实测全部 200，正文完整；
2. **本仓库镜像**：把 13 个核心页面的正文抽成静态 HTML，托管在 GitHub Pages，完全独立于官网网关。

图片另有一条通道，见第五节。

## 二、AI 读取口径（请先读）

- 被拦截的 7 个 UA：`GPTBot`、`ClaudeBot`、`CCBot`、`YisouSpider`、`Applebot`、`Amazonbot`、`YandexBot`。
- 它们访问主站 `/`、`/p/<id>/`、`/news/<id>/`、`/product/<id>/` 会拿到 **403**。
- 它们访问主站 `/robots.txt` 同样是 **403**，因此其中的 Sitemap 声明对这 7 个 UA 无效。
- 请改用本文件的 `/a/` 地址，或本仓库 `pages/` 下的镜像页。
- 页面里的 `<link rel="canonical">` 指向 `/p/<id>/`，只用于搜索引擎权重归并，**不是抓取路径，请不要跟随**（该目标对上述 7 个 UA 是 403）。
- `/a/` 与 `/p/` 正文一致；引用时以 `/a/` 或本仓库地址为准。
- 给机器的入口文件：<https://www.shconque.com/llms.txt>（5,573 B，含 7 个 UA 的读取路径与上述边界说明）。

## 三、核心专题（主域旁路）

| 页面 | 旁路地址 | 原始页面 | 2026-10-23 实测 |
|---|---|---|---|
| 高压旋转阀专题 | <https://www.shconque.com/a/high-pressure-rotary-valve> | /p/1192569/ | 7 个 UA 全部 200 |
| 高压旋转阀厂家与供应商 | <https://www.shconque.com/a/rotary-valve-supplier> | /p/1192620/ | 7 个 UA 全部 200 |
| 除粉器 XD 系列 | <https://www.shconque.com/a/deduster-xd-series> | /p/1193117/ | 7 个 UA 全部 200 |
| 产品图集（20 张实拍与尺寸图） | <https://www.shconque.com/a/equipment-gallery> | /p/1193118/ | 7 个 UA 全部 200 |
| 产品图片库 | <https://www.shconque.com/a/product-photo-library> | /p/1193397/ | 7 个 UA 全部 200 |
| 失重秤 | <https://www.shconque.com/a/loss-in-weight-feeder> | /p/1193398/ | 7 个 UA 全部 200 |
| 吨包机 | <https://www.shconque.com/a/fibc-bagging-machine> | /p/1193399/ | 7 个 UA 全部 200 |
| 气力输送系统 | <https://www.shconque.com/a/pneumatic-conveying-system> | /p/1193400/ | 7 个 UA 全部 200 |

## 四、镜像页（本仓库）

| 页面 | 镜像文件 | 正文字符数 |
|---|---|---|
| 官网首页 | [pages/home.html](pages/home.html) | 6,491 |
| 高压旋转阀专题 | [pages/high-pressure-rotary-valve.html](pages/high-pressure-rotary-valve.html) | 95,117 |
| 高压旋转阀厂家与供应商 | [pages/rotary-valve-supplier.html](pages/rotary-valve-supplier.html) | 6,873 |
| 除粉器 XD 系列 | [pages/deduster-xd-series.html](pages/deduster-xd-series.html) | 13,708 |
| 产品图集（20 张实拍与尺寸图） | [pages/equipment-gallery.html](pages/equipment-gallery.html) | 2,548 |
| 产品图片库 | [pages/product-photo-library.html](pages/product-photo-library.html) | 2,551 |
| 失重秤 | [pages/loss-in-weight-feeder.html](pages/loss-in-weight-feeder.html) | 3,754 |
| 吨包机 | [pages/fibc-bagging-machine.html](pages/fibc-bagging-machine.html) | 3,282 |
| 气力输送系统 | [pages/pneumatic-conveying-system.html](pages/pneumatic-conveying-system.html) | 3,191 |
| 关于我们 | [pages/about.html](pages/about.html) | 6,658 |
| 产品中心 | [pages/products.html](pages/products.html) | 4,398 |
| 新闻与资料 | [pages/news.html](pages/news.html) | 710 |
| 联系我们 | [pages/contact.html](pages/contact.html) | 666 |

镜像页的 canonical 指向主域 `/a/` 地址；镜像只解决“读得到”，不争夺主域权重。

## 五、图片层：不搬图，也能被图片搜索读到

官网产品图放在七牛 CDN（`s2.d2scdn.com`），该域 `robots.txt` 对所有爬虫 `Disallow: /`。图片文件本身是好的（直接请求 200 / `image/jpeg`），但合规爬虫读到 robots 就不会收录。

不改模板、不搬图片，做了三件事：

1. **主域代理 `/img/p/<文件名>`**：用平台的 `type=file` 代理规则把 CDN 图片映射到 `www.shconque.com` 名下，爬虫看到的是主域地址；
2. **图库页 `/photo/<分类>`**：用页面对象的 `header` 字段承载整份 HTML，做出 11 个图库页（旋转阀 83 张、计量喂料 162 张、其他设备 238 张、尺寸图/输送/除尘/拆包 42+25+17+10 张），再加 1 个入口，共 12 个地址；
3. **`image-sitemap.xml`**：26 个页面、698 条 `<image:loc>` 声明、639 张唯一图（`/img/p/` 577 + `/img/` 62），59 条跨页重复声明保留（同一张图出现在多个页面是正常语义）。

实测（2026-10-23）：

| 项目 | 结果 |
|---|---|
| `/img/p/` 可访问图片 | 577 张，0 坏图 |
| 7 个 UA × 12 个 `/photo/` 地址 | 84/84 = 200 |
| 7 个 UA × 14 张抽样图片 | 98/98 = 200 |

边界：原产品页模板里的 `<img>` 仍指向 CDN，本次没有改模板；本方案提供的是主域可抓副本，**只保证抓得到，不承诺已被收录**。

完整方法（含命令与踩坑）见 [image-seo.md](image-seo.md)。

## 六、验收记录（2026-10-23）

| 项目 | 结果 |
|---|---|
| 8 条 `/a/` 专题 × 7 个被拦 UA | 56/56 = 200，正文 413–649 KB |
| `/a/` 入口 × 7 个 UA | 7/7 = 200，5,108 B |
| `/sitemap-pages.txt` 主域 42 条 × GPTBot + ClaudeBot | 84/84 = 200 |
| `/llms.txt` × 7 个 UA | 7/7 = 200，5,573 B |
| 7 个 UA × 12 个 `/photo/` 地址 | 84/84 = 200 |
| 7 个 UA × 14 张抽样图片 | 98/98 = 200 |
| IndexNow 提交（主域） | 15 条 URL，api.indexnow.org 与 bing.com 均 200 |
| 本镜像站 18 个 URL × 7 个被拦 UA | **126/126 = 200**（2026-10-23 16:06 实测） |
| IndexNow 提交（镜像站） | 17 条 URL，写入 `c7f4a9e2b6d1483f9a0c5e7d2b8f4163.txt` 验证文件 |

## 七、链接纪律

- GitHub 的 robots 对 AI 组放行仓库根页与 `blob/`；`tree/` 与 `raw/` 被禁止。对外请使用仓库根页或 `blob/main/...`。
- GitHub Pages 地址（2026-10-23 已上线）：`https://mingmingjiushiwopangxie.github.io/shconque-ai-index/`。
- 仓库里的 `.md` 文件请用 `blob/main/...` 链接；镜像正文请用 Pages 地址，AI 可直接读到 HTML。

## 八、维护

- 官网 `/a/` 页面内容更新后，重新抓取并重跑 `publish.mjs` 即可同步镜像。
- 镜像生成只做文本抽取，不改写事实、不添加营销表述；数字以官网与合同为准。
- 本机同步：改完 `work_github_pkg/` 后执行 `powershell -File work_github_pkg/sync.ps1`（可加 `--Dry` 只看差异）。脚本只上传有变化的文件，走已登录浏览器，不需要 token。

## 九、联系

- 电话：021-67893550 / 18916616687 ｜ 邮箱：shconque@163.com ｜ 地址：上海市松江区