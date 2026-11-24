# TARKA_WEBSITE.PROJECT.md — tarka.cn 网站项目级补充规则

> 版本：2.0.0  
> 关系：**项目级补充**，继承 `agent_matrix/kernel.AGENTS.md`（L1），不替代仓库根 `AGENTS.md` 的施工铁律，不替代 `agent_matrix/AGENTS.default.md` 的用户覆盖层。  
> 发行版：按实际 `VR_EDITION`；本文件按 `official` + `USER.context=site` 编写，部署前必须核对。  
> 读者：Athena、Builder、Content、Media、Verifier、Service（受限）；其他角色默认不参与网站运行时。

## 1. 使命

把 tarka.cn 建成“可对质的 AI 投研系统”的对外入口：解释它是什么、证据链如何工作、为什么承认 `unavailable`，并引导真实商务沟通。**不提供投资建议、不展示收益承诺、不处理账户/交易/持仓。**

## 2. 文档边界（先分清开发文档与运行期文档）

- **不注入运行期**：仓库根 `AGENTS.md`（开发施工铁律）、`agent_matrix/ARCHITECTURE.md`（开发架构设计）、`agent_matrix/README.md`（开发状态）都不是运行时 Agent 的规则来源，禁止复制进 system prompt。
- **运行期加载链**：`agent_matrix/kernel.AGENTS.md`（L1）→ `agent_matrix/editions/<edition>.AGENTS.md`（L2，按 VR_EDITION）→ `agent_matrix/AGENTS.default.md`（用户覆盖层）→ `roles/*.yaml` + `prompts/*`（角色/工具）→ `USER.md`（context/identity/grants）→ `SOUL.md`（价值排序）。
- **本项目定位**：`TARKA_WEBSITE.PROJECT.md` 是 tarka.cn 的项目级补充，只能加严，不能放宽 L1/L2；不得替代仓库根施工铁律。

## 3. 继承与不重复

本文件不重复 L1 的 `NT-01…NT-12`。以下条款直接适用，必须原样遵守：

- NT-01 防伪造、NT-02 假完成、NT-03 越权工具、NT-04 审批、NT-05 密钥
- NT-06 跨发行版、NT-07 许可、NT-08 双区、NT-09 数据外泄
- NT-10 合规结论、NT-11 静默丢弃、NT-12 记忆污染

本文件只写 tarka.cn 项目的**加严/具体化**条款；与 L1 冲突时 L1 优先。

## 4. Site Builder 不是内容生成器（硬边界）

- **Site Builder 只做页面/区块/导航/布局/令牌/发布检查**，不生成任何用户可见文案。
- 标题、正文、卡片文案、FAQ、CTA、SEO title/description 必须来自内容中心，以 `content_id` 引用。
- Builder 的 `plan` 只能描述页面结构、区块类型、布局、内容 ID 占位；不得包含自己生成的文案。
- 用户要求“写文案/改文案/生成介绍”时，Builder 必须转给 Content；不得自己写。
- 缺少内容时，返回 `needs_content` / `needs_confirmation`，不得用占位文案或 LLM 补写。
- 发布检查只校验“内容引用是否存在、字段是否齐全”，不生成/改写内容。

## 5. 项目硬边界

- **内容与页面分离**：内容只能由内容中心生产；页面只能由 site builder 生成；编辑类区块只能引用内容标识，禁止内联撰写文案。
- **禁止独立页面/路由**：不得新建独立页面、独立路由、独立数据表、静态 HTML 或站外页面；能力不足时报告缺口，不得绕过。
- **Builder 工具边界**：只允许 `sb_pages_list`、`sb_tokens_get`、`sb_draft_blocks_get`、`sb_publish_checklist_get`、`sb_versions_list`、`sb_pages_create`、`sb_pages_update`、`sb_nav_regenerate`、`sb_tokens_update`、`sb_draft_blocks_set`（以及 `mcp__mini_app_builder` 的规划/构建/查询）。
- **publish 与版本恢复**：`site_builder.publish` 是插件能力/人工动作，不向 Agent 开放；`sb_version_restore` 也不在 Builder 工具集，版本恢复由人工执行。
- **不碰业务/财务**：网站项目不启用 Business/Finance/Stock Analyst；不读商品、订单、会员、库存、持仓、行情、账单。
- **不碰价格/账户/交易**：不定义套餐/价格/折扣/试用；不做登录、持仓、下单、支付、会员、发票。
- **不编造**：产品名、客户、数据覆盖率、收益率、团队、资质、案例，未由业务方提供即标 `needs_confirmation`，不得补合理值。
- **不写投资建议**：不出现买入/卖出/稳赚/精准预测/年化收益等；只描述研究能力与方法。
- **插件手册**：调用任何插件前先读 `plugins/<插件标识>/MANUAL.md`；缺失或冲突时停止并报告，不得凭经验使用。
- **服务器禁改**：按仓库根 `AGENTS.md`，禁止直接在服务器修改文件；变更走本地 + 部署流程。

## 6. 审批门禁

- 发布、删除、覆盖、回滚、部署、改 nginx/systemd/数据库：必须人工确认。
- 使用客户 Logo/案例/数据/资质：必须客户授权 + 合规确认。
- 任何价格/交易/账户能力：网站项目直接拒绝，转对应业务。

## 7. 输出合同

每个 Agent 的最终输出必须含：结论（做了什么/没做什么）、证据（文件/URL/响应/截图/测试）、未完成项与阻塞、风险与下一步。禁止只写“已完成”。

## 8. 失败语义

- 工具/模型/插件不可用：`blocked` 或 `failed`，附原因；不得 partial 冒充 completed。
- 附件不可读：`needs_confirmation` / `attachment_unreadable`，不猜品牌。
- 边界冲突：`BOUNDARY_VIOLATION`，停止并说明。
- 缺事实：`unavailable` / `needs_confirmation`，不补合理值。

## 9. 变更

本文件是项目文档；如需进入运行时，按真实 `VR_EDITION` 归入 `agent_matrix/editions/<edition>.AGENTS.md` 或项目加载层，**不得新造 edition**，不得覆盖 L1。
