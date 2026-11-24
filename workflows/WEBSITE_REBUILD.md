# WEBSITE_REBUILD.md — tarka.cn 重构流程

> 发布是人工动作；Agent 只到“草稿 + 检查清单 + 证据”。

| 阶段 | 做什么 | 负责 | 产出 |
| --- | --- | --- | --- |
| 0. 事实包 | 产品名、能力边界、数据来源、可公开案例、合规口径 | 业务+合规 | `TARKA_WEBSITE_BRIEF.md` |
| 1. 结构 | 页面树、每页目的、内容 ID | 业务+内容 | 页面清单 + 内容中心条目 |
| 2. 草稿 | Builder 用 `sb_*` 写草稿、导航、SEO | Builder | 草稿 + checklist |
| 3. 复核 | Verifier 只读复核 | Verifier | `review.md` |
| 4. 人工发布 | 有权限的人执行 publish | 运维/负责人 | 版本 + 公网 |
| 5. 回滚 | 人工 restore | 运维 | 上一版 |

## 原则

- 内容中心生产内容；site builder 生成页面；区块只引用内容 ID。
- 不覆盖生产；先 staging/维护窗口。
- 不编造业务；缺项 `needs_confirmation`。
- 发布后爬虫：导航/旧 URL/404/sitemap/SEO/移动端。
