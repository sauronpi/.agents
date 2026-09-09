# 代码审查

## 目标与范围

代码审查主要判断：当前改动是否存在足以阻止合并或交付的、具体且可证明的问题。
目标是确认正确性并收敛；审查中发现的非阻塞问题可以记录，但不得为寻找优化点或
理论风险扩大范围。

审查 MUST 限于：

- 当前任务及验收条件；
- 当前改动及 diff；
- 改动直接影响的调用路径、状态、接口、协议、数据和行为；
- 作出判断所必需的验证结果。

审查深度 SHOULD 与风险相称。

## Finding 分级

### Blocking

Blocking 是不修复就足以阻止当前改动合并或交付的问题，例如构建失败、明确错误或
回归、违反需求或既有契约、可达的运行时错误、严重安全漏洞，以及可证明的资源、并发、
状态机或数据一致性错误。

每个 Blocking Finding MUST 包含：

- **Location**：问题位置；
- **Trigger Path**：触发条件及路径为何实际可达；
- **Impact**：具体失败结果；
- **Violated Constraint**：被违反的需求、契约、测试、协议或正确性约束。
- **Minimal Fix**：恢复被违反约束所需的最小修复建议。

前四项任一证据不足时，MUST NOT 判为 Blocking，并按下节定义区分 Non-blocking 与
Remaining Uncertainty。`Minimal Fix` 不是 Blocking 成立的证据。最佳实践或推测性表述
不能单独作为 Blocking 的证据。

### Non-blocking 与不确定性

已确认存在但不阻止当前任务正确完成的问题属于 Non-blocking，例如风格与可读性问题、
可选重构、微小性能改进，以及可选的防御性或扩展性设计。
尚未确认的事实、可达性或影响属于 Remaining Uncertainty。

无法确认的问题 SHOULD 先通过现有代码、路径、契约和验证确认，不得假设最坏情况。

不得为了让审查看起来完整而制造 Finding。

## 修复与复审

Review MUST 先完成 Finding 判断。用户只要求审查时 MUST NOT 修改代码；任务包含修复时，
只有 Blocking Finding 默认允许触发恢复被违反约束所需的最小修改。未经用户明确要求，
Non-blocking Finding 仅可记录，MUST NOT 据此修改或继续审查。
修复 MUST 保持原任务范围。

修复 Blocking 后 MUST 仅复审原 Finding、修复直接引入的 Blocking、原始需求和必要验证。
仍有 Blocking 时，若能在原范围内取得可验证进展，MUST 继续最小修复并定向复审；否则
MUST 停止修改。

## 停止与输出

存在未解决的 Blocking Finding，或缺少验证违反验收条件或使必要正确性结论无法成立时，
Verdict MUST 为 `FAIL`；否则 MUST 为 `PASS`。验证受阻的原因及影响 MUST 记入
Remaining Uncertainty，不得据此推定代码存在缺陷。
输出 MUST 包含 Verdict；仅在有对应内容时输出 Blocking Findings、Non-blocking
Findings、Verification 和 Remaining Uncertainty。
完成输出后 MUST 结束审查。
