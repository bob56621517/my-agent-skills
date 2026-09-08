# my-agent-skills

bob56621517 的个人自研 Agent 技能市场。本仓库存放可移植的 Agent 技能，每个技能是 `skills/<name>/SKILL.md`，基于 Agent Skills 开放标准，四端通用（Claude Code / Codex / OpenClaw / skills-cli）。

## 如何读取本仓库

- **技能内容**：`skills/<name>/SKILL.md`（frontmatter 的 `name` + `description`，正文为指令）。`scripts/`、`references/` 为可选支撑文件。
- **清单**：`.claude-plugin/` 供 Claude Code、`.codex-plugin/` 供 Codex、根 `plugin.json` 为厂商中立兜底。三处清单指向同一份 `skills/` 内容，不各自复制技能。
- **安装**：见 `README.md` 各端命令。

## 新增技能约定（固定三步）

往本仓库新增一个技能时，遵循如下固定三步；其余端（Codex / OpenClaw / skills-cli）自动识别，无需改动：

1. `mkdir skills/<name>` —— 每个技能一个目录
2. 写 `skills/<name>/SKILL.md` —— frontmatter 必填 `name`（与目录名一致）+ `description`（决定何时触发）；需要脚本/文档时加 `scripts/`、`references/`
3. 在 `.claude-plugin/marketplace.json` 的 `plugins` 数组加一行：`name` / `source: "./skills/<name>"` / `description`

`skills/<name>/SKILL.md` 是唯一内容源；`.codex-plugin/plugin.json` 与根 `plugin.json` 的 `skills: "./skills/"` 已指向整个目录，新技能自动被四端识别。

## 注意

- 本仓库不含任何 API key/token，凭据一律走环境变量。
- 仅收录自研原创技能，不包含第三方收藏。
