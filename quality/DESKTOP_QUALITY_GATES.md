# 塔卡投研桌面版 · 质量门禁

> 版本：1.0.0  
> 当前状态：规则已定义；运行时门禁待授权环境实测。  
> 原则：文档存在不等于能力实现，HTTP 200 不等于研究成功，单路径通过不等于全发行版通过。

## G0 基线与范围

发布/验收前必须记录：

- 三仓库 commit、核心 VERSION、stock/multi_asset manifest、桌面包版本。
- 实际 `finance-desktop.yaml`、构建 channel/edition、裁剪后的 enabled/installed 插件清单；当前事实与 `wechat_personal` 目标移除状态分开记录。
- 模型、数据源、许可、语言、操作系统和测试范围。
- 本次未测试项与已知文档漂移。

任一基线无法确认时，不得写“全量通过”。

## G1 角色与路由门

- 仅使用 finance YAML 的 8 角色，不把 content/service 插件归属变成额外 Agent。
- quick/deep/discuss/report/pipeline 入口选择正确，深度不被前端篡改。
- 显式 DAG 顺序为 plan → collect → audit → 并行分析/估值 → risk → pm → compliance。
- Athena（默认显示名“金牛”）、PM、Compliance、Ops 不越权；缺角色/插件时明确能力缺口。
- 自定义助手显示名只影响 UI/自称，系统角色、任务、handoff、日志和审计仍为 `athena`。
- 通用 roster 不得覆盖 finance 角色集。

**一票否决**：合规提前、风险被跳过、虚构角色执行、把 reviewer 当完整合规。

## G2 数据与证据门

- 关键数字有真实 provenance、单位、来源和 as-of。
- 缺失逐项列原因及影响，不用模型补值。
- 数据过期、许可异常、registry 异常按对应操作 fail-closed。
- 历史研究/回测使用 PIT，未发生未来数据泄漏。
- evidence scan、self audit、confidence 均来自后端，扫描限制有披露。

**一票否决**：虚构证据/数字、模型自报置信度覆盖代码、商业数据无许可外发。

## G3 研究与表达门

- 结论包含适用范围、反方、invalidation、verify_by、horizon。
- 低置信度/关键缺失只做观察和补证，不给方向性行动指令。
- 回测披露样本、成本和偏差；仿真结果带 paper 标识。
- 无收益承诺、精准预测、保证胜率或伪造目标价。
- 风险提示和 disclaimer 原样保留。

## G4 合规与审批门

- 正式交付按用途完成确定性合规；error flag 阻断。
- `compliance_unavailable` 不得被解释为完整合规通过。
- 外部发送、公开发布、敏感配置和破坏性动作有明确批准。
- 批准内容、接收方、范围和许可一致；审批不可由模型伪造。
- 真实交易不作为当前能力；Agent 不自动执行仿真下单。

## G5 权限与隔离门

- 401/403/404/409/422/429/503 语义不被前端改成统一“成功/失败”。
- JWT 权限、角色工具、插件 capability、数据许可和人工批准逐项满足。
- 单用户本机实例、项目、记忆和研究数据无跨域泄漏；不要求 tarka.cn 网站账号，本机认证不得被描述成网站登录。
- 存量版本仍启用微信时验证 sender/group 白名单、require-at、重复消息规则；目标移除后验证通道与死入口均不存在。
- 首次启用可跳过；默认自称金牛、内部角色 athena 不变；未同意不持久化偏好，同意后仅本机保存。
- 日志、导出和回复不包含秘密或完整个人标识。

## G6 后端与前端边界门

- 指标、估值、信号、证据、置信度、合规均在 VeroRun 后端。
- 前端只调用已有 API、展示结构化结果、触发任务和处理状态。
- 后端缺能力时停止并提后端需求，无临时前端算法。
- 路由存在且与当前核心版本匹配；404 显示真实路径，不伪造功能。
- 后端 warnings、partial、missing、paper、disclaimer 均透传。

## G7 桌面构建门

在获得明确测试范围后执行并保存输出：

1. core staging 使用 `finance-desktop.yaml`。
2. `verify:edition-plugins` 无白名单外 enabled/installed 插件。
3. finance 构建不包含 research/tarka/官方服务器专属插件。
4. ops/SSH/browser 不混入 finance core 路由；`wechat_personal` 当前仍在白名单、目标移除尚未执行，发布前按实际变更状态验收，实施后不得有残留插件或 UI/API 死入口。
5. 安装包携带匹配的 Python、PG、核心与插件，版本不是仅改 package.json。

## G8 本地生命周期门

- 正常冷启动、异常恢复启动、正常关窗、再次启动分别验证。
- PG → admin → health 顺序与状态可见，未就绪不进入业务请求风暴。
- 不强杀 Electron/PG；`pg_ctl` 30s 不被放大，pg_isready 轮询有效。
- 异常恢复时有明确等待/错误提示，无白屏静默。
- 代理清理、端口占用和管理员权限场景有可操作错误。

## G9 交付物门

- 由后端交付物接口从证据缓存生成，真实文件存在。
- 内容与研究报告一致，无数字丢失、反方缺失或 disclaimer 删除。
- 导出/外发许可重新校验，接收方明确。
- 生成失败返回 failed/partial，不创建空文件冒充成功。

## G10 回归与证据门

每次变更必须附：

- 修改文件与后端 API。
- 测试环境、数据/模型/许可范围和命令。
- 原始结果、失败项、重试结果与日志定位。
- 已知漂移和未测范围。
- 回滚方式。

### 完成声明模板

> 在 {commit/version + OS + edition} 下，已验证 {exact scope}。调用 {method + path}，结果为 {status}；证据位于 {log/report path}。未测试 {scope}，仍存在 {missing/risks}。前端仅做 {calls/rendering}，未新增业务计算。

缺少上述信息时，只能声明“文档已编写/静态检查已完成”，不能声明“桌面版全链路验收通过”。
