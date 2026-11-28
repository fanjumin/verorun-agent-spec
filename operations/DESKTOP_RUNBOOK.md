# 塔卡投研桌面版 · 运维手册

> 版本：1.0.0  
> 适用：Windows standalone finance-desktop。  
> 本手册记录操作规程，不表示本次已执行这些命令或完成运行验证。

## 1. 运行拓扑

- Electron 主进程托管本地 VeroRun 核心。
- Windows standalone 使用原生 Python + waitress + 便携 PostgreSQL，不走 WSL2。
- 启动顺序：PostgreSQL → admin → health → 业务窗口放行。
- 默认本地使用，不因桌面运行就开放公网端口。
- core 业务窗口与 SSH/browser 独立窗口分离；finance 构建默认裁剪 ops/browser。

## 2. 日常启动与关闭

1. 使用产品提供的启动入口或经批准的开发命令。
2. 观察核心状态，不在未就绪时反复刷新或并发启动第二实例。
3. 业务结束后正常关窗，让主进程执行服务与 PG 优雅关停。
4. 不用任务管理器、`taskkill` 或强杀进程树代替正常关窗。
5. 若启动异常，先保留日志并诊断，不连续强杀重试。

## 3. 开发模式

在 workplace 的 PowerShell 中，按项目规则清理代理并使用 beta 开发通道：

```powershell
$env:HTTP_PROXY = $null
$env:HTTPS_PROXY = $null
$env:http_proxy = $null
$env:https_proxy = $null
$env:VERORUN_CHANNEL = 'beta'
npm run dev:finance
```

说明：

- 该命令用于本机开发，不是生产授权绕过方案。
- 不重打包时使用 dev；正式验收必须使用对应安装包。
- 代理、授权或端口问题要分别记录，不能把所有白屏归因于 PG。

## 4. PostgreSQL 诊断

先找到当前运行时实际的 `pg\bin` 与 `pgdata`，再执行只读诊断：

```powershell
& "<实际路径>\pg_controldata.exe" -D "<实际 pgdata 路径>"
```

判读：

- `Database cluster state: in production`：上次未干净关闭，下次可能执行长时间崩溃恢复；不要强杀，不要反复清零恢复进度。
- `Database cluster state: shut down`：集群干净关闭，可继续排查其他启动环节。

铁律：

- `PG_START_TIMEOUT_MS=30s` 是 pg_ctl 发起启动的上限，不是 PG 就绪预算，禁止放大。
- 就绪只看 `pg_isready`；当前为 300 次轮询、每次间隔 2000ms。
- 手动托管只能使用经批准的常驻任务方式；收尾用 `pg_ctl -m fast stop` 并确认停止。
- 不删除正常存在的 `postmaster.pid` 或数据文件来“修复”启动；陈旧 pid 处理只由已审阅核心逻辑执行。

## 5. 插件裁剪与构建验收

获得明确构建/测试范围后，在 workplace 执行并保存完整输出：

```powershell
npm run stage:core
npm run verify:edition-plugins
npm run build
npm run pack:finance
```

验收点：

1. staging 的 `VERORUN_EDITION=finance` 与目标一致。
2. enabled/installed 均为 YAML `_base + include − exclude`，无额外插件。
3. 当前代码清单仍包含 `wechat_personal`，不包含旧文档中的 `im_gateway`；**已决定移除 `wechat_personal`，但尚未执行**。实施后的发布验收必须确认其不在 enabled/installed/staged/安装包中，且无残留 UI/API 调用。
4. research/tarka/官方服务器专属插件未混入。
5. 安装包内核心、插件、Python、PG 与桌面前端版本匹配。

命令失败时保留原错误，不通过修改白名单或跳过验证来制造通过。

## 6. 健康检查顺序

按层定位，避免“一报错就重启”：

1. 核心状态机：initdb/postgres/core/health/running/degraded/failed。
2. PG 集群状态、端口与 `server.log`。
3. admin/health 进程、端口与启动器日志。
4. 前端 preload/bridge、初始化错误和真实请求 URL。
5. 具体 API 的 401/403/404/409/422/429/503。
6. 插件 enabled、依赖、数据源、许可和任务状态。

日志摘要不包含 `.env` 内容、token、密码、完整个人标识或许可证数据正文。

## 7. 常见错误处置

| 症状 | 先查 | 不得做 |
| --- | --- | --- |
| 白屏 | core 状态、PG 恢复、preload、dev URL、真实 404 | 盲目清缓存/强杀/重打包 |
| `403 subscription_expired` | channel、授权与开发配置 | 修改前端绕过授权 |
| `403 EGRESS_DENIED` | 数据源许可与场景 | 将 license_ids 置空 |
| `404 interface not found` | 前后端版本、插件裁剪、真实路由 | 在前端补业务逻辑 |
| 任务一直 running | job/run 终态、worker、模型/数据源错误 | 重复创建相同任务 |
| 存量版本微信不响应 | 仅在仍随包时查 sender/group 白名单、prefix、require-at、worker；目标版本先确认是否已移除 | 关闭白名单放行所有人，或在已移除版本中伪造通道可用 |

## 8. 变更与回滚

- 变更前记录版本、配置非敏感摘要和可恢复点。
- 核心与前端分别审查；禁止只改前端却声明后端能力完成。
- 回滚优先使用原安装包/版本化产物，不直接删除用户数据。
- 回滚后重跑同范围健康检查和关键研究路径。
- 数据库迁移、插件启停、服务重启和删除类操作按各自项目规则单独批准。

## 9. 事故报告

至少包含：时间、版本、影响范围、最后正常状态、核心/PG/插件状态、脱敏日志、已执行动作、当前风险、回滚与未验证项。

没有实证时使用“疑似/尚未定位”，禁止编造根因或“已彻底修复”。
