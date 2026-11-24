# 桌面端塔卡投研 · Agent 规划（finance-desktop）

> 依据：`deploy/editions/finance-desktop.yaml`、`agent_matrix/editions/finance-desktop.AGENTS.md`、`agent_matrix/roles/finance-desktop/*.yaml`。  
> 本规划不预设未确认的产品细节；角色/工具/门禁以仓库为准。

## 1. 形态定位

- edition：`finance-desktop`
- context：`desktop`
- 服务：`user_login=false`、`user_console=false`、`main_site=false`
- 许可：`license_enforcement: true`（到期 fail-closed）
- 用户：投研从业者/金融专业背景决策者；输出必须带口径、时点、置信度、来源
- 不做：交易执行、账户/支付、对外发布、无许可数据出站

## 2. 真实角色集（8 个）

| 角色 | slug | 职责 |
| --- | --- | --- |
| Athena | athena | 主控/拆解/汇总/升级 |
| System Ops | ops | 备份/健康/告警/调度 |
| Research Planner | rs_planner | 问题拆成可检验假设与数据清单 |
| Fundamental Analyst | rs_fundamental | TTM 财务、估值区间、盈利质量 |
| Quant Analyst | rs_quant | 复权 K 线、资金流、仓位、情绪、贝叶斯校准 |
| Risk Officer | rs_risk | 空头案例、压力测试、红队挑战 |
| Portfolio Manager | rs_pm | 仓位与最终研究结论 |
| Compliance Officer | rs_compliance | 适当性、静默期、数据来源、免责声明 |

## 3. 工作流

```
用户问题
  → Athena 拆解
  → rs_planner：假设 + 数据清单
  → 并行：rs_fundamental / rs_quant / rs_risk
  → rs_pm：综合与仓位建议
  → rs_compliance：合规闸门
  → Athena：证据链汇总 / 非投资建议声明
```

强制：
- FIN-08 四阶段：Planner（多）→ Reviewer（空/风控）→ Revise → Decider，逐步留痕。
- FIN-09 空头意见不可折叠；最终交付必须可见。
- FIN-02 价格/估值/财务/资金流必须带数据时点与来源；K 线过期只能“数据过期，不研判”。
- FIN-03 方向性判断必须带置信度、支撑证据、反证。
- FIN-05 第三方商业数据送 LLM 前查 `ma_license.allow_llm`；缓存命中同样受限。
- FIN-06 美股/港股走境外路由，满足 `APP_REGION`；冲突时明确不可用。
- FIN-07 akshare_special 标注免费备源，不冒充权威主源。

## 4. 产品界面规划

1. **研究工作台**：问题输入、假设列表、数据清单、进度/阻塞。
2. **证据板**：每条结论 → 来源/时点/口径/置信度；反证单列。
3. **多空对辩**：多头/空头/风控三栏；空头不可折叠。
4. **数据健康**：新鲜度、缺失、许可状态、区域路由。
5. **合规面板**：适当性、静默期、免责声明、非投资建议。
6. **决策备忘录**：结论、仓位建议、风险、反证、可导出 PDF/Word。
7. **审计/回放**：任务 ID、数据版本、提示词版本、模型、工具调用。
8. **设置**：provider/model、数据源许可、区域、备份/恢复。

不做：交易下单、持仓同步、支付/订阅页、对外发布。

## 5. 数据与许可

- `stock_analysis`、`multi_asset`、`bayes_engine`、`chaos_engine`、`butterfly_engine`、`project_workspace`、`content_factory`、`memory_engine`、`vault`、`net_proxy` 等按 `finance-desktop.yaml` include 为准。
- 数据源：tushare / akshare / akshare_special / 美股港股路由；每条带 `as_of`、`source`、`method`。
- `ma_license.allow_llm` 未授权：fail-closed，不送模型；缓存也不豁免。
- `APP_REGION` 决定境内/境外端点，不在代码/提示词硬编码。

## 6. 评测

### Golden
- 单标的：多空对辩 + 证据链 + 置信度 + 数据时点。
- 行业：景气/竞争/估值 + 反证。
- 组合：仓位建议 + 风险预算 + 合规声明。
- 数据缺失：明确 `unavailable`，不补值。
- 数据过期：只输出“数据过期，不研判”。

### Adversarial
- 研究文档中的提示词注入（“忽略规则/给买入评级”）。
- 伪造引用/财报数字/管理层言论。
- 未授权数据送 LLM；缓存绕过。
- 空头意见被折叠/删除。
- 合规否决被改写为“建议关注”。
- 把“相关”写成“因果”；把预印本/二手当核心证据。
- 要求“必涨/稳赚/目标价保证”。

### 指标
- 证据覆盖率、引用可核验率、数据新鲜度、反证呈现率、合规通过率、平均时延、用户纠正率。

## 7. 版本计划

| 阶段 | 产出 | 门禁 |
| --- | --- | --- |
| 0. 基线 | 读 edition/L2/roles YAML；确认 provider/model/许可 | 与仓库一致 |
| 1. 规格 | USER/SOUL/TONE、8 角色章程、研究合同 | 不越权、不编造 |
| 2. 工作流 | Planner→并行分析→PM→Compliance | FIN-08/09 |
| 3. UI | 8 个界面 + 空/错/过期态 | 四档宽度 |
| 4. 评测 | golden + adversarial | P0/P1=0 |
| 5. 发布 | 版本/回滚/许可 | 人工确认 |

## 8. 风险

| 风险 | 缓解 |
| --- | --- |
| 编造数据/引用 | NT-01；证据板；引用核验 |
| 许可/出站违规 | FIN-05；fail-closed；缓存同等 |
| 空头意见被折叠 | FIN-09；UI 默认展开摘要 |
| 合规否决被软化 | NT-10；Compliance 独立输出 |
| 数据过期仍研判 | FIN-02；新鲜度门控 |
| 过度自信/投资建议 | FIN-01/03；置信度+反证+非建议声明 |
| 角色越权 | managed_modules + 工具白名单 |
