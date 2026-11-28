# AGENT_REGISTRY.DESKTOP.md — 塔卡投研桌面版角色注册表

> 版本：1.1.0  
> 权威角色源：`agent_matrix/roles/finance-desktop/*.yaml`。  
> 本注册表只增加项目章程，不新增运行时角色，不覆盖 YAML 的 `tool.fn`。

## 显示身份与系统身份

- 主 Agent 系统 slug 固定为 `athena`，用于角色 YAML、任务、handoff、审计、日志和工具路由。
- 主 Agent 默认产品显示名及自称固定为 **金牛**。
- 用户可在本机自定义助手显示名；只改变 UI 和自称，不改变 slug、工具或审计关联。
- 用户称呼、助手称呼与偏好保存规则见 `ONBOARDING.DESKTOP.md`。

| slug | 类型 | 项目章程 | 运行时提示词 | 核心所有权 |
| --- | --- | --- | --- | --- |
| `athena` | master | `agents/AGENT_DESKTOP_ATHENA.md` | `prompts/master_prompt.md` + 项目规则 | 意图、路由、预算、汇总、升级 |
| `rs_planner` | worker | `agents/AGENT_DESKTOP_RS_PLANNER.md` | `prompts/rs_planner.md` | 问题分解与数据清单 |
| `rs_fundamental` | worker | `agents/AGENT_DESKTOP_RS_FUNDAMENTAL.md` | `prompts/rs_fundamental.md` | 基本面证据分析 |
| `rs_quant` | worker | `agents/AGENT_DESKTOP_RS_QUANT.md` | `prompts/rs_quant.md` | 量价与市场结构分析 |
| `rs_risk` | worker | `agents/AGENT_DESKTOP_RS_RISK.md` | `prompts/rs_risk.md` | 反方、压力情景、失效风险 |
| `rs_pm` | worker | `agents/AGENT_DESKTOP_RS_PM.md` | `prompts/rs_pm.md` | 组合视角收敛与可证伪结论 |
| `rs_compliance` | worker | `agents/AGENT_DESKTOP_RS_COMPLIANCE.md` | `prompts/rs_compliance.md` + 规则引擎 | 反方审查与末道合规 |
| `ops` | worker | `agents/AGENT_DESKTOP_OPS.md` | ops 默认规则/插件手册 | 本地核心、插件、数据流、通道 |

## 执行模式映射

### A. 通用 Agent 任务

Athena 通过 `spawn_subagent` 派发，worker 通过 `mcp__cli_bridge` 执行被允许的插件工具。所有任务遵守 L1 终态、预算、handoff 和审批规则。

### B. 个股快速/深度讨论

`discuss_research.py` 的实际逻辑：

| 逻辑阶段 | 角色 |
| --- | --- |
| planner | `rs_planner` |
| reviewer | `rs_compliance`，做反方/风险审查，不等于完整确定性合规发布门 |
| revise | `rs_fundamental` |
| decider | `rs_pm` |

这是四个逻辑 LLM 阶段；不要把 `stage_nodes` 的展示列表当成额外已执行节点。`depth` 由调用方指定，快速问题不得被前端自动改成深度。

### C. 显式研究 DAG

`stock.rs_planner → stock.collect → stock.audit → [stock.rs_fundamental | stock.rs_quant | stock.valuation] → stock.rs_risk → stock.rs_pm → stock.rs_compliance`

其中 collect/audit/valuation 是确定性代码节点，不是新增 Agent；基本面、量价、估值可并行，合规在 PM 后执行。

### D. 深度研究报告

`deep_research.generate` 是独立实现，返回证据、摘要、结论、代码置信度和免责声明。它不等于显式 DAG，也不能仅凭 reviewer 阶段就宣称完成完整合规审批。

## 路由原则

- 单标的浅层问题：优先已有只读分析或快速讨论，不机械调用全部角色。
- 多维度、要求证据/反方/正式交付：按用户选择的深度进入深度讨论、深度报告或显式 DAG。
- 组合问题：必须考虑相关性、集中度、共同风险因子，不把多个单标的结论简单拼接。
- 风险与合规不能被 Athena 或其他角色“代签”。
- 运维问题只派 ops；运维角色不得输出投研结论。
- 需要内容处理、检索、邮件等插件时，由已有角色在权限内调用，不创建新的 content/service Agent。

## 统一 handoff 最小字段

```yaml
task_id: "后端真实 id"
role: "rs_quant"
objective: "本次子任务"
symbol_or_portfolio: "明确对象"
as_of: "数据时点或未知"
depth: "quick|deep|explicit_pipeline"
evidence_refs: []
license_ids: []
allowed_tools: []
budget: {}
upstream_status: "success|partial"
missing: []
requires_approval: false
```

字段是项目交接合同，不表示所有现有端点已原样实现；适配层负责映射，不能伪造后端字段。

## 终态与归属

- `success`：本次请求的必要步骤全部完成，证据和合规满足输出级别。
- `partial`：有可用产出，但必要数据、角色、验证或许可未完成。
- `blocked`：权限、许可、合规、审批或安全门阻止继续。
- `failed`：技术执行失败，无可用完整结果。

`success=true` 与 HTTP 200 不是同义词。只要缺失影响结论，必须同时返回 `partial=true` 和 missing。角色只对自己声明的所有权负责，Athena 对最终状态汇总负责。
