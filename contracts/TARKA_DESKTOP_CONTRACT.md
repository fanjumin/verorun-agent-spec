# TARKA_DESKTOP_CONTRACT.md — 塔卡投研桌面版 · 领域合同

> 版本：3.0.0 · 2026-11-28  
> 领域：finance-desktop 桌面形态 + 移动端配套。继承 L1/L2 与 `TARKA_DESKTOP.PROJECT.md`。  
> 本合同回答一个问题：**什么必须为真，交付才被接受。**  
> 研究节点与证据细节见 `contracts/TARKA_DESKTOP_RESEARCH_FLOW.md`；本文件不替代后端强制校验。

## 1. 交付物定义

| 交付物 | 权威来源 | 验收门 |
| --- | --- | --- |
| 桌面安装包（win x64） | `release-finance/` 打包链 | 内核版本与移动端 BFF 能力对齐；许可闸 fail-closed |
| Android 壳 APK | `verorun-mobile/android/build.py --brand brands/<id>` | 同包名复用同一签名库；不含任何证书/凭据 |
| 移动端 PWA | 桌面端 `/m/`（server 侧）+ 中继路由 | SW 缓存红线生效；断网不回空白页 |
| 下载中心条目 | 后台「下载管理」（CMS 动态页） | 直链 302 + sha256 入库 + 页面可下载 |
| 中继服务 | 服务器 `mobile_relay.py`（tools/ 同源） | healthz 200；TERM ≤5s 优雅退出；白名单 403 红线 |
| 首次启用与称呼偏好（目标） | `ONBOARDING.DESKTOP.md` + 本地后端设置/记忆 | 默认金牛、内部 athena 不变；可跳过；同意后本机保存；无 tarka.cn 登录要求 |

## 2. 三端连通契约（TP-2026-1127 保留用例）

每次影响连通性的变更后必须复验，全绿才算交付：

1. **L1 注册面**：桌面启动即注册中继（壳自动拉起 relay_agent），`healthz agents≥1`；错 SECRET → `bad_hello` 拒绝。
2. **L2 探测面**：手机 resolveRoute 按 relay→direct 顺序；iid mismatch 判 false；双不可达进 offline 快照态。
3. **L3 业务面**：经中继 `/m/instance` 200 且 instance_id 一致；`/m/api/*` 匿名 401（BFF 存在 + 鉴权墙完好）；吊销设备 token 后内核 401（中继放行是符合设计）。
4. **红线（一票否决）**：instance_id 串实例；路径白名单可打到 `/admin/users`；断网时 PWA 缓存命中旧 200；任何技术错误文本上屏（必须人话）。

## 3. 输出内容契约（投研领域）

每条投研结论必须同时满足：

- `as_of`（数据时点）+ `source`（数据源）+ `method`（口径）三要素齐全；
- 方向性判断带置信度 + 支撑证据 + **反证**（FIN-03/09）；
- 数据过期输出"数据过期，不研判"，禁止用旧数据继续叙事（FIN-02）；
- 第三方商业数据在 `ma_license.allow_llm=false` 时不进任何模型调用，缓存同等受限（FIN-05）；
- 最终交付含"非投资建议"声明；合规否决原样呈现，不得改写（FIN-08 协议留痕）。

### 3.1 终态与入口合同

- `success`：本次必要步骤、证据验证和输出级合规全部完成。
- `partial`：存在可用结果，但数据、角色、验证或许可未完成；必须同时列 `missing`，不得包装成完整研报。
- `blocked`：权限、许可、合规或审批阻断；不得改写措辞绕过。
- `failed`：技术执行失败；HTTP 200 不能单独证明研究成功。
- `/api/discuss` 是 planner → reviewer → revise → decider 的讨论路径；reviewer 不等于完整确定性合规。
- `/api/research/report` 是独立深度报告路径，不宣称等同于显式 DAG。
- `/api/research/pipeline` 才按 9 节点合同验收，末道为 `rs_compliance`。
- confidence、evidence scan、self audit、指标与合规只接受后端结果；模型或前端不得覆盖。
- 正式外发遇 `compliance_unavailable` 不得宣称完整合规通过，应阻断或转人工复核。

### 3.2 研究与动作隔离

研究生成、交付物生成、导出、邮件/微信外发、公开发布、仿真下单和真实交易是不同动作。前一动作完成不授予后一动作权限；`position_view` 不是订单，仿真成交不是真实收益，真实交易不属于当前 Agent 能力。

## 4. 证书与凭据契约

- 源码仓库（git 跟踪内容与全部历史）**零证书、零私钥、零签名库、零 SECRET**。
- 本机产物（`.lan-gateway/`、`android/keys/`、`relay.env`、`secrets.json`）由 gitignore 双闸隔离；新增密钥类产物时**先补 gitignore 再生成文件**。
- 用户授权一律本机完成（Ed25519 配对 + device token）；远程面只路由不鉴权。禁止任何"服务器代持凭据"方案。

## 5. 变更纪律

- 破坏性/架构级操作（改窗口路由链、edition 定义、L1/L2、许可逻辑、中继协议）必须先与用户确认，Agent 不得单方实施。
- 每次会话结束前：变更与未测范围清晰，不擅自回退他人改动、不自动提交；要求的测试门有原始证据，交付声明附证据（改了什么、调了哪个 API、怎么验证）。
- 对"已完成"的声明若无证据链视为无效（继承前端仓 AGENTS.md 违规处理条款）。
