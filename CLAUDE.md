# my-agent-skills

bob56621517 的个人自研 Agent 技能市场——一个仓库、一份 `SKILL.md`，四端通用可装：Claude Code / Codex / OpenClaw / skills-cli。

## 组织约定

- **技能唯一内容源**：`skills/<name>/SKILL.md`。frontmatter 必填 `name`（与目录名一致）+ `description`（决定触发）。
- **壳清单只在仓库根，一次建好**，新增技能无需重复：
  - `.claude-plugin/marketplace.json` —— Claude Code 市场（`plugins[]` 数组每新增一个技能加一行）
  - `.codex-plugin/plugin.json`、`.agents/plugins/marketplace.json` —— Codex（`skills: "./skills/"` 指向整个目录）
  - `plugin.json` —— Agent Plugins 厂商中立兜底（OpenClaw 等走 bundle 识别）
- **四端共用同一份 SKILL.md**：不写 Claude 私有字段（如 `context: fork`）或 Codex 私有字段（如 `agents/openai.yaml`）时天然可移植；只有某端独有需求时才加各自文件。

## 新增技能步骤

完全同 [`README.md` 的「如何新增一个技能」](./README.md)：`mkdir skills/<name>` + 写 `SKILL.md` + 在 `.claude-plugin/marketplace.json` 的 `plugins` 数组加一行；其余端自动识别，可选加 `scripts/`/`references/`。

## 红线

- 本仓库不存放 API key/token；任何 `scripts/` 不得提交含密钥的配置，一律走环境变量。
- 不收录第三方收藏技能，仅自研原创。
