# ai- — 公开补充档案

个人核心档案的公开补充部分：主题档案与日常流水，供 AI 按需读取。

- `topics/` — 主题档案（技术背景、项目、兴趣等），每文件一个主题
- `journal/` — 按日期归档的流水记录
- `llms.txt` — 给 AI 的目录索引

本仓库内容敏感度低、可公开。核心档案在另一个私有仓库，通过其 AGENTS.md 指引关联到此处的目录结构。

## 核心档案在哪

核心档案（身份、沟通偏好、工具偏好、当前状态）在**私有仓** `xaiosdi/ai-core`。
任何 AI 读取它最简单的方式是一条**免登录合并链接**（Secret Gist 汇总了 `AGENTS.md` + `core/` 四个文件）——链接在私有仓的 `AGENTS.md` 顶部和 `README.md` 里，需要时去取。

> 该链接不可被搜索引擎枚举，但**拿到链接即可读取**，所以本公开仓库不写它。

## 读取地址（重要）

`raw.githubusercontent.com` 在部分网络环境下不可达（连接被重置）。请按顺序尝试：

| 顺位 | 地址模板 | 说明 |
|---|---|---|
| ① | `https://raw.githubusercontent.com/xaiosdi/ai-/main/<路径>` | 直连最快，但部分网络（国内/企业网/某些沙箱）连接被重置 |
| ② | `https://cdn.jsdelivr.net/gh/xaiosdi/ai-@main/<路径>` | 免费 CDN，无请求额度，支持 CORS；**首次回源会偶发 301 跳转，必须跟随** |
| ③ | `https://api.github.com/repos/xaiosdi/ai-/contents/<路径>` | 最后手段。`content` 是 base64；匿名仅 60 次/小时 |

**关键：遇到 301/302/307 要跟随 `Location` 取第二次**，不跟随就会误判"不可用"。

> jsDelivr 有缓存（最长 12 小时）。要最新内容：优先用 ①，或先访问
> `https://purge.jsdelivr.net/gh/xaiosdi/ai-@main/<路径>` 刷新缓存。
>
> 列目录（不取文件）可用 `https://data.jsdelivr.com/v1/packages/gh/xaiosdi/ai-@main?structure=flat`，
> 不跳转、不耗 GitHub 额度。
