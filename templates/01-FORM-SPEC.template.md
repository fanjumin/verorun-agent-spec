# {{FORM_NAME}}.PROJECT.md — 形态/发行版项目级规则

> 版本：{{VERSION}}  
> edition：{{EDITION}}（必须来自 `deploy/editions/*.yaml`）  
> context：{{CONTEXT}}（local / site / desktop）  
> 继承：`agent_matrix/kernel.AGENTS.md` L1、`{{EDITION}}.AGENTS.md` L2；只加严，不放宽。  
> 不替代仓库根 `AGENTS.md`。

## 1. 使命

{{MISSION}}

## 2. 文档边界

- 开发文档（根 `AGENTS.md`、`ARCHITECTURE.md`、README）不注入运行期。
- 运行期加载链：L1 → L2 → `AGENTS.default.md` → `roles/*.yaml` + `prompts/*` → `USER.md` → `SOUL.md`。

## 3. 范围

**做：** {{IN_SCOPE}}

**不做：** {{NON_GOALS}}

## 4. 数据与工具边界

- 允许数据：{{DATA_SCOPE}}
- 允许工具：{{ALLOWED_TOOLS}}
- 禁止工具/动作：{{FORBIDDEN_TOOLS}}
- 跨租户/跨 edition：拒绝。

## 5. 审批门禁

{{APPROVAL_GATES}}

## 6. 输出合同

{{OUTPUT_CONTRACT}}

## 7. 失败语义

- 工具/模型/插件不可用：`blocked` / `failed`，附原因。
- 缺事实：`unavailable` / `needs_confirmation`，不补合理值。
- 边界冲突：`BOUNDARY_VIOLATION`。

## 8. 变更

版本号 + `CHANGELOG.md`；不得新造 edition；不得覆盖 L1。
