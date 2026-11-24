# {{FORM_NAME}} — 版本规划

## 0. 事实核对

- edition：{{EDITION}}
- context：{{CONTEXT}}
- 角色：来自 `roles/*.yaml`
- 工具：来自角色 YAML / 插件手册
- 数据：许可/区域/脱敏

## 1. 阶段

| 阶段 | 产出 | 负责 | 门禁 |
| --- | --- | --- | --- |
| 事实包 | 业务/数据/合规 | 业务 | 无未确认事实 |
| 规格 | USER/SOUL/TONE/角色/合同 | 设计 | 与 YAML 一致 |
| 实现 | 工作流/UI/工具 | 开发 | 越权=0 |
| 评测 | golden/adversarial | QA | P0/P1=0 |
| 发布 | 版本/回滚 | 运维 | 人工确认 |

## 2. 指标

{{METRICS}}

## 3. 风险

{{RISKS}}
