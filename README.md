# Agent Configuration (`.agents`)

个人可复用的 AI Agent 配置仓库，用于统一维护跨项目规则、Skills 和文档验证配置。

目标：

- 在多个项目之间复用稳定的工作原则和工具能力
- 支持 Codex、GitHub Copilot、Kilo Code 等不同 Agent
- 在 Windows、macOS 和 Linux 之间同步

项目背景、技术栈、构建方式等项目专属知识不属于本仓库，应保存在对应项目的 `AGENTS.md` 或项目文档中。

## 目录结构

```text
.agents/
├── .markdownlint-cli2.jsonc
├── .markdownlint.jsonc
├── AGENTS.md
├── README.md
├── rules/
│   ├── adversarial-review.md
│   └── first-principles.md
└── skills/
    ├── markdown/
    │   └── SKILL.md
    ├── mermaid/
    │   ├── SKILL.md
    │   └── references/
    │       └── syntax-reference.md
    ├── skill-authoring/
    │   └── SKILL.md
    └── wavedrom/
        ├── SKILL.md
        └── references/
            └── syntax-reference.md
```

## Rules 与 Skills

- 仓库根目录的 `AGENTS.md` 定义维护本仓库时应遵循的规则，也是设计共享规则和 Skills 的依据。
- `rules/` 保存可跨项目复用的用户级 Rule 源文件。这些文件不会被 Agent 自动发现；用户应根据实际需要，将选定内容接入不同 Agent 或项目的规则入口。
- `skills/` 提供可复用的任务能力。每个 Skill 使用独立目录，并以 `SKILL.md` 作为入口。

根目录的 `AGENTS.md` 包含本仓库专属约束，不应直接作为其他项目的全局规则。新增内容前应先判断作用域：跨多个项目长期有效的能力才适合放入本仓库；单个项目的约束应留在该项目中。完整的设计原则和修改要求见 [`AGENTS.md`](AGENTS.md)。

## Agent 接入

下面按实际接入顺序组织平台和 Agent 的差异。先在当前平台同步一次共享仓库，再完成所用 Agent 的规则配置；不需要分别执行两套教程。

### 项目级补充提示词

完成用户级规则配置和 Skills 发现后，在目标项目的根目录启动 Agent，并发送下面的提示词，让 Agent 以用户级配置为默认基线，根据项目现状补充项目级 `AGENTS.md`：

```text
请以当前 Agent 已生效的用户级 Rules 和从 `$HOME/.agents/skills/` 发现的 Skills 为
默认基线，按当前项目的实际需要补充项目根目录的 `AGENTS.md`：

1. 先读取当前项目已有的 `AGENTS.md`、其他 Agent 指令和与开发、验证相关的文档，
   保留现有有效内容，避免重复或冲突。
2. 读取 `$HOME/.agents/rules/` 下的 Rule，以及 `$HOME/.agents/skills/` 下各 Skill 的
   `SKILL.md`；`$HOME` 表示 Windows、macOS 或 Linux 当前用户的主目录。
3. 将适用的用户级 Rule 和 Skill 视为默认能力，不要在项目级 `AGENTS.md` 中重复其
   通用内容，也不要导入 `$HOME/.agents/AGENTS.md`，因为它只约束 `.agents` 仓库本身。
4. 项目级 `AGENTS.md` 只补充当前项目特有的背景、约束、构建与验证命令，以及确有
   必要的 Skill 触发条件或项目级例外。新增内容应说明它相对用户级基线补充了什么。
5. 如果所需 Rule 尚未在当前 Agent 的用户级规则入口生效，只报告需要补充的用户级
   配置，不要因此把通用 Rule 复制到项目级 `AGENTS.md`。
6. 如果某项 Skill 必须供团队成员或云端 Agent 使用，而不能依赖个人用户目录，则将它
   作为项目级 Skill 接入 `.agents/skills/`，并检查其中的相对资源引用。
7. 采用满足需求的最小修改，完成后检查 `AGENTS.md` 的结构、相对链接和 Markdown
   格式，并分别报告沿用的用户级基线、增加的项目级补充以及执行的验证。
```

用户级配置负责跨项目默认行为，项目级配置只负责当前项目的增量约束，不应重复维护同一份通用内容。仅供个人本地使用的 Skill 继续由 Agent 从用户级目录自动发现；项目必须供团队或云端 Agent 自包含使用时，再将所需 Skill 提交到项目仓库。

规则和 Skills 使用不同的接入机制：

| Agent | Windows 用户级规则 | macOS/Linux 用户级规则 | 项目级规则 | 共享用户级 Skills |
| --- | --- | --- | --- | --- |
| Codex | `$HOME/.codex/AGENTS.md` | `~/.codex/AGENTS.md` | `AGENTS.md` | `$HOME/.agents/skills/` |
| GitHub Copilot | 随使用界面而异 | 随使用界面而异 | `.github/copilot-instructions.md` | `$HOME/.agents/skills/`（受支持的界面） |
| Kilo Code | `$HOME/.config/kilo/AGENTS.md` | `~/.config/kilo/AGENTS.md` | `AGENTS.md` | `$HOME/.agents/skills/` |

表中的 `~` 和 `$HOME` 都表示当前用户的主目录；共享目录统一写作 `$HOME/.agents`，以便在 Windows、macOS 和 Linux 之间复用。共同支持的是对 `$HOME/.agents/skills/` 的 Skills 发现，而不是对整个 `$HOME/.agents` 目录的自动引用。`$HOME/.agents/AGENTS.md`、`README.md` 和其他配置文件不会因此成为用户级全局指令。Agent 通常先扫描 Skill 的名称和描述，只有任务匹配时才按需加载完整的 `SKILL.md` 及其引用资源。

当 Agent 正在本仓库中工作时，根目录的 `AGENTS.md` 可能作为当前项目规则被加载；这是项目级规则发现，而不是 `$HOME/.agents` 具有全局规则目录的特殊含义。

### 1. 按平台同步共享仓库

首次安装时，将仓库克隆到用户主目录的 `.agents`。如果目录已经存在，跳过克隆并使用原有同步方式更新仓库。不要把 `<repository-url>` 原样执行，应替换为本仓库的远程地址。

#### Windows

在 PowerShell 中执行：

```powershell
git clone <repository-url> "$HOME/.agents"
Test-Path "$HOME/.agents/README.md"
Get-ChildItem "$HOME/.agents/skills" -Filter SKILL.md -Recurse
```

#### macOS 和 Linux

在 Terminal 的 zsh、bash 或其他兼容 POSIX shell 中执行：

```shell
git clone <repository-url> "$HOME/.agents"
test -f "$HOME/.agents/README.md"
find "$HOME/.agents/skills" -type f -name SKILL.md
```

macOS 和 Linux 使用相同的目录约定，但仍应分别检查各机器上的仓库同步、文件权限和 Agent 版本。

### 2. 按 Agent 配置规则和 Skills

只配置实际使用的 Agent。三个 Agent 共享刚才同步的 `$HOME/.agents/skills/`，但规则文件仍放在各自的入口。

#### OpenAI Codex

1. 创建用户级配置目录：

   - Windows PowerShell：`New-Item -ItemType Directory -Force -Path "$HOME/.codex"`
   - macOS/Linux：`mkdir -p "$HOME/.codex"`

2. 在 Windows 的 `$HOME/.codex/AGENTS.md` 或 macOS/Linux 的 `~/.codex/AGENTS.md` 中维护少量、真正需要对所有项目生效的个人规则。
3. 在每个项目的根目录或相关子目录放置 `AGENTS.md`，记录构建命令、验证方式和项目约束。
4. 仅属于某个项目的 Skill 放在该项目的 `.agents/skills/<skill-name>/SKILL.md` 中。
5. 启动新会话，确认 Codex 能发现 `$HOME/.agents/skills/` 中的 Skills，并按任务触发对应 Skill。

不要把本仓库根目录的 `AGENTS.md` 直接链接为用户级规则文件，因为其中包含只适用于维护 `.agents` 仓库的规则。

官方文档：

- [AGENTS Guidance](https://developers.openai.com/codex/concepts/customization#agents-guidance)
- [Skills](https://developers.openai.com/codex/concepts/customization#skills)

#### GitHub Copilot

1. Windows、macOS 和 Linux 均使用 `$HOME/.agents/skills/`；支持 Agent Skills 的 Copilot 界面会从该目录发现个人 Skills。
2. Copilot 的个人指令通过当前 GitHub.com、IDE 或 CLI 界面配置，不假设存在一个适用于全部界面的全局指令文件。
3. 在项目中创建 `.github/copilot-instructions.md`，保存仓库级指令；只对特定路径生效的规则放在 `.github/instructions/<name>.instructions.md` 中，并通过 frontmatter 限定适用文件。
4. 需要由团队、代码审查或云端 Agent 使用的 Skills，应提交到项目的 `.agents/skills/` 或 `.github/skills/`，不要依赖开发者机器上的个人目录。
5. 启动新会话，并在实际使用的 Copilot 界面确认指令和 Skills 已被发现。

官方文档：

- [自定义 Copilot 响应](https://docs.github.com/en/copilot/concepts/prompting/response-customization)
- [自定义指令支持范围](https://docs.github.com/en/copilot/reference/custom-instructions-support)
- [Agent Skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills)

#### Kilo Code

1. Kilo Code 默认兼容 `$HOME/.agents/skills/` 共享目录，无需复制用户级 Skills。
2. 创建用户级配置目录：

   - Windows PowerShell：`New-Item -ItemType Directory -Force -Path "$HOME/.config/kilo"`
   - macOS/Linux：`mkdir -p "$HOME/.config/kilo"`

3. 在 Windows 的 `$HOME/.config/kilo/AGENTS.md` 或 macOS/Linux 的 `~/.config/kilo/AGENTS.md` 中维护少量全局指令。
4. 在项目根目录放置 `AGENTS.md`，提供项目级指令；子目录也可以放置更具体的 `AGENTS.md`。
5. 需要显式组合多个规则文件时，在全局或项目的 `kilo.jsonc` 中通过 `instructions` 配置路径或 glob。项目专属 Skills 可以放在 `.kilo/skills/` 或 `.agents/skills/`。
6. 修改 Skills 后执行 `/reload`，或启动新会话触发重新扫描。

官方文档：

- [Custom Instructions](https://kilo.ai/docs/customize/custom-instructions)
- [Custom Rules](https://kilo.ai/docs/customize/custom-rules)
- [Skills](https://kilo.ai/docs/customize/skills)

### 3. 验证接入并持续维护

在每个实际使用的平台和 Agent 组合中启动新会话，要求 Agent 列出可用 Skills，再用一个明确匹配 Skill 描述的任务验证按需加载。规则文件则用一条可观察、无副作用的指令确认是否生效。

已在 Windows、Linux 和 macOS 的 VS Code 中，分别要求 GitHub Copilot、Codex 和 Kilo Code 插件列出自动发现的 Skills 及其文件路径。九种平台与插件组合均能从用户目录下的 `.agents/skills/` 自动发现 Skills：

| VS Code 插件 | Windows | Linux | macOS |
| --- | --- | --- | --- |
| GitHub Copilot | 已发现 | 已发现 | 已发现 |
| Codex | 已发现 | 已发现 | 已发现 |
| Kilo Code | 已发现 | 已发现 | 已发现 |

已验证自动发现的 Skill 入口如下：

| Skill | 仓库相对路径 |
| --- | --- |
| `markdown` | `skills/markdown/SKILL.md` |
| `mermaid` | `skills/mermaid/SKILL.md` |
| `skill-authoring` | `skills/skill-authoring/SKILL.md` |
| `wavedrom` | `skills/wavedrom/SKILL.md` |

VS Code 插件列出的绝对路径会展开当前用户的主目录：Windows PowerShell 中形如 `$HOME/.agents/skills/<skill-name>/SKILL.md`，Linux 中形如 `/home/<username>/.agents/skills/<skill-name>/SKILL.md`，macOS 中形如 `/Users/<username>/.agents/skills/<skill-name>/SKILL.md`。如果新增、移动或重命名 Skill，应重新启动插件会话（Kilo Code 也可执行 `/reload`），再次要求插件同时列出 Skill 名称和 `SKILL.md` 路径，以确认发现结果来自预期目录。

- 跨项目 Skills：只在本仓库的 `skills/` 中维护，由三个 VS Code 插件从用户目录下的 `.agents/skills/` 发现。
- 个人全局规则：筛选后同步到各 Agent 的用户级规则入口，保持短小，不复制本仓库专属约束。
- 项目规则和项目 Skills：跟随项目仓库提交，避免影响其他项目。
- 产品支持范围变化时：以各节链接的官方文档为准，并同步更新本教程。

## 可迁移性

- 仓库内引用优先使用相对路径
- 文档中的仓库路径统一使用 `/`
- 避免硬编码用户目录和平台绝对路径
- 避免将配置绑定到单一 Agent 的专有格式

## 验证

在仓库根目录运行：

```shell
markdownlint-cli2
```

`.markdownlint-cli2.jsonc` 定义扫描范围，`.markdownlint.jsonc` 定义 Markdown 规则。
