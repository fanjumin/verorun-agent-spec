# {{FORM_NAME}} — 文档边界

| 类型 | 文件 | 是否注入运行期 |
| --- | --- | --- |
| 开发施工铁律 | 仓库根 `AGENTS.md` | 禁止 |
| 开发架构设计 | `agent_matrix/ARCHITECTURE.md`、`README.md` | 禁止（可能滞后） |
| 运行期 L1 | `agent_matrix/kernel.AGENTS.md` | 是 |
| 运行期 L2 | `agent_matrix/editions/{{EDITION}}.AGENTS.md` | 是（按 VR_EDITION） |
| 用户覆盖层 | `agent_matrix/AGENTS.default.md` | 是 |
| 角色/工具 | `agent_matrix/roles/*.yaml`、`prompts/*` | 是 |
| 用户身份/授权 | `USER.md` | 是 |
| 价值排序 | `SOUL.md` | 是 |
| 项目补充 | `{{FORM_NAME}}.PROJECT.md` | 作为项目级补充，不替代 L1 |

结论：开发文档管“怎么写代码”，运行期文档管“Agent 怎么做事”，不得互相注入。
