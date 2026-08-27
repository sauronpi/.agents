# Agent Configuration (`.agents`)

个人可复用的 AI Agent 配置仓库，用于统一维护跨项目 Rules、Skills 和文档验证配置。

目标：

- 在多个项目之间复用稳定的工作原则和工具能力
- 保持 Agent、语言、框架和项目无关
- 支持 Windows、macOS 和 Linux

项目背景、技术栈、构建方式和业务约束应保存在对应项目的规则入口或项目文档中。

## 目录结构

```text
.agents/
├── .markdownlint-cli2.jsonc
├── .markdownlint.jsonc
├── AGENTS.md
├── README.md
├── rules/
│   ├── adversarial-review.md
│   ├── c-code-style.md
│   ├── code-review.md
│   ├── document-review.md
│   └── first-principles.md
└── skills/
    ├── c-code-style/
    │   ├── SKILL.md
    │   └── references/
    │       └── style-guide.md
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

- 根目录的 `AGENTS.md` 只规定如何维护本仓库，不是可复用 Rule。
- `rules/` 保存可跨项目复用的用户级 Rule。
- `skills/` 保存可复用的任务能力，每个 Skill 以独立目录中的 `SKILL.md` 为入口。

Rules 不会因为仓库位于用户目录而自动生效。项目级规则入口应显式引用所需的用户级
Rule，并只维护当前项目的补充和例外，不得复制或改写用户级 Rule 的通用内容。

## 项目级接入

项目级接入以目标项目为作用域：

- 先读取项目已有规则，避免重复和冲突。
- 显式引用当前项目需要的用户级 Rules，不复制其内容。
- 项目级规则只补充项目特有的背景、约束、命令和例外。
- 项目级规则入口由目标项目及其使用的 Agent 决定，本仓库不规定专有文件名或语法。
- 引用的用户级 Rules 必须在实际运行环境中存在且可读。
- 不得把本仓库根目录的 `AGENTS.md` 接入其他项目。

### 1. 同步共享仓库

首次安装时，将仓库克隆到用户主目录的 `.agents`。如果目录已经存在，使用原有方式
更新。执行前应将 `<repository-url>` 替换为实际远程地址。

#### Windows

```powershell
git clone <repository-url> "$HOME/.agents"
Test-Path "$HOME/.agents/README.md"
Get-ChildItem "$HOME/.agents/rules" -Filter *.md
Get-ChildItem "$HOME/.agents/skills" -Filter SKILL.md -Recurse
```

#### macOS 和 Linux

```shell
git clone <repository-url> "$HOME/.agents"
test -f "$HOME/.agents/README.md"
find "$HOME/.agents/rules" -type f -name '*.md'
find "$HOME/.agents/skills" -type f -name SKILL.md
```

`$HOME` 表示当前用户的主目录。

### 2. 项目级引用用户级 Rules

在目标项目根目录启动 Agent，使用以下提示词建立引用和项目级补充：

```text
请为当前项目接入 `$HOME/.agents/rules/` 中适用的用户级 Rules：

1. 读取当前项目实际生效的项目级规则入口、其他 Agent 指令以及开发和验证文档，保留
   现有有效内容。
2. 读取 `$HOME/.agents/rules/` 下的全部 Rule，根据当前项目的真实需要选择适用文件；
   不要默认引用全部 Rules。
3. 在项目级规则入口中显式引用所选用户级 Rule。优先使用当前入口支持的引用方式；
   没有专用语法时，写明在执行相关任务前必须读取对应文件。
4. 不要复制、合并、摘要或改写用户级 Rule 的内容，也不要引用
   `$HOME/.agents/AGENTS.md`，因为它只约束 `.agents` 仓库本身。
5. 项目级规则只补充项目特有的背景、约束、构建和验证命令，以及必要且明确限定范围的
   项目级例外；不要重复用户级 Rule。
6. 检查引用路径是否可解析、Agent 是否会实际读取、项目级补充是否冲突以及 Markdown
   格式。最后报告引用的用户级 Rules、项目级补充、验证结果和剩余限制。
```

仅把 Rule 文件保存在 `$HOME/.agents/rules/` 中不代表它已经生效。项目级规则入口必须
显式引用所需文件，并确保 Agent 会读取该引用。项目级规则是用户级 Rules 之上的增量层，
不是用户级 Rules 的副本。

### 3. 接入项目级 Skills

支持用户级 Skills 发现的 Agent 可以从 `$HOME/.agents/skills/` 加载个人 Skills。
如果 Skill 必须供团队、CI 或云端 Agent 使用，应将整个 Skill 目录接入目标项目的
`.agents/skills/`，并保留 `SKILL.md`、`references/`、`scripts/` 和 `assets/` 之间的
相对引用。

项目专属 Skill 应直接在目标项目中维护，不应加入本仓库。

### 4. 接入通用 C 代码风格

普通 C、嵌入式 C 或 C/C++ 混合编译项目应在项目级规则入口中加入以下指令，并将项目
配置替换为实际值：

```text
处理人工维护的 .c 或 .h 文件前，必须读取并遵循：
- $HOME/.agents/rules/c-code-style.md
- $HOME/.agents/skills/c-code-style/SKILL.md

项目配置：
- C standard: C99
- C++ standard: C++11
- Public symbol prefix: <module_>
- Constant symbol prefix: <MODULE_>
- C compiler: <command>
- C++ compiler: <command>
- Formatter/lint/build/test: <commands>
- Shared C/C++ headers: <paths or all public headers>
- Project exceptions: <exceptions or none>
```

`C99` 和 `C++11` 是项目没有更明确约束时的默认检查标准。公共符号前缀未声明时可以从
现有 API 推断，但无法可靠推断且需要新增公共符号时，必须先询问用户。没有公共 API 的
项目可以明确声明无需公共前缀。

共享头文件使用条件化的 `extern "C"` 支持同一项目内的 C/C++ 混合编译。这不表示项目
承诺数据布局、跨编译器兼容、动态库版本兼容、其他语言 FFI 或绑定生成。

### 5. 验证项目级接入

在目标项目中启动新会话并检查：

1. 项目级入口对用户级 Rules 的引用是否存在、可解析且实际被加载。
2. 用一条可观察、无副作用的指令分别确认用户级 Rules 和项目级补充已生效。
3. 要求 Agent 列出可用 Skills，并用匹配任务验证所需 Skill 能被触发。
4. 检查项目级规则和 Skills 的相对链接、格式及项目已有验证。

修改、移动或重命名 Rule、Skill 或项目级入口后，应重新执行验证。Agent 不支持列出
加载来源时，应通过可观察行为验证，不要仅根据文件存在推断接入成功。

## 维护边界

- 跨项目 Rules：在本仓库的 `rules/` 中维护，由项目级规则入口按需引用。
- 跨项目 Skills：在本仓库的 `skills/` 中维护。
- 项目规则：只维护项目级补充和例外，需要共享时跟随项目提交。
- 项目 Skills：在目标项目中维护，需要共享时跟随项目提交。
- 本仓库的 `AGENTS.md`：只约束本仓库维护，不对其他项目生效。

## 可迁移性

- 仓库内引用优先使用相对路径
- 文档中的仓库路径统一使用 `/`
- 避免硬编码具体用户目录
- 避免依赖单一 Agent、语言、框架或项目结构

## 工具安装

`.agents` 仓库验证和部分 Skills 渲染使用的命令行工具基于 Node.js。先安装
[Node.js](https://nodejs.org/)（含 `npm` 和 `npx`），再按需安装下列工具。

`-g` 表示全局安装，便于在任意目录运行；也可不加 `-g` 在项目内局部安装并通过
`npx` 调用。下列命令在 Windows、macOS 和 Linux 通用。

| 工具 | 用途 | 必需性 | 安装命令 |
| --- | --- | --- | --- |
| `markdownlint-cli2` | `.agents` 仓库自身的 Markdown 验证 | 工具可用时必需 | `npm install -g markdownlint-cli2` |
| `wavedrom-cli` | `wavedrom` Skill 渲染时序图为 SVG | 可选 | `npm install -g wavedrom-cli` |
| `maid` | `mermaid` Skill 预检 Markdown 中的 Mermaid 代码块 | 可选 | `npm install -g @probelabs/maid` |
| `mmdc` | `mermaid` Skill 将 Mermaid 渲染为 SVG/PNG（补充验证） | 可选 | `npm install -g @mermaid-js/mermaid-cli` |

`mermaid` Skill 也可不经预装、按其 `SKILL.md` 通过 `npx` 临时调用上述工具；仅在用户
允许下载并执行第三方 npm 包时使用。

未安装可选工具时，对应 Skill 仍可用于编写和审阅，但无法在本地预检或渲染产物验证。

## 验证

在仓库根目录运行：

```shell
markdownlint-cli2
```

`.markdownlint-cli2.jsonc` 定义扫描范围，`.markdownlint.jsonc` 定义 Markdown 规则。
