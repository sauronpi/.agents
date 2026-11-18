# 子 Agent 委派与选模前置规则

## 目的

本规则用于在常驻上下文中锚定子 Agent 委派与模型选择约束，防止工具默认说明抢先触发并绕过按需加载的 Skill。

## 规则

1. **新建前 MUST 先加载并执行 Skill**
   准备新建子 Agent 或子会话时，MUST 先读取并执行 [`subagent-selection`](../skills/subagent-selection/SKILL.md) Skill，再决定是否启动。
   - **适用范围示例**：在 Zed 中即调用 `spawn_agent`（未传 `session_id`）或 `create_thread` 等新建子会话的工具；其他 Agent 中对应等价的子 Agent/子会话创建工具。
   - **例外**：续用已有子会话（如复用 `session_id` 继续对话）时，无需重新执行选模流程。

2. **MUST NOT 沿用工具默认模型直启**
   即使工具参数说明允许省略模型以使用默认模型，新建子会话时也 MUST NOT 未经选模直接启动，也 MUST NOT 在用户选定模型不可用时静默回退到其他模型。

3. **流程以 Skill 为准**
   可显式选模检查、按提供方分组列出并推荐模型、等待用户确认、显式传入完整模型 ID、上下文传递与结果交接的具体要求，均以 [`subagent-selection`](../skills/subagent-selection/SKILL.md) Skill 为准。
