# 文档审查

## 目标与范围

文档审查旨在确认当前文档能够完成既定用途并使审查收敛，不是尽可能寻找改进机会。

审查 MUST 依据当前文档的目标、目标读者、适用范围和必要约束判断正确性与完整性，
不得预设所有文档需要相同结构或深度。项目级规则 MAY 补充项目特有约束。

审查 MUST 限于：

- 当前任务、验收条件、文档和修改；
- 与当前审查对象直接相关的引用、上下文和依赖内容；
- 作出关键判断所必需的验证结果。

审查深度 SHOULD 与问题的影响、不确定性和文档用途相称。

## Finding 分级

### Blocking

Blocking 是不修复就足以阻止文档完成既定用途或被可靠使用的问题，例如关键事实错误、
核心要求遗漏、重要内容矛盾或关键步骤不可执行。

每个 Blocking Finding MUST 说明：

- **Location**：问题位置；
- **Issue**：当前内容及具体错误、矛盾或缺失；
- **Impact**：对理解、执行、实现、判断或知识使用的具体影响；
- **Evidence**：支持判断的需求、上下文、项目事实、契约、可靠来源或验证；
- **Minimal Fix**：恢复正确性所需的最小修复。

`Minimal Fix` 不是 Blocking 成立的证据；无法说明具体影响或提供充分依据时，Finding
MUST 降级为 Non-blocking 或 Remaining Uncertainty。

### Non-blocking 与不确定性

不阻止文档完成既定用途的问题属于 Non-blocking，例如风格排版、可选补充和轻微重复。
不得为了让审查显得完整而制造 Finding。

无法确认某项内容是否错误时，Reviewer MUST 标记不确定性并按需用现有上下文、来源或
项目事实验证，不得假设其必然错误。只有不确定性本身足以使文档无法被可靠使用时才可
成为 Blocking；否则记录其成立条件和影响。

## 审查重点

- **事实与依据**：MUST 区分事实、推断和假设。仅核验实质影响文档用途的外部事实；
  来源 MUST 支持对应表述，并符合其适用时间和条件。
- **完整性**：检查目标读者正确理解和使用文档所必需的信息是否齐全。
- **结构**：仅检查其是否阻碍核心内容的正确理解或使用。
- **冗余**：检查重复是否造成矛盾、多个有效版本、显著维护风险，或掩盖核心信息。
  不得以最短为目标牺牲准确性和可理解性。

## 修改与收敛

Review MUST 先完成问题判断。只有当前任务已授权修改时，确认的 Blocking Finding 才默认
允许触发修改；修复 MUST 保持文档的目标、受众和范围，采用最小充分变更；仅当现有结构
直接造成 Blocking 且无法局部修复时，才允许大范围重构。除非用户明确要求，否则
Non-blocking Finding 只记录，MUST NOT 触发修改或继续审查。

修复 Blocking 后 MUST 仅验证原 Blocking 是否解决，以及修复直接影响的内容是否引入
新 Blocking 或破坏原始目标。

复审后仍有 Blocking 时，若能在原范围内取得可验证进展，MUST 继续最小修复并定向复审；
否则 MUST 停止修改，说明原因及继续所需的信息或重新规划条件。

## 输出与停止

完成必要验证后，存在未解决的 Blocking Finding 时，Verdict MUST 为 `FAIL`；否则 MUST 为
`PASS`。

输出 MUST 包含 Verdict；仅在有对应内容时输出以下分组。完成输出后 MUST 结束审查。

```text
Verdict: PASS | FAIL
[Blocking Findings:]
- <finding>
[Non-blocking Findings:]
- <finding>
[Verification:]
- <performed check and result>
[Remaining Uncertainty:]
- <unverified matter, condition, and effect>
```
