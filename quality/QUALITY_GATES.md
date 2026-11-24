# QUALITY_GATES.md — tarka.cn 发布门禁

> 发布是**人工动作**；Agent 只准备草稿与检查清单。Builder 没有 `site_builder.publish` 工具。

## Definition of Done

- 页面树/导航一致；每页一个 h1；SEO title/description 完整
- 区块类型合法；无空标题/空内容/重复块
- 内容来自内容中心；区块只引用内容 ID，无内联文案
- 无价格/账户/交易/持仓/支付/投研建议
- 无未确认客户/案例/数据/资质/收益
- 无实例品牌/域名/Logo 残留
- 图片 alt/兜底
- 375/768/1280/1920 无遮挡/横向滚动
- 404 自有；导航/页脚 200
- 可回滚版本存在

## 人工发布前

1. `sb_publish_checklist_get` 全通过
2. Verifier `pass`
3. 合规确认：无收益承诺/投资建议
4. 运维确认：有回滚版本
5. 发布后爬虫通过

## 阻断

P0 禁止发布；P1 修复后复测；P2/P3 记录排期。
