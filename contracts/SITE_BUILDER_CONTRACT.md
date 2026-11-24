# SITE_BUILDER_CONTRACT.md — tarka.cn 网站领域合同

> 版本：2.0.0（按仓库 `plugins/site_builder` + `roles/04-builder.yaml` 校正）  
> 继承：`agent_matrix/kernel.AGENTS.md` L1、`agent_matrix/AGENTS.default.md`、项目文档。  
> 关键事实：**publish 不向 Agent 开放；版本恢复也不在 Builder 工具集。**

## 1. 范围

**做：** 页面树、区块草稿、导航、SEO、设计令牌、发布检查清单、版本列表。

**不做：** 发布、版本恢复、账户、交易、持仓、价格、订单、支付、客户 PII、投研建议、业务数据。

## 2. 数据边界

允许：`site_builder.pages`、`site_builder.blocks`、`site_builder.design_tokens`、`site_builder.versions`、`site_builder.publish_checklist`。

禁止：`business.*`、`finance.*`、`orders.*`、`payments.*`、`inventory.*`、`members.*`、客户 PII、持仓、研究原始数据、其他 `site_key`。

## 3. 真实工具合同（Builder）

允许：

- 只读：`sb_pages_list`、`sb_tokens_get`、`sb_draft_blocks_get`、`sb_publish_checklist_get`、`sb_versions_list`
- 草稿写：`sb_pages_create`、`sb_pages_update`、`sb_nav_regenerate`、`sb_tokens_update`、`sb_draft_blocks_set`
- Mini App Builder MCP：规划/构建/查询（9 个工具）；upload/submit_audit 不在工具集

不允许：

- `site_builder.publish`（插件能力，人工发布）
- `sb_version_restore`（人工恢复）
- 任何 `business.*` / `finance.*` / `db.raw` / `shell` / `nginx.write`

## 4. 状态机

```
idle
  → parse_requirement
  → preview / plan
  → draft（sb_pages_* / sb_draft_blocks_set）
  → checklist（sb_publish_checklist_get）
  → [人工] publish
  → [人工] version_restore（如需要）
```

Agent 的终点是“草稿 + 检查清单 + 证据”。发布与恢复由有权限的人执行。

## 5. 内容与页面分离

- 内容只能由内容中心生产。
- site builder 的编辑类区块只能引用内容标识，禁止内联撰写文案。
- 禁止新建独立页面、独立路由、独立数据表、静态 HTML、站外页面。
- 能力不足时报告缺口，不得绕过实现。

## 6. 页面/区块规则

- 页面路径唯一；导航项必须指向存在的页面。
- 区块类型必须在白名单内；未知类型拒绝。
- `text` 块必须有 title 或 content；空标题空内容丢弃。
- `feature` 块不得让 `subtitle` 与 `content` 重复渲染同一段。
- 每页一个 h1；SEO title/description 必填。
- 品牌令牌不得含实例品牌/域名/路径；图片必须有 alt。

## 7. 发布门禁（人工执行前必须通过）

1. SEO：每页 title + description
2. 联系表单：至少一个 `form_contact`
3. 无降级页面：每页有草稿区块
4. 内链：导航/页脚链接可解析
5. 边界：无价格/订单/支付/账户/交易/投研建议
6. 品牌：无实例品牌/域名/Logo 残留
7. 空块/重复块：无空标题块、无多代区块
8. 移动端：375/768/1280/1920 无遮挡/横向滚动

## 8. 错误码

`not_permitted`、`not_in_edition_include`、`NO_TEMPLATE`、`MODEL_NOT_READY`、`ATTACHMENT_UNREADABLE`、`INVALID_PLAN`、`BOUNDARY_VIOLATION`、`CHECKLIST_FAILED`、`needs_confirmation`

## 9. 评测

- 正常：5-6 页官网、md 方案、导航/SEO、草稿检查。
- 边界：价格、账户、交易、投研建议、业务数据。
- 工具边界：Agent 调用 publish/version_restore 必须 `not_permitted`。
- 内容边界：区块内联文案必须被拒绝。
- 回归：重复执行、空块、重复块、旧路由、移动端。
