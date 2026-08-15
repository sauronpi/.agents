# 文档审查

## 目标与范围

文档审查只判断：当前文档是否存在足以阻止发布、合并、交付或作为可靠知识使用的、
具体且有依据的问题。目标是确认文档能够完成既定用途并使审查收敛，不是尽可能寻找
措辞、结构、风格或扩展内容上的改进机会。

审查 MUST 先确定当前文档的目标、目标读者、适用范围和必要约束，再判断正确性与完整性。
项目级规则 MAY 补充领域事实、权威来源、契约、格式和验收条件；通用规则不得假设所有
文档需要相同结构或深度。

审查 MUST 限于：

- 当前任务、验收条件、文档和修改；
- 与当前修改直接相关的引用、上下文和依赖内容；
- 作出关键判断所必需的验证结果。

无直接关系的问题 MAY 记录，但 MUST NOT 自动修改、扩大范围或触发新的审查循环。审查
深度 SHOULD 与问题的影响、不确定性和文档用途相称。

## Finding 分级

### Blocking

Blocking 是不修复就足以阻止文档完成既定用途或被可靠使用的问题，例如明确事实错误、
关键结论与证据不符、核心要求遗漏、重要内容矛盾、关键步骤不可执行，以及必要前提、
适用范围、引用、契约或结构存在足以导致错误理解或使用的问题。

每个 Blocking Finding MUST 说明：

- **Location**：问题位置；
- **Issue**：当前内容及具体错误、矛盾或缺失；
- **Impact**：对理解、执行、实现、判断或知识使用的具体影响；
- **Evidence**：支持判断的需求、上下文、项目事实、契约、可靠来源或验证；
- **Minimal Fix**：恢复正确性所需的最小修复。

`Minimal Fix` 不是 Blocking 成立的证据。无法说明具体影响或提供足够依据时，Finding
MUST 降级为 Non-blocking 或 Remaining Uncertainty。“感觉不清楚”“最好补充”
“一般应该”“可能困惑”等表述不能单独证明 Blocking。

### Non-blocking 与不确定性

不阻止文档完成既定用途的问题属于 Non-blocking，包括风格或排版偏好、可选示例或图表、
更漂亮的结构、非必要背景、轻微重复和任务外的知识扩展。Non-blocking Finding 默认只
记录，MUST NOT 触发修改或继续审查；不得为了让审查显得完整而制造 Finding。

无法确认某项内容是否错误时，Reviewer MUST 标记不确定性并按需用现有上下文、来源或
项目事实验证，不得假设其必然错误。只有不确定性本身足以使文档无法被可靠使用时才可
成为 Blocking；否则记录其成立条件和影响。

## 审查重点

- **事实与推断**：MUST 区分已确认事实、来源支持的结论、推断、假设、建议、示例和未
  验证信息。只有错误地将非事实表述为事实、隐藏会影响使用或决策的不确定性，或用不足
  证据支撑关键结论时，才可能构成 Blocking。
- **来源**：只验证会实质影响文档用途的外部事实，优先检查关键事实、数字、版本、结论
  及其适用时间和条件。来源必须真正支持表述；不得把断章取义、相关性冒充因果关系或
  明显过时的信息作为关键依据。能够增加更多来源本身不构成 Blocking。
- **完整性**：只有缺失信息会使目标读者无法正确理解、执行、实现、判断或使用文档时，
  才可成为 Blocking。“还能补充”不等于不完整。
- **结构**：结构只服务于文档目标。不得机械要求特定章节、目录、摘要、示例、图表或
  附录；只有现有结构阻碍核心内容的正确理解或使用时，才可成为 Blocking。
- **冗余**：只有重复造成矛盾、多个有效版本、显著维护风险，或掩盖核心信息时，才可能
  构成 Blocking。帮助理解的局部重复默认属于 Non-blocking；不得以最短为目标牺牲准确性
  和可理解性。

## 修改与收敛

Review MUST 先形成 Findings。只有当前任务已授权修改时，确认的 Blocking Finding 才默认
允许触发修改；修复 MUST 是解决问题所需的最小变更，并保持文档的目标、受众、范围、
深度、信息密度和表达风格。只有用户明确要求时才修改 Non-blocking Finding。

MUST NOT 借审查重写全文、扩展成教程、统一其他文档、修改无关章节、增加大量背景或执行
大范围风格润色，除非现有结构本身直接造成 Blocking 且无法局部修复。

一次文档任务最多执行一次 Full Review。修复 Blocking 后 MUST 执行一次 Targeted Re-review，
且只能检查：

1. 原 Blocking 是否解决；
2. 修复是否直接引入新的 Blocking；
3. 原始文档目标是否仍满足；
4. 关键事实、引用、逻辑和结构是否仍一致。

MUST NOT 重新启动 Full Review 或寻找新的措辞、结构、背景、来源和示例优化。Targeted
Re-review 中的新问题只有由修复直接引入或直接阻止原始目标时才可成为 Blocking；否则
记录后停止扩展。复审后仍有 Blocking 时，MUST 输出 `Verdict: FAIL` 并停止修改，说明原因
及继续所需的信息或重新规划条件；不得用反复改写掩盖信息不足或要求矛盾。

## 输出与停止

文档目标已满足、必要事实或关键引用已验证、核心内容没有明显矛盾且不存在已知 Blocking
Finding 时，Verdict MUST 为 `PASS`。剩余 Non-blocking Finding 或已记录的不确定性不阻止
PASS。存在未解决的 Blocking Finding 时，Verdict MUST 为 `FAIL`。

使用以下输出；没有内容的分组写 `None`，不得为填充模板制造 Finding：

```text
Verdict: PASS | FAIL
Blocking Findings:
- None | <finding>
Non-blocking Findings:
- None | <finding>
Verification:
- <performed check and result>
Remaining Uncertainty:
- None | <unverified matter, condition, and effect>
```

Verdict 为 `PASS` 时，输出末尾 MUST 且只能追加：

```text
DOCUMENT REVIEW PASS
```

输出 `DOCUMENT REVIEW PASS` 后 MUST 立即停止，不得继续寻找可补充、删减、润色、重组或
进一步验证的内容。
