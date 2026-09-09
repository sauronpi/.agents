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

审查深度 SHOULD 与风险相称。任务要求的重构也不得扩展到无关代码。

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

前四项任一证据不足时，MUST 降级为 Non-blocking 或 Remaining Uncertainty；`Minimal Fix`
仅是修复建议。最佳实践以及“可能”“理论上”“最好”“建议”“更加健壮”“未来可能”等
表述不能单独作为证据。

### Non-blocking 与不确定性

已确认存在但不阻止当前任务正确完成的问题属于 Non-blocking，包括风格、命名、可读性
优化、可选重构、微小性能改进、未来扩展，以及需求之外且不影响当前正确性的防御性或
扩展性设计。尚未确认的事实、可达性或影响属于 Remaining Uncertainty。

无法确认的问题 SHOULD 先通过现有代码、路径、契约和验证确认；仍无法证明 Blocking 时，
不得假设最坏情况。缺少验证只有在违反验收条件或使必要正确性结论无法成立时，才阻止
交付。

不得为了让审查看起来完整而制造 Finding。

## 修复与复审

Review MUST 先完成 Finding 判断。用户只要求审查时 MUST NOT 修改代码；任务包含修复时，
只有 Blocking Finding 默认允许触发恢复被违反约束所需的最小修改。只有用户明确要求时
才处理 Non-blocking Finding；否则 Non-blocking Finding 只记录，不得触发修改或继续审查。
不得借审查扩大任务、API 或数据模型。

修复 Blocking 后 MUST 仅复审原 Finding、修复直接引入的 Blocking、原始需求和必要验证。
仍有 Blocking 时，若能在原范围内取得可验证进展，MUST 继续最小修复并定向复审；否则
MUST 停止修改并输出 `Verdict: FAIL`。

## 停止与输出

完成必要验证后，存在未解决的 Blocking Finding 时 Verdict MUST 为 `FAIL`，否则 MUST 为
`PASS`。Non-blocking Finding 和不足以否定必要正确性结论的 Remaining Uncertainty 不阻止
PASS。输出 MUST 包含 Verdict；仅在有对应内容时输出 Findings、Verification 和 Remaining
Uncertainty，且 Finding 仍须包含本规则要求的证据。完成输出后 MUST 结束审查。

```text
Verdict: PASS | FAIL
[Blocking Findings:]
- <finding>
[Non-blocking Findings:]
- <finding>
[Verification:]
- <performed check and result>
[Remaining Uncertainty:]
- <unverified fact and its effect>
```
