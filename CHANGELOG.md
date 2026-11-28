# CHANGELOG

## 3.1.0 — 2026-11-28

- 明确产品为**单用户本机桌面应用，不是多租户平台**；无需注册或登录 `tarka.cn` / `agent.tarka.cn`。区分本机核心认证与网站账号，可选中继不承载网站登录或偏好同步。
- 记录新决策：目标将 `wechat_personal` 移出发行白名单。当前 YAML 仍包含、变更尚未执行，因此文档同时保留“当前 14 插件/目标 13 插件”，未虚写为已移除；增加实施后无残留插件和 UI/API 死入口的验收门。
- 主 Agent 默认显示名与自称改为**金牛**，内部 `athena` slug、角色文件、任务、handoff、工具、DAG 和审计关联保持不变；支持本机自定义显示名。
- 新增 `ONBOARDING.DESKTOP.md`：首次启用可跳过，首轮只问用户/助手称呼；未明确同意仅当前会话生效，同意后仅本机保存；onboarding 状态与长期偏好分离，支持修改、删除与恢复默认。
- 更新基线、项目规则、USER、SOUL/TONE、TOOLS/MEMORY、Registry、金牛与 Ops 章程、计划、质量门和运维手册；评测集增加身份/首次启用及插件移除目标门，共 67 条，仍为 `designed_not_run`。
- 本次仅更新 Spec 和受影响项静态复核，未执行白名单变更、首次启用开发或运行时测试。

## 3.0.0 — 2026-11-28

- 完成 VeroRun、verorun-workplace、verorun-agent-spec 三仓库静态复核，新增 `TARKA_DESKTOP_BASELINE.md`：记录 HEAD、真实职能、8 角色、14 插件、三条研究路径、桌面裁剪链与文档/代码漂移；未把静态阅读写成运行测试。
- 补齐桌面版运行约规：`SOUL.DESKTOP.md`、`TONE.DESKTOP.md`、`TOOLS.DESKTOP.md`、`MEMORY.DESKTOP.md`、`AGENT_REGISTRY.DESKTOP.md`。
- 新增 8 个 `agents/AGENT_DESKTOP_*.md` 角色章程，明确职责、输入输出、工具边界、失败与验收。
- 新增 `contracts/TARKA_DESKTOP_RESEARCH_FLOW.md`：区分 discuss、research/report、research/pipeline；显式 DAG 固定为 plan → collect → audit → 并行分析/估值 → risk → pm → compliance。
- 新增 `quality/DESKTOP_QUALITY_GATES.md`、`quality/DESKTOP_EVALS.md` 与 `operations/DESKTOP_RUNBOOK.md`；评测状态为 `designed_not_run`。
- 更新 `README.md`、`TARKA_DESKTOP.PROJECT.md`、`USER.DESKTOP.md`、`contracts/TARKA_DESKTOP_CONTRACT.md` 和 `plans/TARKA_DESKTOP_PLAN.md`，统一当前插件、仿真交易、终态、合规异常和完成声明口径。
- 本次未修改 VeroRun/workplace，未启动桌面/数据库/模型，未执行项目测试或安装包构建。

## 2.1.0 — 2026-11-27

- 新增**塔卡投研桌面版约规集**（finance-desktop）：
  - `TARKA_DESKTOP.PROJECT.md` — 三端架构铁律（桌面零对外监听/服务器只做闸门/授权在本机）、证书与密钥纪律（2026-11-27 全仓审计结论：git 内容与历史零证书）、移动端边界（BFF 唯一入口/SW 缓存红线/配对闸）、FIN 系列执行口径、CMS 动态页纪律（含 upsert 整行覆盖陷阱）。
  - `USER.DESKTOP.md` — `context: desktop` 授权（data_scope=own、can_trade=false 永久禁止、local_authority=true）。
  - `contracts/TARKA_DESKTOP_CONTRACT.md` — 交付物验收门 + TP-2026-1127 连通契约（红线一票否决）+ 投研输出三要素 + 变更纪律。
- 依据：TP-2026-1127 三方连通测试报告与修复实施（中继注册/BFF cherry-pick/SIGTERM 修复/UI 上传链打通）。
- 更新 README 阅读顺序与目录。

## 2.0.5 — 2026-11-24

- 明确编排关系：内容生成由 Athena 派发 Content；Builder 不自行转派、不直接调用 Content。
- Builder 缺内容返回 `needs_content` 给 Athena，Content 产出 `content_id` 后经 Athena 回传 Builder。
- 更新项目规则、Site Builder 合同、Builder/Master/Content 章程、边界修正说明与评测用例。

## 2.0.4 — 2026-11-24

- **重大口径修正**：Site Builder 不生成内容。内容只能由 Content/内容中心生产；Builder 只做页面/区块/导航/布局/内容引用/发布检查。
- 新增 `BOUNDARY_CORRECTION.md`，记录该错误对既有 A/D 组测试归因的影响。
- 更新项目规则、Site Builder 合同、Builder 章程、质量门禁、评测用例：Builder 写文案/SEO 描述必须 `not_permitted` 并转 Content。

## 2.0.3 — 2026-11-24

- 新增 plans/MARKDOWN_DROP_PUBLISH.md：Agent 直接投递 .md 的发布管线，区分内部 docs 容器与 tarka.cn 公网文章两条轨道。

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
