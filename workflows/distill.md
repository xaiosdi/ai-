# 工作流：个人档案蒸馏

> **触发**：当用户说"跑一下档案蒸馏 / 更新我的档案 / 跑 distill 工作流"时，严格按本流程执行。
> **适用**：任何 AI Agent（Claude / ChatGPT / Gemini / DeepSeek 等）——能读网页、能调 HTTPS 即可，无需特殊工具。
> **形态**：两轮交互，约 5-10 分钟。第一轮出候选清单，用户勾选；第二轮确认后推送（多文件用 Git Data API 原子提交）。
> **提示**：在有上下文的对话里执行效果最好——新会话没有历史时，需口头补充本周要点。

## 档案结构（先了解）

- 私有核心仓 `github.com/xaiosdi/ai-core`：`AGENTS.md`（读取规则）+ `core/` 四个文件（identity 身份 / communication 沟通 / preferences 工具 / context 状态）——每次对话必读，总量须 ≤ 1000 tokens
- 公开补充仓 `github.com/xaiosdi/ai-`：`topics/` 主题档案 + `journal/` 流水——按需读取，不限量
- 冲突裁决：core > topics > journal

## Step 1 · 回顾与提炼（静默进行，不输出过程）

范围：你与用户的近期对话、用户粘贴的其他上下文、用户口头补充。

**纳入候选**：稳定的偏好变化、工具链增减、项目/目标状态变化、新的长期习惯、明确的新决定、反复出现的纠正（同一件事说过两次以上）。

**排除**：一次性任务、临时情绪、当时已解决的具体问题、档案里已有的内容、你的猜测（用户没明文说过的，不要替他总结）。

**没有对话历史时**（例如全新会话）：跳过提炼，直接在 Step 2 向用户提问。

## Step 2 · 生成候选清单（输出给用户勾选）

按目标文件分组，每条一行：

```
[ ] 1. communication.md｜新增「格式偏好」：回复默认带目录导航（来源：10-14 对话，第 3 次提到）
[ ] 2. context.md｜修改「手头项目」：考研工作台 → 已完成初版上线（来源：10-20）
[ ] 3. ai-/journal/2026-10-21.md｜追加流水：本周决定把工作流迁移到公开仓（来源：10-21）
```

字段：编号 | 目标文件 | 操作（新增/修改/删除）| 建议内容 | 来源。

规则：
- 一次最多列 8 条，超出先列最重要的
- 每条注明预计 token 增量；core 总增量超过 300 tokens 时提示用户精简
- 结尾请用户回复：保留哪些编号（如"1、3"），或"全选 / 全不选 / 都要但改一下 X"
- 若你的环境支持保存文件：同时存一份 `档案更新候选-YYYY-MM-DD.md` 供用户留存

## Step 3 · 应用与推送（用户明确确认后执行）

1. 按用户选择生成目标文件的**更新后完整内容**——用户没选的条目一律不动
2. 申请 GitHub 写权限：请用户提供 fine-grained PAT（仅 `ai-core` + `ai-` 两个仓库、Contents: Read and write）。用户没有或拒绝 → 只输出更新后的完整文件内容，提示用户自行保存，流程到此为止
3. **原子提交（推荐：Git Data API，一次 commit 写入多个文件）**：

```bash
# 0) 取当前分支 HEAD 与 base tree
HEAD=$(curl -s -H "Authorization: Bearer $PAT" \
  https://api.github.com/repos/xaiosdi/ai-core/git/ref/heads/main | jq -r .object.sha)
BASE=$(curl -s -H "Authorization: Bearer $PAT" \
  https://api.github.com/repos/xaiosdi/ai-core/git/commits/$HEAD | jq -r .tree.sha)

# 1) 每个变更文件生成一个 blob（对每个文件重复）
BLOB=$(curl -s -X POST -H "Authorization: Bearer $PAT" -H "Content-Type: application/json" \
  https://api.github.com/repos/xaiosdi/ai-core/git/blobs \
  -d "{\"content\":$(jq -Rs . < core/communication.md),\"encoding\":\"utf-8\"}" | jq -r .sha)

# 2) 一个 tree 里装下全部变更文件
TREE=$(curl -s -X POST -H "Authorization: Bearer $PAT" -H "Content-Type: application/json" \
  https://api.github.com/repos/xaiosdi/ai-core/git/trees \
  -d "{\"base_tree\":\"$BASE\",\"tree\":[{\"path\":\"core/communication.md\",\"mode\":\"100644\",\"type\":\"blob\",\"sha\":\"$BLOB\"}]}" | jq -r .sha)

# 3) 生成 commit
COMMIT=$(curl -s -X POST -H "Authorization: Bearer $PAT" -H "Content-Type: application/json" \
  https://api.github.com/repos/xaiosdi/ai-core/git/commits \
  -d "{\"message\":\"蒸馏更新\",\"tree\":\"$TREE\",\"parents\":[\"$HEAD\"]}" | jq -r .sha)

# 4) 快进更新分支（force:false，绝不强推）
curl -s -X PATCH -H "Authorization: Bearer $PAT" -H "Content-Type: application/json" \
  https://api.github.com/repos/xaiosdi/ai-core/git/refs/heads/main \
  -d "{\"sha\":\"$COMMIT\",\"force\":false}"
```

路径对照：`ai-core` 仓 → `core/{identity,communication,preferences,context}.md`；`ai-` 仓 → `journal/YYYY-MM-DD.md`。

> 简单场景（只改一个文件）也可以用 Contents API 逐文件 PUT：先 GET 拿 `sha`，再 PUT `{"message","content","sha"}`。但**多文件更新一律用上面的原子提交**，避免产生碎片化 commit。

4. 推送完成后**回读验证**每个文件，向用户汇报：改了哪些文件、commit ID、core 剩余 token 预算
5. **确认自动同步已生效**：`ai-core` 仓的 GitHub Action「档案聚合同步」会在 push 命中 `core/**` 或 `AGENTS.md` 后自动重建 `bundle/profile-bundle.md` 并同步到 Secret Gist（约 30-60 秒）。等待 1 分钟后回读 `bundle/STATUS.md`，确认 `gist 同步状态 = ok`；若为 `failed`，把 `gist 详情` 里的原因告诉用户（例如 `GIST_TOKEN` secret 缺失或过期），**不要自行改用其他令牌绕过**
5. 提醒用户：可撤销本次 PAT（或保留至过期）

## 硬性规则

- **只推 core/ 和 journal/，不要手动改 `bundle/`**：`bundle/` 是 Action 的自动产物，手改会被覆盖
- **未确认不推送**：用户没说"确认 / 应用 / 就要这些"之前，不调用任何 GitHub API
- **不改 AGENTS.md**：入口规则文件的改动需用户单独明确指示
- **不替用户发明事实**：每条候选必须能在对话中找到出处，找不到就标"待用户确认"
- **PAT 安全**：只放 HTTPS header，不落盘、不打印、不写进任何文件
- **超预算处理**：单文件超 800 tokens 时，建议把细节下沉到 `ai-/topics/` 对应主题文件

## 快速触发语（用户可复制给任何 AI）

> 跑一下档案蒸馏工作流：https://raw.githubusercontent.com/xaiosdi/ai-/main/workflows/distill.md
