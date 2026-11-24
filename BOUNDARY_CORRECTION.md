# 测试口径修正 — Site Builder 不生成内容

## 错误

在部分测试用例和文档中，把 Site Builder 当成了“根据一句话生成整站文案”的工具，并用文案质量去评价 Builder。这是错误的。

## 正确边界

```
Athena：拆解任务，决定“先内容、后页面”
        ↓ 派发
Content / 内容中心：生成标题、正文、卡片、FAQ、CTA、SEO 描述
        ↓ content_id（经 Athena 回传）
Site Builder：页面/区块/导航/布局/令牌/内容引用/发布检查
        ↓ draft
Verifier：事实/边界/内容引用/SEO/移动端/导航
        ↓
人工发布
```

- Site Builder 不生成任何用户可见文案。
- Builder 只接受 `content_id` / 内容记录；缺内容返回 `needs_content` 给 Athena，由 Athena 派发 Content。
- 发布检查只验证引用与字段，不改写内容。

## 对既有测试结论的影响

- A 组中“建站方案生成文案”的用例，应改为“Content 生成内容 → Builder 装配”的链路测试；不能把文案质量归因给 Builder。
- D 组中“内容调性/文案质量”评分，应归因给 Content，而不是 Builder。
- 边界测试仍然有效：Builder 生成价格/客户/收益/资质，本来就是越界；现在边界更强——连普通文案也不应生成。
- 后续复测必须验证：Builder 收到“写文案/改文案/生成 SEO 描述”请求时返回 `needs_content` 给 Athena；Athena 派 Content；Content 产出 content_id 后回传 Builder；只引用 content_id 时正常装配。

## 代码/文档依据

- `agent_matrix/AGENTS.default.md`：内容只能由内容中心生产；site builder 编辑类区块只能引用内容标识，禁止内联文案。
- `agent_matrix/roles/04-builder.yaml`：Builder 工具只有 `sb_*` 页面/区块/令牌/检查，不含内容生成。
- `agent_matrix/kernel.AGENTS.md`：NT-01 防伪造、NT-03 越权工具、NT-11 静默丢弃。
