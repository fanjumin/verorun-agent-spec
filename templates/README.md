# Agent Spec Kit — 可复制模板

> 用途：为 VeroRun 的每个形态/发行版创建一套 Agent 规约。复制本目录到项目目录，替换 `{{...}}` 占位符。
> 事实源：`agent_matrix/kernel.AGENTS.md`、`agent_matrix/editions/*.AGENTS.md`、`agent_matrix/roles/*.yaml`、`deploy/editions/*.yaml`。
> 禁止：新造 edition、改写 L1、把开发文档（根 `AGENTS.md`、`ARCHITECTURE.md`）注入运行期。

## 术语

- 统称：**Agent Spec（Agent 规约集）**
- 工具实现叫法：**Agent Instructions / Agent Context Files**
- 运行期规约：L1 + L2 + `AGENTS.default.md` + `USER.md` + `SOUL.md` + `TONE.md` + `roles/*.yaml` + `prompts/*`
- 开发规约：仓库根 `AGENTS.md`、`ARCHITECTURE.md`、`README.md`（只给开发者）

## 文件

| 文件 | 作用 |
| --- | --- |
| `00-DOCUMENT-MAP.template.md` | 开发/运行期文档边界 |
| `01-FORM-SPEC.template.md` | 形态/发行版项目级规则 |
| `02-USER.template.yaml` | 真实 USER schema |
| `03-SOUL.template.md` | 使命/价值排序 |
| `04-TONE.template.md` | 语气 |
| `05-AGENTS.default.template.md` | 运行期用户覆盖层 |
| `06-AGENT-CHARTER.template.md` | 单角色章程 |
| `07-DOMAIN-CONTRACT.template.md` | 领域合同 |
| `08-QUALITY-GATES.template.md` | 发布门禁 |
| `09-EVALS.template.md` | 回归/对抗用例 |
| `10-AGENT-REGISTRY.template.csv` | 角色注册表 |
| `11-PROJECT-PLAN.template.md` | 版本规划 |
| `12-CHANGELOG.template.md` | 变更记录 |

## 使用顺序

1. 先读 L1/L2 与 `roles/*.yaml`，确认 edition、context、角色、工具。
2. 填 `01-FORM-SPEC`：使命、非目标、硬边界、审批、输出合同。
3. 填 `02-USER`：context/identity/grants，`can_publish` 默认 false。
4. 填 `03-SOUL`/`04-TONE`：价值排序与语气分开。
5. 用 `10-AGENT-REGISTRY.csv` 从 YAML 逐项校对角色与工具。
6. 用 `06/07/08/09` 写角色、合同、门禁、评测。
7. 最后更新 `12-CHANGELOG` 与版本号。
