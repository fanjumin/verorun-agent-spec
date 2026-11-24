# Markdown Drop 发布管线（Agent 直接投递 .md）

> 目标：用户把 `.md` 交给 Agent，Agent 写入授权目录或调用发布工具，系统自动校验、渲染、发布；不再手工进 CMS 编辑。
> 前提：不绕过鉴权、页面产物铁律、SEO/缓存/回滚。

## 1. 两条轨道，不能混

| 轨道 | 用途 | 是否可绕过 CMS | 关键约束 |
| --- | --- | --- | --- |
| A. 内部 Docs 容器 | Agent Spec、开发文档、内部知识库 | 可以 | 必须登录/内网；noindex；独立容器；不进入 tarka.cn 公网 |
| B. tarka.cn 公网文章 | 产品动态、洞察、公开文档 | 不能直接丢目录 | 必须走内容中心/site builder 的 `docs.publish` 适配器；审核、SEO、版本、回滚 |

**结论：**“丢 .md 给 Agent”适合 A；B 需要“Agent 调用发布工具”，而不是“Agent 写 webroot”。

## 2. 推荐架构（A：内部 Docs 容器）

```
用户 .md
  → Agent 调用 docs.publish（或写入 inbox）
  → data/docs/inbox/            # 只写，不可执行
  → docs-publisher（独立进程/容器）
      1. 校验 frontmatter / 扩展名 / 大小 / 编码
      2. 路径归一化，拒绝 .. / symlink / 隐藏文件
      3. Markdown → HTML（mistune/markdown）+ bleach 清洗
      4. 生成 TOC / 锚点 / 代码高亮 / 图片本地化
      5. 写入 data/docs/versions/<hash>/
      6. 原子切换 data/docs/published/current
      7. 更新 index / search / sitemap（内部）
  → docs 容器（MkDocs/Docsify/Flask）只读挂载 published/current
  → 返回 URL + version + hash
```

- Agent 只拿到 `docs.publish` 工具，不拿 shell/scp/任意路径写权限。
- `inbox` 可写不可读执行；`published` 只读；`versions` 只追加。
- 发布失败进 `rejected/`，带原因，不覆盖当前版本。

## 3. 推荐架构（B：tarka.cn 公网文章）

```
Agent 收到 .md
  → docs.publish（MCP/HTTP）
  → 解析 frontmatter
  → 调内容中心创建 content item（content_format=markdown）
  → 调 site_builder 生成页面草稿（只引用 content_id）
  → 人工/Verifier 审核
  → 人工 publish（现有门禁）
  → staticgen/CMS 渲染
```

- 不允许 Agent 直接把 `.md` 写进 `static/` 或站点根目录。
- 不允许新建独立页面/路由/静态 HTML；必须走内容中心 + site builder。
- 公开文章仍然过 SEO、导航、移动端、404、回滚检查。

## 4. Frontmatter 合同

```yaml
---
title: "文章标题"
slug: "agent-spec-overview"        # ^[a-z0-9][a-z0-9-]{1,80}$
category: "agent-spec"
audience: "internal"               # public | internal | private
status: "draft"                    # draft | review | published
date: "2026-11-24"
version: "1.0.0"
tags: ["agent", "spec"]
cover: ""                          # 可选，必须是已批准素材
canonical: ""                      # 可选
---
```

规则：
- `slug` 由系统生成或校验，不能含 `/`、`..`、空格、中文路径。
- `audience=public` 必须人工审批；Agent Spec 默认 `internal`。
- `status=published` 不能由 Agent 单独设置；需要审核/人工发布。

## 5. 路径与 URL

| 输入 | 输出 |
| --- | --- |
| `docs/agent-spec/overview.md` | `/internal/agent-spec/overview` |
| `docs/insights/2026-11-24-xxx.md` | `/insights/2026-11-24-xxx`（public，需审批） |
| `docs/legal/privacy.md` | `/legal/privacy`（public，需审批） |

- 路径只能来自 frontmatter，不让 Agent 传任意路径。
- 目录固定根：`data/docs/`；禁止 `../`、绝对路径、符号链接、隐藏文件。
- 只允许 `.md`；单文件大小上限（如 2MB）；UTF-8。

## 6. 安全

- Markdown 渲染后必须 bleach 清洗；禁用 `<script>`、`onerror`、`iframe`、`style`。
- 图片：本地化/白名单；禁止远程 HTML/脚本；alt 必填。
- 代码块：保留语言标记，不执行。
- 认证：内部 docs 需 admin/owner 登录；`X-Robots-Tag: noindex`。
- 审计：发布人、task_id、源文件 hash、版本、审核人、时间。
- 限流：每 Agent/每用户每分钟 N 次发布。

## 7. 版本与回滚

- 每次发布生成 `versions/<timestamp>-<hash>/`。
- `current` 是指针；发布失败不切换。
- 回滚 = 切回上一版 `current` + 重建 index；不需要改源文件。
- 保留最近 N 版；旧版可归档。

## 8. 索引与缓存

- 重新生成 docs index / category index / search index。
- 计算 ETag（文件 hash），CDN/nginx 缓存可失效。
- 公开文章更新 sitemap；内部 docs 不生成 sitemap。
- 旧 URL 保持 301 或 404，不能循环。

## 9. 实现阶段

| 阶段 | 产出 | 门禁 |
| --- | --- | --- |
| 1. 只读渲染 | `docs.publish` 接收 .md，渲染到 staging，返回预览 | 路径/XSS/编码测试 |
| 2. 版本化 | versions/current、回滚 | 发布失败不覆盖 |
| 3. 内部 docs | 登录保护 + noindex + 搜索 | 未登录 401 |
| 4. 公网适配 | 接内容中心/site builder，人工 publish | 现有发布门禁 |
| 5. 自动化 | 索引/缓存/审计/限流 | 回归通过 |

## 10. 验收用例

- `.md` 含 `<script>` / `onerror` → 清洗，不执行。
- `slug=../../etc/passwd` → 400。
- 符号链接/隐藏文件/非 `.md` → 400。
- `audience=public` 未审批 → 拒绝发布。
- 发布失败 → current 不变。
- 回滚 → 上一版 URL 正常。
- 内部 docs 未登录 → 401；公开站点不可见。
- 公网文章 → 走内容中心 + site builder，不出现独立静态文件。
