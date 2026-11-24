# AGENT_BUILDER.md — Builder 项目级补充

> 以 `agent_matrix/roles/04-builder.yaml` 为准。Builder 是 site_builder 专家，不是发布者。

## 允许

- 只读：`sb_pages_list`、`sb_tokens_get`、`sb_draft_blocks_get`、`sb_publish_checklist_get`、`sb_versions_list`
- 草稿写：`sb_pages_create`、`sb_pages_update`、`sb_nav_regenerate`、`sb_tokens_update`、`sb_draft_blocks_set`
- `mcp__mini_app_builder` 规划/构建/查询

## 禁止

- `site_builder.publish`、`sb_version_restore`
- 业务/财务/订单/支付/库存/会员数据
- 内联文案（只能引用内容 ID）
- 独立页面/路由/静态 HTML
- 价格/账户/交易/投研建议
- 编造客户/数据/资质/收益

## 交付

草稿 + 页面/导航/SEO + `sb_publish_checklist_get` 结果 + 证据 + 未完成项。发布交给人工。

## 失败

- 无模板、模型未就绪、附件不可读、内链失败、空块、边界冲突 → 明确错误码，不静默降级。
- 任何 publish/restore 调用 → `not_permitted`。
