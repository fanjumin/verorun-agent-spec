# VeroRun Agent 文档 · tarka.cn 网站项目

> 本目录是 **tarka.cn 网站项目的 Agent 文档**，不是 VeroRun 仓库根 `AGENTS.md`，也不是运行期 L1/L2 宪法。
> 仓库真实分层：`agent_matrix/kernel.AGENTS.md`（L1）→ `agent_matrix/editions/<edition>.AGENTS.md`（L2）→ `agent_matrix/AGENTS.default.md`（用户覆盖层）→ 角色 YAML/Prompt。

## 这套文档解决什么

- 网站形态只做公网站点：品牌、产品、方法论、安全、联系、建站与发布。
- 不碰账户、交易、持仓、价格、订单、支付、客户 PII、投研建议。
- 明确 Builder 的真实工具边界：只读 `sb_*` + 草稿写 `sb_*`；**publish 与版本恢复不向 Agent 开放**。
- 明确“内容中心生产内容、site builder 生成页面、区块只引用内容 ID”的仓库铁律。

## 阅读顺序

1. `TARKA_WEBSITE.PROJECT.md` — 项目级补充规则（继承 L1，不替代 L1）
2. `MULTI_FORM_MATRIX.md` — 多形态/发行版矩阵；只填 tarka.cn，其他待确认
3. `AGENT_REGISTRY.md` — 按 `agent_matrix/roles/*.yaml` 校准的角色/工具/发行版
4. `USER.md` — `context: site` 的 YAML 授权
5. `SOUL.md` + `TONE.md` — 使命/价值排序 + 语气
6. `contracts/SITE_BUILDER_CONTRACT.md` — 网站领域合同
7. `contracts/TARKA_WEBSITE_BRIEF.md` — 线上已确认文案与待业务确认项
8. `agents/AGENT_*.md` — 单角色项目级补充
9. `workflows/WEBSITE_REBUILD.md`、`quality/`、`operations/` — 重构、验收、运维

## 开发文档 vs 运行时文档（必须分清）

| 类型 | 文件 | 读者 | 是否注入运行期 Agent |
| --- | --- | --- | --- |
| **开发施工铁律** | 仓库根 `AGENTS.md` | 改代码的开发者/编码 Agent | **禁止** |
| **开发架构设计** | `agent_matrix/ARCHITECTURE.md`、`agent_matrix/README.md` | 开发者/架构评审 | **禁止**（且可能滞后） |
| **运行期内核宪法 L1** | `agent_matrix/kernel.AGENTS.md` | Athena、全部 sub 角色、orchestrator | 是 |
| **发行版增量 L2** | `agent_matrix/editions/<edition>.AGENTS.md` | 对应 edition 的角色 | 是（按 VR_EDITION） |
| **运行期用户覆盖层** | `agent_matrix/AGENTS.default.md`（部署为 `AGENTS.md`） | 运行期角色 | 是 |
| **角色定义** | `agent_matrix/roles/*.yaml` | 运行期角色/调度 | 是（角色/工具/模块） |
| **角色 Prompt** | `agent_matrix/prompts/*` | 对应角色 | 是（按 PromptResolver） |
| **用户身份/授权** | `USER.md` | 运行期角色 | 是（`context/identity/grants`） |
| **使命与价值排序** | `SOUL.md` | 运行期角色 | 是 |
| **项目补充** | 本目录 `TARKA_WEBSITE.PROJECT.md` | 本项目运行时 | 作为项目级补充，不替代 L1 |

> 结论：根 `AGENTS.md` 和 `ARCHITECTURE.md` 是给开发者看的，不是给运行时 Agent 组看的。运行期以 L1 + L2 + `AGENTS.default.md` + `roles/*.yaml` + `prompts/*` + `USER.md` + `SOUL.md` 为准。

## 与仓库的关系

- **不要**把本目录的 `TARKA_WEBSITE.PROJECT.md` 复制成仓库根 `AGENTS.md`；根 `AGENTS.md` 是开发者施工铁律。
- 如果要将本项目规则纳入运行时，应作为项目级补充被 `AGENT_MATRIX` 加载，或按真实发行版写入 `agent_matrix/editions/<edition>.AGENTS.md`；不得新造 edition。
- 角色、工具、发行版以 `agent_matrix/roles/*.yaml` 与 `deploy/editions/*.yaml` 为准；本文档只做 tarka.cn 项目的加严说明。

## 目录

```
verorun-agent/
├─ README.md
├─ TARKA_WEBSITE.PROJECT.md
├─ MULTI_FORM_MATRIX.md
├─ AGENT_REGISTRY.md
├─ USER.md
├─ SOUL.md
├─ TONE.md
├─ TOOLS.md
├─ MEMORY.md
├─ agents/*.md
├─ contracts/*.md
├─ workflows/WEBSITE_REBUILD.md
├─ quality/QUALITY_GATES.md
├─ quality/EVALS.md
├─ operations/RUNBOOK.md
├─ templates/            # Agent Spec Kit 可复制模板
├─ plans/                # 形态规划（塔卡桌面投研）
└─ CHANGELOG.md
```
