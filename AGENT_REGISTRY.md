# AGENT_REGISTRY.md — tarka.cn 项目角色映射（按仓库 YAML 校准）

> 来源：`agent_matrix/roles/*.yaml`、`agent_matrix/editions/*`、`agent_matrix/kernel.AGENTS.md`。  
> 注意：`agent_matrix/ARCHITECTURE.md` 是开发架构设计，不是运行时角色源；运行期角色/工具以 `roles/*.yaml` 为准。  
> 本表是项目级映射，不替代仓库角色定义；冲突以 YAML / L1 为准。  
> 假设：tarka.cn 使用 `official` 发行版 + `USER.context=site`；部署前必须核对 `VR_EDITION`。

| 角色 | slug | editions | 网站项目 | 真实边界（摘要） |
| --- | --- | --- | --- | --- |
| Athena | athena | 全版本 | 编排 | 只允许 `spawn_subagent`、`mcp__cli_bridge`；不直接执行业务工具 |
| Builder | builder | official | 核心 | 只读 `sb_*` + 草稿写 `sb_*`；**无 publish / version_restore** |
| Content | content | official | 核心（内容中心） | CMS/广告/content_factory；不写 site builder 页面 |
| Media | media | official | 视觉 | image/cover/media_library；vision/image；不伪造截图 |
| Verifier | verifier | official | 复核 | 只读 `sb_*` 五件套 + 统计/检索；无发布/写入 |
| Service | service | official | 受限 | 客服/工单/通知；网站只做表单分流，不读客户 PII |
| Business | business | official | 默认关闭 | 商品/订单/会员/库存；网站项目不启用 |
| Finance | finance | finance-desktop | 不适用 | 账单/订阅/支付/部署；网站项目不启用 |
| Ops | ops | finance-desktop | 不适用 | 运维/部署/监控；网站发布由平台人工执行 |
| Office | office | official | 默认关闭 | HR/行政/秘书；不进入网站运行时 |
| Stock Analyst | stock_analyst | official | 默认关闭 | 行情/指标/策略；网站项目不调用 |
| Survey Analyst | survey | official | 默认关闭 | 问卷/测评；不进入网站运行时 |
| Explorer | explorer | official | 默认关闭 | 只读探索/聚合；可用于线下调研，不发布 |
| 翻译桥 | translator | 全版本 | 默认关闭 | 只转述 CLI 后端原文；不加工事实 |

## 网站项目启用建议

- 启用：Athena（编排）、Builder、Content、Media、Verifier、Service（受限）。
- 默认关闭：Business、Finance、Ops、Office、Stock Analyst、Survey、Explorer、翻译桥。
- 任何角色 `ready=false` 或插件 `status=error` 时，阻断对应任务；不得用 partial 结果冒充成功。
