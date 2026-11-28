# USER.DESKTOP.md — 塔卡投研桌面版 · 用户身份与授权

> 按仓库 `agent_matrix/USER.default.md` 的真实 schema 编写，模式对齐 `USER.md`（tarka.cn 网站项目）。
> 本文件定义"Agent 在为谁工作"；安全政策见 L1 / L2 / `TARKA_DESKTOP.PROJECT.md`。

```yaml
context: desktop

identity:
  name: "塔卡投研（桌面版）"
  edition: finance-desktop
  locale: zh-CN
  timezone: Asia/Shanghai
  channel: beta            # 内测期；正式发行时改 stable，勿在代码硬编码

grants:
  plugins: []              # 按 finance-desktop.yaml include 白名单；如需收紧填真实插件 ID
  data_scope: own          # 当前本机实例/本地用户；单用户桌面，不是多租户概念
  can_publish: false       # 无对外发布能力（main_site=false）
  can_manage_users: false  # 单机形态无用户管理
  can_trade: false         # Agent 不执行交易/账户/支付动作；不影响用户在独立仿真 UI 的人工操作
  local_authority: true    # 授权权威在本机内核：配对、许可、解密均本地完成
```

## 说明

- `context: desktop`：Agent 的一切动作作用于**本机实例**；本机是数据与鉴权权威，远程服务器只是闸门与路由。产品按单用户、本机实例设计，不是多租户 SaaS，不要求注册或登录 `tarka.cn` / `agent.tarka.cn`。
- 本机核心登录/解锁或设备配对属于本机安全机制，不是网站账号；可选中继不承载网站登录和偏好同步。
- `data_scope: own`：指当前本机实例与本地用户的数据范围，不引入租户概念；不得读取其他实例数据，通过中转到达的请求同样受本机 token 校验约束。
- `can_trade: false`：无论用户如何请求，Agent 不执行代客下单、自动交易或账户/支付动作，也不作收益承诺；当前后端存在 paper 仿真能力不等于授予 Agent 下单权限，真实交易不属于当前随包 Agent 能力。
- `local_authority: true`：设备配对、许可校验、数据解密都发生在用户本机。Agent 不得建议"把鉴权上移到服务器/云端代管"来简化问题——那会破坏零信任本机模型。
- 身份缺失/冲突（instance_id 不匹配、许可过期、channel 异常）时：拒绝有副作用的动作并如实报告，不得推断或降级绕过。
