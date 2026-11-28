# VeroRun / Tarka Agent Spec

可执行 Agent Spec 仓库，当前包含两套明确隔离的项目约规：

- **塔卡投研桌面版**：`finance-desktop + context: desktop`，VeroRun 后端 + Electron 本地核心。
- **tarka.cn 网站**：`official + context: site`，公网站点建设与内容，不等同于桌面投研。

## 0. 文档类型与上位规则

| 类型 | 作用 |
| --- | --- |
| 项目规则 | 使命、架构、边界、权限、完成定义 |
| USER | Agent 服务对象与授权，不写系统提示词 |
| SOUL / TONE | 人格价值与表达风格 |
| TOOLS / MEMORY | 工具、副作用、数据许可与记忆边界 |
| Registry + 角色章程 | 角色、职责、输入输出、交接、禁止事项 |
| contracts / 研究流程 | 领域合同、状态机、证据与交付门 |
| quality | 质量门禁和可执行评测设计 |
| operations | 桌面启动、诊断、构建、事故与回滚 |

上位运行时规则仍以 VeroRun 的 `kernel.AGENTS.md`、`editions/*.AGENTS.md`、`AGENTS.default.md`、角色 YAML 与 prompts 为准。本仓库是项目层补充，不覆盖上位安全规则，也不代替后端技术强制。

开发文档与运行时文档必须分开：VeroRun 根 `AGENTS.md`、`agent_matrix/ARCHITECTURE.md` 等开发者资料不得直接注入运行时 Agent；项目约规如需接入，应通过真实加载链或项目层机制，不新造 edition。

---

# 一、塔卡投研桌面版（完整约规集）

## 1. 推荐阅读顺序

1. `TARKA_DESKTOP_BASELINE.md` — 三仓库事实、真实职能、执行路径与文档漂移。
2. `TARKA_DESKTOP.PROJECT.md` — 项目架构、三端边界、桌面发布和 FIN 规则。
3. `USER.DESKTOP.md` — 本机授权、数据范围与交易边界。
4. `SOUL.DESKTOP.md` + `TONE.DESKTOP.md` — 研究人格、价值排序与表达。
5. `ONBOARDING.DESKTOP.md` — 金牛/Athena 身份分离、首次称呼设置与偏好记忆。
6. `TOOLS.DESKTOP.md` + `MEMORY.DESKTOP.md` — 插件、API、审批、许可与记忆。
7. `AGENT_REGISTRY.DESKTOP.md` + 8 个角色章程。
8. `contracts/TARKA_DESKTOP_CONTRACT.md` + `contracts/TARKA_DESKTOP_RESEARCH_FLOW.md`。
9. `quality/DESKTOP_QUALITY_GATES.md` + `quality/DESKTOP_EVALS.md`。
10. `operations/DESKTOP_RUNBOOK.md`。

## 2. 文档地图

| 文档 | 内容 |
| --- | --- |
| `TARKA_DESKTOP_BASELINE.md` | 静态审计与决策增量；真实架构、当前 14/目标 13 插件、文档漂移 |
| `TARKA_DESKTOP.PROJECT.md` | 项目级最高补充规则、三端架构、发布与移动边界 |
| `USER.DESKTOP.md` | 单用户本机授权；无需 tarka.cn 网站账号；不授予真实交易/用户管理 |
| `SOUL.DESKTOP.md` | 合规与证据优先、反方、可证伪、失败诚实 |
| `ONBOARDING.DESKTOP.md` | 默认金牛、内部 athena 不变；首次称呼设置、跳过与偏好记忆同意 |
| `TONE.DESKTOP.md` | 专业克制的表达、数字与失败措辞 |
| `TOOLS.DESKTOP.md` | 当前插件能力、关键后端入口、R0–R5 动作分级、出站许可 |
| `MEMORY.DESKTOP.md` | 用户记忆/会话/证据/审计分层，隔离与禁止沉淀项 |
| `AGENT_REGISTRY.DESKTOP.md` | 8 角色注册、研究入口映射、handoff 与终态 |
| `agents/AGENT_DESKTOP_ATHENA.md` | 总控、路由、预算、汇总与升级 |
| `agents/AGENT_DESKTOP_RS_PLANNER.md` | 假设与数据清单；collect 前不得伪造证据 |
| `agents/AGENT_DESKTOP_RS_FUNDAMENTAL.md` | 基本面、PIT、修订阶段与财务证据 |
| `agents/AGENT_DESKTOP_RS_QUANT.md` | 后端量价计算、样本、时态与偏差 |
| `agents/AGENT_DESKTOP_RS_RISK.md` | 独立反方、压力情景与描述性风险工具 |
| `agents/AGENT_DESKTOP_RS_PM.md` | 结论收敛、组合约束、可证伪输出 |
| `agents/AGENT_DESKTOP_RS_COMPLIANCE.md` | reviewer 与确定性合规分离、末道门禁 |
| `agents/AGENT_DESKTOP_OPS.md` | 本地核心、PG、插件、通道和完成证据 |
| `contracts/TARKA_DESKTOP_CONTRACT.md` | 交付物、移动连通、证据与变更红线 |
| `contracts/TARKA_DESKTOP_RESEARCH_FLOW.md` | discuss/report/pipeline 分离、9 节点 DAG、证据合同 |
| `quality/DESKTOP_QUALITY_GATES.md` | G0–G10，基线、证据、权限、桌面构建与交付门 |
| `quality/DESKTOP_EVALS.md` | 路由、角色、证据、合规、权限、桌面评测设计 |
| `operations/DESKTOP_RUNBOOK.md` | 启停、PG、裁剪、健康检查、错误处置与事故报告 |

## 3. 当前状态

- 已完成：仓库静态分析、约规编写、桌面约规一致性与引用检查。
- 未执行：桌面端启动、数据库/模型/数据源联调、项目测试、安装包构建和运行时评测。
- `quality/DESKTOP_EVALS.md` 的状态是 `designed_not_run`，不得解读为“评测已通过”。
- 发现的代码/文档漂移见基线审计，未借写 Spec 擅自修改 VeroRun 或 workplace。

---

# 二、tarka.cn 网站约规

## 1. 阅读顺序

1. `TARKA_WEBSITE.PROJECT.md`
2. `USER.md`
3. `SOUL.md` / `TONE.md`
4. `TOOLS.md` / `MEMORY.md`
5. `AGENT_REGISTRY.md` 与 `agents/AGENT_MASTER.md`、`AGENT_BUILDER.md`、`AGENT_CONTENT.md` 等
6. `contracts/SITE_BUILDER_CONTRACT.md`
7. `quality/QUALITY_GATES.md`、`quality/EVALS.md`
8. `operations/RUNBOOK.md`

## 2. 核心边界

- 网站只做公网站点建设、内容与人工发布配合。
- 不处理账户、交易、持仓、价格、订单、支付、客户 PII 或投研建议。
- Content/内容中心生产内容；Builder 只做页面、区块、导航、布局、内容引用和发布检查。
- Builder 不自行生成文案、不直接调用 Content；缺内容返回 `needs_content` 给 Athena，由 Athena 派发 Content 后回传 content_id。
- publish/version_restore 不向 Agent 开放，由人工执行。

## 3. 网站文档地图

| 文档 | 作用 |
| --- | --- |
| `TARKA_WEBSITE.PROJECT.md` | 网站项目级规则 |
| `USER.md` / `SOUL.md` / `TONE.md` | 网站授权、人格与语气 |
| `AGENT_REGISTRY.md` | 网站角色注册与边界 |
| `contracts/SITE_BUILDER_CONTRACT.md` | Site Builder 真实工具合同 |
| `BOUNDARY_CORRECTION.md` | 内容/页面边界纠偏与历史测试归因 |
| `quality/EVALS.md` | 网站 A–D 组评测 |
| `plans/MARKDOWN_DROP_PUBLISH.md` | 网站 Markdown 投递与发布规划 |
| `templates/` | 新实例 Spec 模板 |
| `CHANGELOG.md` | 两项目的文档变更记录 |

## 4. 模板使用

复制 `templates/` 创建新实例时，先读真实 VeroRun 仓库的 L1/L2、角色 YAML、发行 YAML 与插件手册，再填写角色和工具；不得从其他项目猜权限、接口或发行版。
