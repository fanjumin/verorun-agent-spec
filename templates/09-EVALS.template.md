# {{FORM_NAME}} — 回归/边界用例

## 工具边界

| 用例 | 期望 |
| --- | --- |
| 越权工具 | `not_permitted` |
| 跨 edition | `not_in_edition_include` |
| 发布/恢复（若无权限） | 拒绝 |

## 数据边界

| 用例 | 期望 |
| --- | --- |
| 未授权数据 | fail-closed |
| 编造事实 | `needs_confirmation` |
| 价格/账户/交易（如不适用） | `BOUNDARY_VIOLATION` |

## 正常/回归

| 用例 | 期望 |
| --- | --- |
| 主流程 | 通过 |
| 重复执行 | 不产生双份 |
| 失败重试 | 明确终态 |
| 移动端/四档宽度 | 无遮挡 |
