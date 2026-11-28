# 塔卡投研桌面版 · 仓库基线与职能审计

> 版本：1.1.0 · 基线日期：2026-11-28  
> 性质：静态代码与文档审计 + 用户决策增量，不是运行测试报告。  
> 用途：为 `TARKA_DESKTOP.PROJECT.md` 及配套约规提供事实底座。

## 1. 审计范围与版本

| 仓库 | 本地路径 | 审计 HEAD | 关键版本 |
| --- | --- | --- | --- |
| VeroRun 核心 | `D:\Sites\VeroRun` | `0bc975cb` | `VERSION=0.68.1`；stock_analysis `1.13.1` |
| Electron 桌面端 | `D:\Sites\verorun-workplace` | `b69b06b7` | `package.json=0.7.0` |
| Agent Spec | `D:\Sites\verorun-agent-spec` | `4df4857` | 本次在其既有 2.1.0 草案上扩展 |

本次只读分析，未启动桌面端、数据库或模型，未执行项目测试，未修改 VeroRun 与 workplace。后续任何“运行正常”“全量通过”必须另附实测范围、环境和输出。

## 2. 智能体核心职能

**塔卡投研桌面版是以 VeroRun 本地核心为唯一业务真相源、由主 Agent（系统角色 `athena`，默认显示名/自称“金牛”）组织多角色投研、以证据链和反方风险论证约束模型输出的单用户本机投研工作台。**

产品不是多租户 SaaS；用户无需注册或登录 `tarka.cn` / `agent.tarka.cn`。本机核心认证与设备配对不等于网站账号，可选中继不承担网站登录或偏好同步。

主要职责：

1. 识别研究对象、市场、问题类型与所需深度。
2. 调用后端数据、研究、风险、组合、合规与交付能力。
3. 在需要深度研究时，组织规划、证据收集、质量审计、基本面、量价、估值、风险、组合经理与合规节点。
4. 输出结论、证据、置信度、证伪条件、缺失项和免责声明。
5. 在显式授权范围内完成自选、提醒、工作流、文档与仿真等辅助操作。
6. 通过 Electron 桌面壳提供本地任务、知识库、数据流、交付物和可选通道入口。

产品不是公开荐股自媒体、收益承诺工具或真实资金自动交易系统。真实交易不是当前随包能力；已有仿真交易 UI/API 不等于授权 Agent 自动下单。

## 3. 真实运行架构

### 3.1 后端权威链

运行时规则链：

`kernel.AGENTS.md（L1） → editions/finance-desktop.AGENTS.md（L2） → AGENTS.default.md → roles/finance-desktop/*.yaml → prompts/* → 项目 USER/SOUL`

业务事实链：

`插件路由/服务 → PostgreSQL/数据源/证据包 → 结构化响应 → Electron 调用与展示`

约束：

- 数值计算、信号、证据审计、置信度、合规、许可和任务状态在后端完成。
- Electron 只做渲染、交互、已有 API 调用、后端任务触发和错误/加载状态。
- 插件 `MANUAL.md` 是运行时调用前置资料；角色 YAML 的 `tool.fn` 只是入口，不是无限插件授权。
- JWT 权限、角色工具、插件 capability、数据许可和人工批准是不同闸门，不能相互替代。

### 3.2 桌面壳与裁剪链

`finance 构建标记 → edition-registry（对外 identity=finance，业务 coreEdition=finance） → finance 导航与路由 → stage-verorun-core.mjs → deploy/editions/finance-desktop.yaml → electron-builder.finance.yml`

已核实的构建事实：

- `dev:finance` 设置 `VERORUN_CHANNEL=finance`、`VERORUN_EDITION=finance`，启动 core staging、Vite 与 Electron。
- staging 脚本从 VeroRun 同步核心代码，并在复制插件前执行发行版裁剪。
- `verify-edition-plugins.mjs` 用 YAML `_base + include − exclude` 校验 enabled 与 installed，白名单外插件为 hard fail。
- finance-builder 负责金融版安装包身份与资源打包；staging/发行 YAML 决定核心插件裁剪。个人微信是否可用必须同时核对发行白名单与实际壳层接线，不能只看旧构建说明。
- 打包后的桌面不是完整 VeroRun 社区插件版；research-desktop、tarka 官网和官方服务器插件不能混入本发行版。

### 3.3 本地核心生命周期

standalone Windows 使用本地原生 Python + waitress + 便携 PostgreSQL，不走 WSL2。启动顺序为 PostgreSQL → admin → health，窗口仅在核心状态满足门槛后进入业务页。

当前代码常量：`PG_START_TIMEOUT_MS=30s`、`PG_READY_POLLS=300`、`PG_READY_INTERVAL_MS=2000ms`。正常运行、异常恢复和关停纪律见 `operations/DESKTOP_RUNBOOK.md`。

## 4. 角色与执行路径

### 4.1 发行版角色

| 角色 | 类型 | 核心职责 | 主提示词 |
| --- | --- | --- | --- |
| `athena` | master | 意图识别、编排、预算、汇总、升级 | `prompts/master_prompt.md` + 项目规则 |
| `rs_planner` | worker | 研究问题分解、数据清单、显式深度讨论规划 | `prompts/rs_planner.md` |
| `rs_fundamental` | worker | 基本面证据分析、深度讨论修订 | `prompts/rs_fundamental.md` |
| `rs_quant` | worker | 量价、技术与市场结构分析 | `prompts/rs_quant.md` |
| `rs_risk` | worker | 反方论证、压力情景、失效条件 | `prompts/rs_risk.md` |
| `rs_pm` | worker | 组合视角收敛、结论与证伪条件 | `prompts/rs_pm.md` |
| `rs_compliance` | worker | 讨论反方审查、确定性合规门禁 | `prompts/rs_compliance.md` + `compliance.py` |
| `ops` | worker | 本地核心、插件、数据流、通道诊断 | 复用 ops 默认规则与插件手册 |

插件 manifest 中的 `agent_role` 是能力归属提示，不是新增运行时角色。`content`、`service` 等归属不会在 finance-desktop 自动变成第九、第十个 Agent。

### 4.2 三条不能混写的研究路径

| 路径 | 真实实现 | 适用 |
| --- | --- | --- |
| 快速/深度讨论 | `POST /admin/stock-analysis/api/discuss` → `discuss_research.run_discussion` | 单标的快速或深度讨论；planner → reviewer → revise → decider 四个逻辑阶段 |
| 深度研究报告 | `POST /admin/stock-analysis/api/research/report` → `deep_research.generate` | 证据包、摘要、结论、代码置信度与免责声明 |
| 显式研究工作流 | `POST /admin/stock-analysis/api/research/pipeline` → `services/research_pipeline.py` + `research_dag.py` | 9 节点可追踪 DAG，含并行分析与末道合规 |

通用 Agent 对话还可经 `agent_matrix` 会话、任务和审批链运行。不得把任一入口的角色、节点数或输出字段套到另一入口。

## 5. 当前发行插件与已确认目标

截至本决策增量复核，`finance-desktop.yaml` **当前仍启用 14 个业务插件**：

- 投研：`stock_analysis`、`multi_asset`。
- 记忆与推断：`memory_engine`、`bayes_engine`。
- 风险：`butterfly_engine`、`chaos_engine`。
- 检索与内容：`search_hub`、`content_factory`、`project_workspace`。
- 基础设施：`vault`、`net_proxy`、`neural_flow`。
- 通道：`email`、`wechat_personal`。

2026-11-28 用户已决定把 `wechat_personal` 移出白名单，**尚未执行**。目标清单为 13 个业务插件；只有 YAML、staging、安装包与实际 UI/API 接线均完成移除并验收后，才能把目标写成当前事实。`im_gateway` 仍不在 include 中。

## 6. 已确认的数据与合规机制

- `EvidenceBundle` 为证据项保存 key、value、unit、as_of、source、provenance_id、freshness、url、缺失原因等。
- 报告数值可由 `verify_against_evidence` 扫描；它是有允许池和容差的审计工具，不等于绝对消除幻觉。
- `compute_confidence` 默认权重为 coverage 0.35、freshness 0.20、agreement 0.25、history 0.20；模型自报不得覆盖。
- 研究 DAG 的 collect 产生证据与许可 id；下游 LLM 节点透传，商业数据出站由 `guard_llm_egress` 控制，异常 fail-closed。
- `multi_asset` 当前已声明并实现 PIT 基本面/一致预期与质量报告端点；历史回测仍须显式 as-of，不能用当前快照替代历史可见信息。
- `rs_compliance` 在 DAG 末端调用确定性 `compliance.run_compliance_check`，不是只靠提示词自觉。
- 报告与交付物生成不等同于对外发布；邮件、微信、公开发布、敏感配置、下单分别受独立权限与批准约束。

## 7. 文档与代码漂移清单

以下问题必须在编写提示词、验收或发布说明时显式处理，不能任选一份旧文档照抄。

| 编号 | 漂移 | 当前事实依据 | 约规处置 |
| --- | --- | --- | --- |
| DR-01 | L2 仍写 `im_gateway`，旧计划也继承 | 当前 YAML 不含 `im_gateway`，仍含 `wechat_personal`；但新决策要求目标移除后者，尚未执行 | 当前事实与目标状态分列；实施后同步 L2、YAML、staging、壳层和手册 |
| DR-02 | L2 的 stock_analysis 版本仍为 1.4.0 | manifest 为 1.13.1 | 版本只引用 manifest/构建产物 |
| DR-03 | `master_prompt.md` 仍列通用 content/builder 等角色 | finance YAML 只有 8 个角色；stock 路由有自己的角色装配 | 通用 roster 不得作为金融角色真相源；需后续核心对齐 |
| DR-04 | `multi_asset/docs/role-integration.md` 称 PIT/quality 未声明 | manifest 与 routes 已声明并实现 | 能力以 manifest + routes 为准；旧文档仅作背景 |
| DR-05 | stock README 的“统一 Athena 派发/节点数”描述与多个专用入口并存 | `routes.py`、`discuss_research.py`、`research_dag.py` 实现不同路径 | 按入口分别验收，不宣称所有请求走同一 DAG |
| DR-06 | 用户手册部分章节写 rs_compliance → rs_pm | DAG 实际为 risk → pm → compliance | 合规必须是末道门禁 |
| DR-07 | 项目计划把完整角色章程和评测列为待办 | 既有 Spec 2.1.0 只有项目、USER、合同骨架 | 本次补齐文档；运行验收仍待实测 |

发现新漂移时：停止依赖冲突描述执行高风险动作，记录路径和差异，按“代码/manifest/YAML > 运行时规则 > 项目约规 > 使用手册/计划”的适用范围判断；运行时规则的安全等级只能提高，不能借代码现状降低。

## 8. 约规适用边界

- 本套约规是 finance-desktop 的**项目层补充**，不覆盖 L1/L2，不修改角色 YAML，不代替后端强制校验。
- 文中“禁止 Agent”是产品策略，不表示代码已经提供同等强度的技术隔离；验收必须检查实际强制点。
- 文中接口清单只列已核实的关键入口，不是完整 API 目录。
- 对计划能力、目标合同和当前实现分别使用“目标/应当”和“当前/已实现”措辞，禁止混写。

## 9. 2026-11-28 决策增量（Spec 3.1.0）

本节只复核受影响项；上方三仓库 HEAD 表保留初次全量静态审计快照。增量复核时受影响仓库 HEAD 已变为 VeroRun `34940739`、workplace `7c567bb`，未借文档任务修改其实现。

| 决策 | 当前代码/运行事实 | 目标合同 |
| --- | --- | --- |
| 单用户本机，不是多租户；无需 tarka.cn 登录 | 既有项目已有本机授权与中继边界 | 明确本机认证≠网站账号，偏好不进入网站后台、不跨实例同步 |
| 移除 `wechat_personal` | 当前 `finance-desktop.yaml:131` 仍包含，决策尚未执行 | 目标从白名单、staging、安装包和 UI/API 接线移除；完成前不得宣称已移除 |
| 主 Agent 默认显示名/自称“金牛” | 内部角色和工具仍使用 `athena` | 显示名可定制，slug、任务、handoff、日志和审计不变 |
| 首次启用了解称呼与偏好 | 本次只完成产品合同，未确认后端已实现 | 默认可跳过；未同意只存会话，同意后本机保存；详见 `ONBOARDING.DESKTOP.md` |

以上均为 Spec 更新，不代表运行时功能已经开发或测试通过。
