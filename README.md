# Agent Configuration (`.agents`)

个人可复用的 AI Agent 配置仓库，用于统一维护跨项目 Rules、Skills 和相关验证配置。
项目背景、技术栈、构建方式和业务约束应保存在对应项目中。

## 目录

| 路径                       | 用途                                              |
| -------------------------- | ------------------------------------------------- |
| `AGENTS.md`                | 本仓库的维护规则，不对其他项目生效                |
| `rules/`                   | 可跨项目复用的用户级 Rules                        |
| `skills/`                  | 可复用的任务能力，每个 Skill 以 `SKILL.md` 为入口 |
| `.markdownlint-cli2.jsonc` | Markdown 扫描范围                                 |
| `.markdownlint.jsonc`      | Markdown 检查规则                                 |

## 项目级 Rules 和 Skills 接入

各项目应根据实际需求，在项目级规则入口中显式引用 `$HOME/.agents/rules/` 下需要的
Rules。项目级规则只需补充该项目特有的背景、约束、命令和例外，不应复制或改写用户级
Rule 的通用内容，也不应引用本仓库的 `AGENTS.md`。

大多数 Agent 会自动发现 `$HOME/.agents/skills/` 下的用户级 Skills，无需在项目中显式
引用。仅当 Agent 不支持自动发现时，才应在项目级规则入口中显式引用需要的 Skills。

## 同步仓库

首次安装时，将仓库克隆到用户主目录的 `.agents`。如果目录已经存在，使用原有方式
更新。执行前应将 `<repository-url>` 替换为实际远程地址。

### Windows

```powershell
git clone <repository-url> "$HOME/.agents"
Test-Path "$HOME/.agents/README.md"
Get-ChildItem "$HOME/.agents/rules" -Filter *.md
Get-ChildItem "$HOME/.agents/skills" -Filter SKILL.md -Recurse
```

### macOS 和 Linux

```shell
git clone <repository-url> "$HOME/.agents"
test -f "$HOME/.agents/README.md"
find "$HOME/.agents/rules" -type f -name '*.md'
find "$HOME/.agents/skills" -type f -name SKILL.md
```

`$HOME` 表示当前用户的主目录。

## 可迁移性

- 仓库内引用优先使用相对路径
- 文档中的仓库路径统一使用 `/`
- 避免硬编码具体用户目录
- 避免依赖单一 Agent、语言、框架或项目结构

## 工具安装

部分 Skills 可以使用基于 [Node.js](https://nodejs.org/) 的命令行工具进行自动检查或
渲染。这些工具不是触发 Skill 的前置条件，需要对应工具验证时再按需安装：

| 工具                | 用途                              | 必需性 | 安装命令                                 |
| ------------------- | --------------------------------- | ------ | ---------------------------------------- |
| `markdownlint-cli2` | Markdown Skill 检查 Markdown 文件 | 可选   | `npm install -g markdownlint-cli2`       |
| `wavedrom-cli`      | 渲染 WaveDrom 时序图              | 可选   | `npm install -g wavedrom-cli`            |
| `maid`              | 预检 Mermaid 代码块               | 可选   | `npm install -g @probelabs/maid`         |
| `mmdc`              | 渲染 Mermaid 图                   | 可选   | `npm install -g @mermaid-js/mermaid-cli` |

`-g` 表示全局安装；也可以安装为项目依赖并通过 `npx` 调用。具体命令、适用条件和验证
流程以对应 Skill 为准。`npx` 可能下载并执行缺失的包，使用前应取得用户许可。

C/C++ 编译器、formatter、lint、构建和测试工具等项目依赖应由目标项目提供，不在此处
规定具体实现或安装方式。
