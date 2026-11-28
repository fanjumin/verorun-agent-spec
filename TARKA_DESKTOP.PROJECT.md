# TARKA_DESKTOP.PROJECT.md — 塔卡投研桌面版 · 项目级约规

> 版本：3.1.0 · 2026-11-28  
> 继承 L1（`agent_matrix/kernel.AGENTS.md`）与 L2（`agent_matrix/editions/finance-desktop.AGENTS.md`），不替代、不加发行版。  
> 依据：`TARKA_DESKTOP_BASELINE.md`（三仓库静态复核）+ `plans/TARKA_DESKTOP_PLAN.md` + 2026-11-25~27 三端连通实施中验证过的事实（TP-2026-1127）。  
> 读者：塔卡投研桌面版相关的运行期角色与开发 Agent。运行期注入以本文件为项目级补充；开发施工铁律以各仓库根 `AGENTS.md` 为准。

## 1. 形态与三端架构（唯一事实）

```
手机 App/PWA ──https──▶ 中转（agent.tarka.cn /relay/i/<iid>/...）
                          │   nginx /relay/ → mobile_relay.py（数据面，纯路由）
桌面端（用户本机） ◀──────┘   relay_agent.py 出站 wss 注册，零入站端口
                          │
                          └──▶ 本机内核 :8084（鉴权权威、数据权威、业务权威）
```

铁律（违反任何一条即架构违规）：

1. **桌面端零对外监听是刻意的安全裁决**。任何"给桌面端开公网端口/域名"的方案一律否决；远程接入只经中转。
2. **远程服务器只是闸门**：中转只做「按 instance_id 路由 + 路径白名单 + 并发限流」；**不验设备 token、不落库、不记正文**。鉴权权威在本机内核（`shared/device_token.py`）；被吊销设备能连上中转，但每个请求在本机内核拿 401。
3. **授权发生在本机，产品是单用户桌面而非多租户平台**：设备配对（Ed25519 公钥登记 + device token 签发）、许可校验、数据解密全部在用户本机完成。用户不需要注册或登录 `tarka.cn` / `agent.tarka.cn`；网站后台只是运营者管理面，不是用户数据通道或账号体系。本机核心登录/解锁属于本机安全机制，不等于网站登录。
4. **W8 路由顺序**：手机基址探测 relay 优先、LAN 直连兜底（2026-11-25 裁决 Q2=A），顺序勿对调；`instance_id` 不匹配一律判 false，绝不静默连到别的实例。
5. 中继域名当前为 `agent.tarka.cn`（独立 `/relay/` location 块）。`api.tarka.cn` 保持 444 关闭是正确的"不误放"；若未来拆分机器面 vhost，需先落地 nginx 变更评审，不得由 Agent 单方实施。

## 2. 证书与密钥纪律（2026-11-27 全仓审计结论，永久生效）

审计结论：三个源码仓库（VeroRun / verorun-workplace / verorun-mobile）**git 跟踪内容与全部历史中不存在任何证书、私钥、签名库**。以下纪律保证它持续为真：

| 产物 | 位置 | 生成方式 | 入库策略 |
| --- | --- | --- | --- |
| LAN https 证书 | `<repo>/.lan-gateway/`（ca/server 的 key+crt） | `tools/lan_gateway.py` **首次运行本机生成**，SAN 含当前局域网 IP，到期重跑续签 | **禁止入库**（gitignore 覆盖） |
| APK 签名库 | `verorun-mobile/android/keys/*.keystore` | 本机 keystore | **禁止入库**（`.gitignore: android/keys/`）；同包名必须复用同一库，丢失 = 无法覆盖安装 |
| 品牌固定 CA | `brands/<id>.<用途>/pinned-ca.crt` | 构建时从本机 `.lan-gateway/ca.crt` 复制 | **禁止入库**（`.gitignore: brands/*`） |
| 打包版中继配置 | `<userData>/relay.env` | 用户本机配置（MOBILE_RELAY_* 四件套） | **禁止入库**；打包载荷不含 .env 是安全设计，不得绕过 |
| 随包 secrets | `<nativeBase>/secrets.json` | 壳层持久化（JWT/FLASK/ENCRYPTION/HEALTH） | 本机文件，随数据目录隔离 |

追加规则：

- 任何代码/配置/文档**不得硬编码**证书、私钥、密码、SECRET、iid、服务器凭据。需要机器差异时用"运行时生成 + 本机文件"，需要环境差异时用 env 注入。
- 发现工作区出现未跟踪的 `*.key/*.pem/*.keystore` 文件时：确认 gitignore 覆盖 → 不移动、不提交、不外传；证书类文件不进 `.screenshots/` 等归档目录。
- `certifi`/`stripe` 等 venv 依赖内的公共 CA 束属于第三方包内容，不是本机证书，无需处理。

## 3. 移动端边界（与手机相关的一切）

- 手机端是**纯消费者**：只调用本机内核已有 API（`/m/instance`、`/m/api/*` BFF、`/m/mail/*`、`/m/pair/*`）；禁止在前端实现业务计算、指标、研判、confidence 决策（继承前端仓 AGENTS.md）。
- `/m/api/*` BFF 是手机首屏/自选/产物的**唯一入口**（master `6abb37cc` 起）；新增手机能力先扩 BFF，不让手机直连插件或 agent_matrix 内部端点。
- Service Worker 缓存红线：`/m/instance`、`/m/bindings`、`/m/api/`、`/m/mail/`、`/m/pair/`、`/m/auth/` **永不缓存**；缓存优先会让断网时探针命中旧 200，比报错更糟。
- 配对闸②（付费闸）：`mobile_requires_paid` 403 + `purchase_url` 下发，fail-closed；闸③（中继反查 entitlement）默认关，开闸前提见 relay.README。
- 配对码载荷（`pair_payload`）只含一次性码 + 地址 + iid，**不塞长期凭据**；桌面壳通过 `MOBILE_RELAY_PUBLIC_URL` 注入中继地址，配对二维码由桌面端"连接手机"页动态生成（短时效自刷新），任何人不得再造第二个配对码来源。

## 4. 数据许可与合规（FIN 系列强制）

继承 `plans/TARKA_DESKTOP_PLAN.md` §3 全部强制项，此处只重申执行口径：

- FIN-02：所有价格/估值/财务/资金流输出必须带 `as_of` + `source` + `method`；数据过期只输出"数据过期，不研判"，禁止补值研判。
- FIN-05：第三方商业数据送 LLM 前查 `ma_license.allow_llm`；**缓存命中同样受限**，fail-closed。
- FIN-08/09：四阶段讨论协议（Planner→Reviewer→Revise→Decider）逐步留痕；空头意见不可折叠，最终交付必须可见。
- 最终交付必须含"非投资建议"声明与证据链；合规否决不得被改写或软化。

## 5. 内容与站点边界（桌面版视角）

- 下载中心（`/download/<slug>`）是 **CMS 动态页**：后台「下载管理」维护条目，前台模板渲染；**禁止为发布物手写静态页面**。
- 后台下载条目更新接口是**整行 UPDATE**（`upsert_download`）：任何 API 方式更新必须带全字段载荷，只传单字段会把其余列清空（2026-11-27 已发生并恢复一次）。
- 发行物上传：`MAX_CONTENT_LENGTH` 是部署实例配置（当前 100MB）；大文件优先走后台 UI 上传通道，绕行 scp 直传仅限运维且需同步补齐元数据（sha256/size）。
- 实例品牌（tarka 等）专属配置一律走品牌目录/环境注入；**源码仓库不得出现任何实例专属硬编码**（继承 2026-11 用户裁决）。

## 6. 变更与验收基线

- 涉及三端连通性的改动，回归 `TP-2026-1127` 测试计划的 L0–L4 矩阵（中继健康/白名单/W8 顺序/吊销 401/多实例隔离为红线）。
- 中继进程变更必须验证 SIGTERM 优雅退出（验收：TERM 后 ≤5s 退出，日志完整走 stopping 链）——Python 3.12 下 `async with server` 会永久挂起，已被修复，勿回退。
- 托管方式：服务器无 root 时用 cron 看门狗（幂等 start.sh）等效 supervisor；有 root 时应迁移 systemd 并保留幂等判定。
- 前端/桌面仓验收三件套：typecheck / check-i18n / vitest 全绿；commit message 一律英文。

## 7. 配套约规与执行口径（3.0.0）

- `SOUL.DESKTOP.md` / `TONE.DESKTOP.md`：研究人格、价值排序、数字与失败表达。
- `TOOLS.DESKTOP.md`：当前 14 插件、目标 13 插件及 R0–R5 副作用分级；`wechat_personal` 为“决定移除、尚未执行”。
- `MEMORY.DESKTOP.md` + `ONBOARDING.DESKTOP.md`：本机偏好、首次启用、称呼、记忆同意与数据分层。
- `AGENT_REGISTRY.DESKTOP.md` + `agents/AGENT_DESKTOP_*.md`：8 角色注册、交接、输入输出与禁止事项。
- `contracts/TARKA_DESKTOP_RESEARCH_FLOW.md`：discuss、research/report、research/pipeline 三入口分离；显式 DAG 固定为 plan → collect → audit → 并行分析/估值 → risk → pm → compliance。
- `quality/DESKTOP_QUALITY_GATES.md` / `quality/DESKTOP_EVALS.md`：质量门与评测设计；未实测不得写通过。
- `operations/DESKTOP_RUNBOOK.md`：原生 Windows 核心、PG 恢复、裁剪构建和事故处置。

冲突处置：代码/manifest/发行 YAML、L1/L2、项目约规、使用手册/计划各自按适用范围执行；不得用旧文档扩大权限或降低安全等级。当前代码仍含 `wechat_personal`，新决策要求目标移除但尚未执行；完成前不得写成已移除，完成后以 13 个业务插件清单验收。具体漂移见 `TARKA_DESKTOP_BASELINE.md`。

本次 3.1.0 只完成静态分析、决策增量与文档建设；桌面启动、项目测试、安装包、首次启用实现和运行时评测仍需按明确范围另行执行。

## 8. 主 Agent 身份与首次启用（3.1.0）

- 系统角色固定为 `athena`；默认产品显示名和自称为 **金牛**。不得全局重命名 Athena slug、角色文件、任务 role、handoff、工具或审计键。
- 用户可在本机自定义助手显示名，也可设置自己希望被如何称呼；自定义只影响 UI/自称，不创建新 Agent。
- 首次启用遵循 `ONBOARDING.DESKTOP.md`：默认可用、可跳过，首轮只问用户称呼和助手称呼，明确同意后才跨会话保存偏好。
- onboarding 状态存本地后端设置；偏好未同意时仅当前会话有效，同意后只存本机，不同步网站或其他实例。
- 本产品是单用户本机应用，不要求 tarka.cn 账号；本机核心登录/解锁不属于网站登录。

