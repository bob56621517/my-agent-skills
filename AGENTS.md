# my-agent-skills

bob56621517 的个人自研 Agent 技能市场。本仓库存放可移植的 Agent 技能，每个技能是 `skills/<name>/SKILL.md`，基于 Agent Skills 开放标准，四端通用（Claude Code / Codex / OpenClaw / skills-cli）。

## 如何读取本仓库

- **技能内容**：`skills/<name>/SKILL.md`（frontmatter 的 `name` + `description`，正文为指令）。`scripts/`、`references/` 为可选支撑文件。
- **清单**：`.claude-plugin/` 供 Claude Code、`.codex-plugin/` 供 Codex、根 `plugin.json` 为厂商中立兜底。三处清单指向同一份 `skills/` 内容，不各自复制技能。
- **安装**：见 `README.md` 各端命令。

## 注意

- 本仓库不含任何 API key/token，凭据一律走环境变量。
- 仅收录自研原创技能，不包含第三方收藏。
