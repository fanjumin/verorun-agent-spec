# USER.md — tarka.cn 网站项目

> 按仓库 `agent_matrix/USER.default.md` 的真实 schema 编写。  
> 本文件定义“Agent 在为谁工作”，不定义安全政策；政策见 L1 / `AGENTS.default.md` / 项目文档。

```yaml
context: site

identity:
  name: "tarka.cn"
  domain: "tarka.cn"
  locale: zh-CN
  timezone: Asia/Shanghai

grants:
  plugins: []          # 空 = 按 official 发行版白名单；如需最小权限，填实际插件 ID，不要臆造
  data_scope: own      # 只允许本站/本租户
  can_publish: false   # 网站发布是人工动作，Agent 不得直接发布
  can_manage_users: false
```

## 说明

- `context: site`：Agent 的一切站点动作默认作用于 `tarka.cn` 对应实例。
- `data_scope: own`：不得跨站点/租户读取。
- `can_publish: false`：Builder 只能生成草稿与检查清单；发布由有权限的人执行。
- `plugins` 为空表示不额外限制；若要收紧，必须填写真实插件标识并经过部署验证。
- 身份缺失/冲突时，Agent 不得推断，应拒绝有副作用的动作并报告。
