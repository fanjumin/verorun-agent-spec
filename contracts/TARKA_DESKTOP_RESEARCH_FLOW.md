# 塔卡投研桌面版 · 研究流程与证据合同

> 版本：1.0.0  
> 配套：`AGENT_REGISTRY.DESKTOP.md`、`TOOLS.DESKTOP.md`、`contracts/TARKA_DESKTOP_CONTRACT.md`。

## 1. 入口选择

Athena/调用方先识别对象、市场、问题类型、深度、输出用途和是否需要副作用。

| 用户意图 | 默认路径 | 禁止误路由 |
| --- | --- | --- |
| 单一指标、简单技术/基本事实 | 已有只读 API 或快速讨论 | 不默认启动 9 节点 DAG |
| 单标的深度讨论 | `/api/discuss`，`depth=deep` | 不把 reviewer 当作完整合规审批 |
| 证据化研究报告 | `/api/research/report` | 不声称等同于显式 DAG |
| 可追踪正式研究、多角色并行与末道合规 | `/api/research/pipeline` | 不省略 collect/audit/risk/compliance |
| 多标的组合 | 组合分析 + 必要的角色流程 | 不拼接单标的结论替代相关性分析 |
| 回测 | 后端回测 API | 不用当前快照回填历史，不算未来函数 |
| 正式外发 | 已完成研究 + 独立外发审批 | 不研究生成即自动发送 |

深度不明且会显著改变成本时，先让用户选择；已有明确 `depth` 时不擅自升级。

## 2. 通用任务状态机

`received → planned → awaiting_approval（可选） → running → validating → success | partial | blocked | failed`

规则：

- 每次状态变化由后端任务/工作流记录支撑，前端只展示。
- 缺关键输入、权限、许可或批准时进入 blocked，不伪造中间结果。
- 节点失败但有可用产出时为 partial，列明 missing 和不可支持的结论。
- 重试先查 task/job/run 状态，避免重复下单、重复外发和重复工作流。
- 只有必要步骤、证据验证和输出级合规全部完成后才可标 success。

## 3. 个股讨论合同

实际顺序：

1. **Planner**：输出研究计划。
2. **Reviewer**：`rs_compliance` 提示词进行反方与风险审查。
3. **Revise**：`rs_fundamental` 基于计划和审查修订。
4. **Decider**：`rs_pm` 给出最终讨论文本。

约束：

- 讨论输入可以包含规范化价格信息，但仍须保留数据时点和缺失。
- reviewer 是讨论角色，不代替 `compliance.run_compliance_check` 的确定性门禁。
- decider 不得删除 reviewer 的关键反方，不得输出收益承诺。
- 快速/深度共用逻辑阶段，深度由真实参数和运行配置决定，不由文案伪称。

## 4. 显式 9 节点 DAG 合同

| 顺序 | 节点 | 必要输入 | 必要输出/门禁 |
| --- | --- | --- | --- |
| 1 | `stock.rs_planner` | symbol、question | hypotheses、data_checklist；此阶段尚无证据，不得伪造引用 |
| 2 | `stock.collect` | symbol | EvidenceBundle、evidence_text、coverage、license_ids、missing |
| 3 | `stock.audit` | bundle | coverage、freshness、missing、warnings、quality_grade |
| 4a | `stock.rs_fundamental` | evidence、audit、license_ids | fundamental_view、view、raw |
| 4b | `stock.rs_quant` | evidence、audit、license_ids | technical_view、view、raw |
| 4c | `stock.valuation` | symbol、后端估值数据 | valuation_range 或明确 unavailable |
| 5 | `stock.rs_risk` | 上游观点、证据、许可 | bear_cases、stress_tests |
| 6 | `stock.rs_pm` | 分析、估值、风险、证据 | position_view、conclusion、self_audit、invalidation、verify_by |
| 7 | `stock.rs_compliance` | conclusion、用户上下文、来源/指标 | passed、flags、disclaimer、evidence_hash、version |

4a/4b/4c 可并行；其他任何顺序优化不得把 compliance 提前到 PM 之前。

### 节点失败

- collect 无有效证据：不得继续生成方向性结论。
- audit 质量不足：保留缺失和低质量，只允许补证/观察路径。
- 任一分析师失败：其他结果可保留，但状态为 partial，PM 不得假设失败角色意见。
- valuation 不可用：明确 unavailable，不用行业印象补估值。
- risk 失败：不得标完整研究成功。
- compliance `success=false` 或存在 error flag：正式输出 blocked；不得由 Athena/PM 覆盖。
- confidence/evidence_scan 附加步骤失败：如实降级，不伪造验证通过。

## 5. 证据合同

每条支撑关键结论的证据至少保留：

```yaml
key: "指标或事实键"
value: "原始值"
unit: "单位/币种"
as_of: "归属期或时点"
source: "真实数据源"
provenance_id: "后端证据 id"
freshness: "realtime|delayed|eod|stale|unavailable|unknown"
url: "如有"
license_ids: []
note: "缺失、修订或质量说明"
```

### 使用规则

- 关键数字必须对应真实证据或后端可复现计算；结论旁保留引用。
- 数值由代码计算，语言由模型组织。模型不得自行生成 confidence、命中率、回测绩效。
- 日期、百分比、单位换算等扫描容差是审计实现细节，不得利用容差塞入无依据数字。
- `evidence_scan.clean=true` 只表示扫描未发现疑似数字，不证明所有语义都真实。
- 证据冲突时保留来源、时点和差异，不擅自选择更符合预设观点的一条。
- 数据缺失写 `未获得/unavailable + 原因 + 影响`，不得用“通常”“大概”补值。

## 6. 时态、PIT 与回测

- 每个问题区分事件时间、数据归属期、入库时间、抓取时间和研究 as-of。
- 历史研究只能使用当时可见的信息；PIT 基本面和一致预期必须带 as-of。
- 回测披露样本区间、成本、滑点、停牌/涨跌停、复权、退市和幸存者偏差处理；未处理项列为限制。
- 不用全样本调参后的最优结果冒充样本外表现；样本内外必须分开。
- 过期数据不得生成新的可执行信号；能否用于历史描述取决于具体 fresh flag 和问题。

## 7. 置信度与结论

- confidence 只接受后端 `compute_confidence` 或对应服务结果。
- 默认解释组件为 coverage/freshness/agreement/history，模型自报只可作为无效附加文本，不能覆盖。
- 低置信度或关键证据不足时，只输出观察、补证和验证路径，不给方向性行动指令。
- PM 结论至少包括：view、claims、evidence_refs、invalidation、verify_by、horizon、disclaimer。
- `position_view` 是研究表达，不是订单；不得自动转换为交易参数。

## 8. 输出与外发门

正式交付前检查：

1. 任务终态真实，必要节点无缺失。
2. 证据引用、数字扫描和 confidence 来自后端。
3. 反方、失效条件和数据缺失仍保留。
4. 合规 `passed=true`，flags 已处理，disclaimer 原文保留。
5. 数据许可覆盖当前展示/导出/外发场景。
6. 文件由后端交付物接口生成并验证存在；不存在就报失败。

邮件、微信、公开发布或技能推送还需独立确认接收方、内容、附件、用途和许可。生成交付物不代表已获外发批准。
