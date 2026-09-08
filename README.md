# my-agent-skills

bob56621517 的个人自研 **Agent 技能市场**。

一个仓库、一份 `SKILL.md`，四端通用可装：

| 端 | 安装方式 |
|---|---|
| **Claude Code** | `/plugin marketplace add bob56621517/my-agent-skills` |
| **Codex** | `codex plugin marketplace add bob56621517/my-agent-skills` |
| **OpenClaw** | `openclaw plugins install ./my-agent-skills`（或 `git:`） |
| **skills-cli** | `npx skills add bob56621517/my-agent-skills` |

所有技能均为**原创自研**，基于 [Agent Skills 开放标准](https://agent-skill.github.io)（`SKILL.md`），除个别直接受 Claude Code / Codex 私有字段影响外，一份内容四端共用。不收录第三方收藏技能。

## 目录结构

```
my-agent-skills/
├── .claude-plugin/
│   └── marketplace.json      # Claude Code 市场清单
├── .codex-plugin/
│   └── plugin.json           # Codex 插件清单（skills: ./skills/）
├── .agents/plugins/
│   └── marketplace.json      # Codex 市场清单（尽力兼容）
├── plugin.json               # Agent Plugins 厂商中立兜底（OpenClaw 等识别）
├── skills/
│   └── <skill-name>/         # 每个技能一个目录
│       └── SKILL.md          #   技能本体（四端唯一内容源）
├── AGENTS.md                 # 约定与说明（所有 agent 通用，唯一约定文件）
├── README.md
└── LICENSE
```

## 如何新增一个技能

1. `mkdir skills/<技能名>` 并写 `skills/<技能名>/SKILL.md`
   - frontmatter 必填 `name`（与目录名一致）+ `description`（决定何时触发）
   - 需要脚本/文档时再加 `scripts/`、`references/`、`assets/`
2. 在 `.claude-plugin/marketplace.json` 的 `plugins` 数组加一行：
   ```json
   {
     "name": "<技能名>",
     "source": "./skills/<技能名>",
     "description": "一句话说明",
     "author": { "name": "bob56621517" },
     "category": "development"
   }
   ```
3. 其余端无需改动：`.codex-plugin/plugin.json` 与根 `plugin.json` 的 `skills: "./skills/"` 已指向整个目录，新技能自动被识别。

## 各端安装详情

### Claude Code

```bash
/plugin marketplace add bob56621517/my-agent-skills
/plugin install git-context-prep@my-agent-skills
```

### Codex

```bash
codex plugin marketplace add bob56621517/my-agent-skills [--sparse .agents/plugins]
codex plugin add my-agent-skills@my-agent-skills
```

### OpenClaw

OpenClaw 通过 bundle 兼容层自动识别本仓库（无需 `openclaw.plugin.json`，加了反而跳过 bundle）：

```bash
openclaw plugins install ./my-agent-skills
openclaw gateway restart
```

### skills-cli

```bash
npx skills add bob56621517/my-agent-skills
```

## 安全提示

本仓库**只存放 `SKILL.md` 与清单文件，不包含任何 API key/token**。为技能添加 `scripts/` 时，请勿提交含密钥的配置或凭据——敏感信息一律走环境变量。
