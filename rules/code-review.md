# 代码审查

## 目标与范围

代码审查只判断：当前改动是否存在足以阻止合并或交付的、具体且可证明的问题。
目标是确认正确性并收敛，不是尽可能发现优化点或理论风险。

审查 MUST 限于：

- 当前任务及验收条件；
- 当前改动及 diff；
- 改动直接影响的调用路径、状态、接口、协议、数据和行为；
- 作出判断所必需的验证结果。

审查深度 SHOULD 与风险相称。无直接因果关系的历史问题可以记录，但 MUST NOT 自动
修复、扩大范围或触发审查循环。任务要求的重构也不得扩展到无关代码。

## Finding 分级

### Blocking

Blocking 是不修复就足以阻止当前改动合并或交付的问题，例如构建失败、明确错误或
回归、违反需求或既有契约、可达的运行时错误、严重安全漏洞，以及可证明的资源、并发、
状态机或数据一致性错误。

每个 Blocking Finding MUST 用证据说明：

- **Location**：问题位置；
- **Failure Condition**：触发条件；
- **Impact**：具体失败结果；
- **Reachability**：路径为何实际可达；
- **Violated Constraint**：被违反的需求、契约、测试、协议或正确性约束。

任一证据不足时，MUST 降级为 Non-blocking 或 Remaining Uncertainty。最佳实践以及
“可能”“理论上”“最好”“建议”“更加健壮”“未来可能”等表述不能单独作为证据。

### Non-blocking 与不确定性

不阻止当前任务正确完成的问题属于 Non-blocking，包括风格、命名、可读性优化、可选
重构、微小性能改进、未来扩展、无法证明可达的风险，以及需求之外的 fallback、retry、
防御分支、恢复路径、兼容层或抽象。

Non-blocking Finding 默认只记录，MUST NOT 触发修改或继续审查。无法确认的问题 SHOULD
先通过现有代码、路径、契约和验证确认；仍无法证明 Blocking 时，不得假设最坏情况并
增加代码。缺少验证只有在违反验收条件或使必要正确性结论无法成立时，才阻止交付。

只有用户明确要求时才处理 Non-blocking Finding；不得为了让审查看起来完整而制造
Finding。

## Review 与 Fix

Review MUST 先形成 Finding，再决定是否修复。只有 Blocking Finding 默认允许触发修复，
且修复 MUST 是恢复被违反约束所需的最小改动。

MUST NOT 借审查修改无关代码、清理技术债、扩大 API 或数据模型、增加未来功能或无证据
的保护机制，也不得将局部修复演变为系统重构。

## 收敛流程

一次实现最多执行一次 Full Review：

```text
Implementation → Full Review → Fix Blocking
→ Targeted Re-review → PASS 或 STOP
```

修复后 MUST 执行 Targeted Re-review，MUST NOT 重新启动 Full Review。复审只能检查：

1. 原 Blocking 是否解决；
2. 修复是否直接引入新的 Blocking；
3. 原始需求是否仍然满足；
4. 必要验证是否仍然通过。

MUST NOT 重新扫描代码库、寻找优化、挑战已接受设计或处理无关历史问题。新 Finding
只有在由上一轮修复直接引入，或直接阻止原任务完成时，才可作为 Blocking 继续处理；
否则记录后停止扩展。

默认最多允许两轮“修复 Blocking → Targeted Re-review”。超过上限仍有 Blocking，或
同一问题连续修复仍未解决时，MUST 输出 `Verdict: FAIL` 并停止修改，说明已确认的原因，
如需求歧义、设计错误、上下文不足或验证环境不足。MUST NOT 开始第三轮修复或新的
Full Review；继续前必须重新规划或取得必要信息。

## 停止与输出

需求已满足、必要验证已通过且没有已知 Blocking Finding 时，Verdict MUST 为 `PASS`。
剩余 Non-blocking Finding 或已记录的不确定性不阻止 PASS。输出 `REVIEW PASS` 后 MUST
立即结束，不得继续寻找优化、更优设计、额外健壮性、重构机会或理论风险。

存在未解决的 Blocking Finding 或触发循环上限时，Verdict MUST 为 `FAIL`，不得输出
`REVIEW PASS`。没有内容的分组写 `None`：

```text
Verdict: PASS | FAIL
Blocking Findings:
- None | <finding>
Non-blocking Findings:
- None | <finding>
Verification:
- <performed check and result>
Remaining Uncertainty:
- None | <unverified fact and its effect>
```

Blocking Finding 使用以下字段：

```text
Severity: Blocking
Location:
Failure Condition:
Impact:
Reachability:
Violated Constraint:
Minimal Fix:
```

`Minimal Fix` 是修复建议，不是 Blocking 成立的证据。Verdict 为 `PASS` 时，输出末尾
MUST 且只能追加：

```text
REVIEW PASS
```
