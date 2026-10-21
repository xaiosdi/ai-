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
| ① | `https://cdn.jsdelivr.net/gh/xaiosdi/ai-@main/<路径>` | 首选。免费 CDN，无请求额度限制，支持 CORS |
| ② | `https://raw.githubusercontent.com/xaiosdi/ai-/main/<路径>` | 备选，部分网络不可达 |
| ③ | `https://api.github.com/repos/xaiosdi/ai-/contents/<路径>` | 最后手段。`content` 字段是 base64；匿名仅 60 次/小时 |

> jsDelivr 有缓存（约 12 小时），刚更新的内容可能延迟可见。
