# AGENT_OPS.md — Ops 项目级补充（不适用）

> 以 `agent_matrix/roles/06-ops.yaml` 为准：editions=finance-desktop；automation/health_check/monitor/alerter/workflow/cron/deployment/provision/captcha。

## 网站项目

**不启用。** tarka.cn 的发布/重启/回滚由平台运维人工执行，不交给网站 Agent。网站 Agent 只能生成草稿与检查清单。

运维动作见 `operations/RUNBOOK.md`；任何部署/重启/回滚必须人工确认。
