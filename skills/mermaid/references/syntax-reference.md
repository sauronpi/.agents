# Mermaid 配置与排错参考

基础语法和版本差异以 Mermaid 官方文档及目标渲染器为准。

## 宿主边界

- 全局安全策略（含 `securityLevel`）、布局器和默认值由宿主通过 `mermaid.initialize(...)` 配置；单图设置使用 frontmatter `config:`，但不能用于绕过宿主安全策略，且目标平台可能限制或移除它。
- 使用链接、HTML 或回调前确认宿主安全策略并审查目标 URL；不要为不可信图源码降低安全级别。
- Directive 已弃用，仅在维护旧图或目标渲染器不支持 frontmatter 时保留。

## 常见失败与边界定位

| 症状或错误位置                                               | 常见原因                                                                        | 处理顺序                                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| 图类型或新语法无法识别（如 unknown diagram、header invalid） | 缺少图类型声明、关键字拼错，或目标版本不支持该图型或语法                        | 核对声明和官方版本说明                                                      |
| Flowchart 解析失败、出现非预期边型                           | 小写 `end` 被当作结束关键字；紧接连线的节点 ID 首字母 `o`、`x` 被当作特殊边标记 | 将误用的 `end` 改为 `End`；在节点 ID 前加空格或将首字母大写                 |
| 箭头或消息附近报 lexical/parse error                         | 混用了 Flowchart 与 Sequence 箭头，或标签分隔符不闭合                           | 按对应图型的语法核对箭头和标签分隔符                                        |
| 子图方向与 `direction` 不一致                                | 外部节点直接连接了子图内部节点                                                  | 优先保留连接语义并接受父图方向；仅当关系确实指向整个子图时，改为连接子图 ID |
| Gantt 日期或工期偏移                                         | `dateFormat` 与输入不匹配，或 `excludes` 延长了任务                             | 分别核对输入格式、排除日和依赖任务的计算结果                                |
| 图可渲染但拥挤、交叉或文字溢出                               | 单图节点、长文本、子图、样式或交互叠加过多                                      | 缩短标签，必要时拆分主题图                                                  |

## 参考来源

- [Mermaid Syntax Reference](https://mermaid.js.org/intro/syntax-reference.html)
- [Directives](https://mermaid.js.org/config/directives.html)
- [Config Schema](https://mermaid.js.org/config/schema-docs/config.html)
- [Flowchart](https://mermaid.js.org/syntax/flowchart.html)
- [Gantt](https://mermaid.js.org/syntax/gantt.html)
