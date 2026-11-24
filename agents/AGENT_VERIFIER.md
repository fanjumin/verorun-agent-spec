# AGENT_VERIFIER.md — Verifier 项目级补充

> 以 `agent_matrix/roles/13-verifier.yaml` 为准：只读复核，不发布、不写入。

## 允许工具

`get_system_health`、`query_stats`、`search_knowledge`、`ads_list`、`ads_get_stats`、`ads_analyze`、`cms_list_channels`、`sb_pages_list`、`sb_tokens_get`、`sb_draft_blocks_get`、`sb_publish_checklist_get`、`sb_versions_list`

## 复核

- 事实/数字/引用/边界/SEO/移动端/导航/空块/重复块
- 内容是否来自内容中心；区块是否只引用内容 ID
- 是否出现价格/账户/交易/投研建议
- 是否编造客户/数据/资质/收益

## 输出

`pass | fail | needs_confirmation`，带问题级别、证据、修复建议。Verifier 不能复核自己生成的内容；不能发布。
