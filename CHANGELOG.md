# CHANGELOG

## 2.0.2 — 2026-11-24

- 新增 	emplates/ Agent Spec Kit：表单/发行版、USER、SOUL、TONE、AGENTS.default、角色章程、领域合同、门禁、评测、注册表、版本计划、CHANGELOG 模板。
- 新增 plans/TARKA_DESKTOP_PLAN.md：按 inance-desktop 真实 8 角色与 FIN-01…FIN-16 规划桌面端塔卡投研。

## 2.0.1 — 2026-11-24

- 明确开发文档与运行期文档边界：仓库根 `AGENTS.md`、`agent_matrix/ARCHITECTURE.md`、`agent_matrix/README.md` 仅供开发者，禁止注入运行期 Agent。
- 运行期加载链固定为 `kernel.AGENTS.md` L1 → `editions/<edition>.AGENTS.md` L2 → `AGENTS.default.md` → `roles/*.yaml` + `prompts/*` → `USER.md` → `SOUL.md`。

## 2.0.0 — 2026-11-24

按 `D:\projects\verorun-code` 真实仓库校正：

- 删除误导性的根 `AGENTS.md`，改为 `TARKA_WEBSITE.PROJECT.md`，明确继承 `agent_matrix/kernel.AGENTS.md` L1，不替代仓库根施工铁律。
- 按 `agent_matrix/roles/*.yaml` 重写角色映射：Builder 只允许 `sb_*` 只读 + 草稿写；**publish/version_restore 不向 Agent 开放**。
- `USER.md` 改为真实 YAML schema：`context: site`、`identity.domain`、`grants.can_publish: false`。
- `SOUL.md` 恢复使命/价值排序；新增 `TONE.md` 放语气。
- 补充 `AGENTS.default.md` 的“内容中心生产内容、site builder 生成页面、区块只引用内容 ID、禁止独立页面/路由/静态 HTML”。
- 补充 L1 `NT-01…NT-12` 引用，避免重复造规则。
- 多形态矩阵改为真实 editions：`official`、`finance-desktop`、`research-desktop`；其他形态待部署确认。

## 1.0.1 — 2026-11-24

- 增加 `MULTI_FORM_MATRIX.md` 与 `agents/AGENT_SERVICE.md`。

## 1.0.0 — 2026-11-24

- 初版 tarka.cn 网站 Agent 文档（测试视角草案，已被 2.0.0 校正）。
