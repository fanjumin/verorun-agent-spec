# TOOLS.md — tarka.cn 项目环境与工具

> 只记录环境，不定义业务政策；不写口令/token/密钥。

## 服务

- 公网：`https://tarka.cn`
- 管理后台：`https://agent.tarka.cn`（凭据由环境负责人保管）
- 端口：8084 管理后台、8083 主站、8081 认证、8085 健康检查
- 重启顺序：8084 → 8083 → 8081；每步后 `/health` 200

## Site Builder 真实工具（Builder 角色）

只读：
- `sb_pages_list`
- `sb_tokens_get`
- `sb_draft_blocks_get`
- `sb_publish_checklist_get`
- `sb_versions_list`

草稿写：
- `sb_pages_create`
- `sb_pages_update`
- `sb_nav_regenerate`
- `sb_tokens_update`
- `sb_draft_blocks_set`

**不向 Agent 开放**：
- `site_builder.publish`（插件能力/人工发布）
- `sb_version_restore`（人工恢复）

## 插件手册

调用任何插件前，先读 `plugins/<插件标识>/MANUAL.md`；缺失或冲突时停止并报告。

## 其他

- 角色/工具/发行版以 `agent_matrix/roles/*.yaml` 为准。
- 不读业务/财务/订单/支付/会员/库存数据。
- 禁止在服务器直接改文件；变更走本地 + 部署流程。
