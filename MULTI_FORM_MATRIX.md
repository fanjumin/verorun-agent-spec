# MULTI_FORM_MATRIX.md — 多形态/发行版矩阵

> 仓库真实发行版：`official`、`finance-desktop`、`research-desktop`（见 `agent_matrix/editions/` 与 `deploy/editions/*.yaml`）。  
> 下表只完整填写 tarka.cn；其他形态必须按实际 `VR_EDITION` 与角色 YAML 校准，不得推测。

| 形态 | 发行版/上下文 | 核心职责 | 允许数据 | 禁止 | 备注 |
| --- | --- | --- | --- | --- | --- |
| **tarka.cn 网站** | `official` + `context: site`（待部署确认） | 公网站点建设/内容/发布 | `site_builder.*`、内容中心、已批准素材 | 账户/交易/持仓/价格/订单/支付/客户 PII/投研建议 | 本目录 |
| 桌面端塔卡投研 | `finance-desktop` + `context: desktop` | 单用户本机投研（无需 tarka.cn 登录）、证据链、反方风险、组合、合规与交付物 | 以发行 YAML、JWT、插件 capability、数据许可和本机授权为准 | 真实交易/未批准外发/跨实例读取/前端业务计算 | 完整规范见 `TARKA_DESKTOP.PROJECT.md` 与配套 DESKTOP 文档 |
| 奥恪科研版 | `research-desktop`（需确认） | 科研/引用/写作 | 以 `research-desktop.AGENTS.md` 为准 | 以该 L2 为准 | 见 `roles/research-desktop/*.yaml` |
| 金融桌面版 | 同“桌面端塔卡投研” | 不另建角色或规范 | 同上 | 同上 | `finance-desktop` 是 VeroRun edition，对外 identity 为 finance/塔卡投研 |
| 客服类 | 按实际 edition | 咨询/工单/分流 | 以该 edition 为准 | 客户 PII/未授权承诺 | 待业务确认 |
| 小程序类 | 按实际 edition | 待确认 | 待确认 | 待确认 | `mini_app_builder` 仅在 Builder 工具集内做规划/构建/查询 |

## 可继承

- L1 `NT-01…NT-12`
- 安全/审批/证据/输出合同
- `AGENTS.default.md` 的用户覆盖层规则（内容与页面分离、插件手册前置、禁止绕过）

## 必须按形态重写

- 角色集与 `managed_modules`
- 工具白名单
- 数据范围 / 许可 / 出站
- 审批门禁
- 输出合同与评测集
