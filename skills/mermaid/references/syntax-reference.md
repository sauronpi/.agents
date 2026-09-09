# Mermaid 配置与排错参考

基础语法和版本差异以 Mermaid 官方文档及目标渲染器为准。

## 宿主边界

- 全局安全策略、布局器和默认值由宿主通过 `mermaid.initialize(...)` 配置；单图设置使用 frontmatter `config:`，但目标平台可能限制或移除它。
- `securityLevel` 应由宿主通过 `mermaid.initialize(...)` 控制，不依赖不可信图代码自行降低。
- 使用链接、HTML 或回调前确认宿主安全策略并审查目标 URL；不要为不可信图源码降低安全级别。
- Directive 已弃用，仅在维护旧图或目标渲染器不支持 frontmatter 时保留。

## 常见失败与边界定位

| 症状或错误位置                            | 常见原因                                                    | 处理顺序                                     |
| ----------------------------------------- | ----------------------------------------------------------- | -------------------------------------------- |
| 首行报 unknown diagram 或 header invalid  | 缺少图类型声明、关键字拼错，或渲染器版本不支持该图型        | 核对首行和目标版本；先换成该图型最小模板     |
| Flowchart 在 `end`、`o`、`x` 附近解析失败 | 小写 `end` 被当作结束关键字；连线后的 `o`、`x` 被当作特殊边 | 改为 `End`；在节点前加空格或改用大写 ID      |
| 箭头或消息附近报 lexical/parse error      | 混用了 Flowchart 与 Sequence 箭头，或标签分隔符不闭合       | 回到对应图型的箭头表，删除样式后逐条恢复     |
| 子图方向与 `direction` 不一致             | 外部节点直接连接了子图内部节点                              | 改为连接子图 ID，或接受父图方向              |
| Gantt 日期或工期偏移                      | `dateFormat` 与输入不匹配，或 `excludes` 延长了任务         | 分别核对输入格式、排除日和依赖任务的计算结果 |
| `-beta` 图型或新语法无法识别              | 目标 Mermaid 版本低于语法下限                               | 查官方版本说明；改用稳定图型或低版本语法     |
| 图可渲染但拥挤、交叉或文字溢出            | 单图节点、长文本、子图、样式或交互叠加过多                  | 缩短标签，拆分主题图，再逐项恢复增强配置     |

## 参考来源

- [Mermaid Syntax Reference](https://mermaid.js.org/intro/syntax-reference.html)
- [Directives](https://mermaid.js.org/config/directives.html)
- [Config Schema](https://mermaid.js.org/config/schema-docs/config.html)
- [Flowchart](https://mermaid.js.org/syntax/flowchart.html)
- [Gantt](https://mermaid.js.org/syntax/gantt.html)
