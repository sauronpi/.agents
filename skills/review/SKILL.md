---
name: review
description: '对代码变更、diff、指定代码文件或文档进行正式代码审查或文档审查，判定 Blocking / Non-blocking 问题、执行最小修复复审并给出 PASS/FAIL 结论时使用。可与语言或文档格式 Skill 配合使用；不用于对抗式审查（adversarial-review）。'
---

# 审查

本 Skill 适用于代码审查和文档审查。两类审查共用目标与范围、Finding 分级、修复与复审、
停止与输出；各自的判断重点和 Blocking 示例见文末补充。一次任务同时涉及代码和文档时，
按审查对象分别适用对应补充。

## 目标与范围

审查主要判断：审查对象是否存在足以阻止其被合并、交付或可靠使用的、具体且可证明的
问题。目标是确认审查对象能完成既定用途或被合并、交付，并使审查收敛；发现的非阻塞
问题可以记录，但不得为寻找优化点或理论风险扩大范围。

审查 MUST 限于：

- 当前任务及验收条件；
- 审查对象：变更审查为当前改动及 diff；没有 diff 的现状审查为用户指定的文件、
  组件、代码快照或文档；
- 与审查对象直接相关的依赖：代码为调用路径、状态、接口、协议、数据和行为；文档为
  引用、上下文和依赖内容；
- 作出判断所必需的验证结果。

审查深度 SHOULD 与风险相称；风险包括问题的影响和不确定性，文档审查还 SHOULD 考虑
文档用途。

## Finding 分级

### Blocking

Blocking 是不修复就足以阻止审查对象被合并、交付或可靠使用的问题。

每个 Blocking Finding MUST 包含：

- **Location**：问题位置；
- **Issue**：当前内容及具体错误、矛盾或缺失；
- **Impact**：具体失败结果，或对理解、执行、实现、判断、知识使用的具体影响；
- **Evidence**：支持判断的需求、上下文、项目事实、契约、可靠来源或验证；
- **Minimal Fix**：恢复被违反约束或正确性所需的最小修复建议。

Blocking 是否成立取决于问题及其影响的证据，不取决于修复建议是否完备。问题、触发
条件或影响的依据不足时 MUST NOT 判为 Blocking，按 Non-blocking 或 Remaining
Uncertainty 处理。`Minimal Fix` 仍须随 Blocking 报告，但它不参与成立与否的判断，
也不是其证据。最佳实践或推测性表述不能单独作为 Blocking 的证据。

### Non-blocking

已确认存在但不阻止审查对象完成既定用途的问题属于 Non-blocking。

### Remaining Uncertainty

事实、可达性或影响尚未确认时属于 Remaining Uncertainty。SHOULD 先用现有上下文、
代码路径、来源、契约、项目事实和验证确认，不得假设最坏情况，也不得假设其必然错误；
仍然无法确认时 MUST 记录其成立条件和影响。

不得为了让审查显得完整而制造 Finding。

## 修复与复审

修改 Finding 前 MUST 确认其成立。用户只要求审查时 MUST NOT 修改审查对象；任务包含
修复时，修复当前任务范围内已确认的 Finding。需要新授权或显著扩大任务范围的 Finding
不自动修改；若该 Finding 阻止当前任务完成，则请求用户决定，否则只报告。

修复 MUST 采用最小充分变更。不得仅为 Non-blocking Finding 扩大审查范围或增加审查轮次。

修复后 MUST 仅复审所有已修复 Finding、修改直接影响的路径或内容、原始需求或任务目标，
以及必要验证，并确认没有引入新的 Blocking。

仍有 Blocking 时，若能在原范围内取得可验证进展，MUST 继续最小修复并定向复审；否则
MUST 停止修改，说明原因及继续所需的信息或重新规划条件。

## 停止与输出

存在未解决的 Blocking Finding，或缺少验证违反验收条件，或缺少验证使必要结论无法
成立时，Verdict MUST 为 `FAIL`；否则 MUST 为 `PASS`。验证受阻的原因及影响 MUST 记入
Remaining Uncertainty，不得据此推定审查对象存在缺陷。

输出 MUST 包含 Verdict；仅在有对应内容时输出 Blocking Findings、Non-blocking
Findings、Verification（实际检查及结果）和 Remaining Uncertainty（未验证事项、
成立条件及影响）。完成输出后 MUST 结束审查。

## 代码审查补充

代码审查对象为当前改动及 diff；没有 diff 的现状审查为用户指定的文件、组件或代码快照。

Blocking 示例：构建失败、明确错误或回归、违反需求或既有契约、可达的运行时错误、
严重安全漏洞，以及可证明的资源、并发、状态机或数据一致性错误。

代码审查的 Blocking Evidence MUST 同时包含：

- **Trigger Path**：触发条件及路径为何实际可达；
- **Violated Constraint**：被违反的需求、契约、测试、协议或正确性约束。

任一不足时 MUST NOT 判为 Blocking，按 Non-blocking 或 Remaining Uncertainty 处理。

Non-blocking 示例：风格与可读性、可选重构、微小性能改进，以及可选的防御性或扩展性
设计。

## 文档审查补充

文档审查 MUST 依据当前文档的目标、目标读者、适用范围和必要约束判断正确性与完整性，
不得预设所有文档需要相同结构。修复 MUST 保持用户未要求改变的文档目标、受众和范围。

Blocking 示例：关键事实错误、核心要求遗漏、重要内容矛盾、关键步骤不可执行。

文档中的关键事实无法核验时 MUST NOT 判为 Blocking，应按 Remaining Uncertainty 记录；
必要结论因此无法成立时，Verdict 仍为 `FAIL`。

Non-blocking 示例：风格排版、可选补充、轻微重复。

审查重点：

- **事实与依据**：MUST 区分事实、推断和假设；仅核验实质影响文档用途的外部事实；
  来源 MUST 支持对应表述，并符合其适用时间和条件。
- **冗余**：检查重复是否造成矛盾、多个有效版本、显著维护风险，或掩盖核心信息；
  不得以最短为目标牺牲准确性和可理解性。
