# Reference

## Worker prompt

写入临时文件,再据此拉起 agent。填入真实值。PM 只写 spec 原文与 Git 上下文;验收标准、测试命令、commit 约定由 agent 按仓库约定自行决定。

```
任务(spec):

<议题编号 + URL + 正文,或本地需求文本——原样>

Git 上下文:
- repo: <repoPathOrRemote>
- 独立 worktree: <worktreePath>
- working branch: <branch>
- base branch: <baseBranch>

你负责整件事。读仓库里的 AGENTS.md / CLAUDE.md 并遵循其中的约定
(commit 风格、测试执行、审核规则)。你来做、你自己审、自己测、自己提交、
自己 push,把分支留成可合入状态。

状态文件(必填):一开始就写 <worktreePath>/.coding-agent/status.json,
按 schema(<skillReferencePath>#status-file)持续更新 state/progress/updated_at;
结束写 done(或 failed + failure_class),给出 summary/commits/push/pr。

通知(可选,仅当宿主为常驻网关时提供):
- channel: <notifyChannel>
- target: '<notifyTarget>'
完成后,用下面固定命令发一次成功/失败消息:
openclaw message send --channel <channel> --target '<target>' --message '<简要结果>'
```

PM 只在 prompt 里写所需上下文;模型配置走 Env 重映射,不写密钥。测试命令与 commit 约定由 agent 自己决定。

## Env 重映射

用**重映射已有环境变量**来注入可选模型配置。永远是命令前导,只在子进程里生效。PM 只读**变量名**、从不读值——它从不键入、从不记录任何 key。源变量必须已存在于环境中;这是重映射,不是凭据仓库。

```
env -u <targetVar> \
  <targetVar>="${SOURCE_VAR}" \
  [<targetVar2>="${SOURCE_VAR2}"] \
  <command>
```

示例(把已有的 DeepSeek 提供方映射进一次 Codex 调用):

```
env -u OPENAI_API_KEY -u OPENAI_BASE_URL -u OPENAI_MODEL \
  OPENAI_API_KEY="${DEEPSEEK_API_KEY}" \
  OPENAI_BASE_URL="${DEEPSEEK_BASE_URL}" \
  OPENAI_MODEL="${DEEPSEEK_MODEL}" \
  codex exec - < "$PROMPT"
```

规则:

- 对每个 target 变量都 `-u`,防止环境里的旧 auth 混进来,且让映射生效。
- 永远别 `export` 到会话环境。
- token 从不出现在日志、状态文件、议题评论、报告里。

## 状态文件

每个 worker 写 `<worktreePath>/.coding-agent/status.json`(每 worktree 一个,并行 worker 永不撞车;worktree 清理时一并删除)。

```json
{
  "state": "working | done | failed",
  "failure_class": "liveness | correctness | null",
  "updated_at": "<ISO 时间戳>",
  "progress": "简短进度",
  "branch": "<branch>",
  "base_branch": "<baseBranch>",
  "commits": 0,
  "push": "none | remote | local",
  "pr": "<url 或编号或 null>",
  "test_signal": "<agent 自述的测试结果>",
  "summary": "<简要结果>",
  "files_changed": ["<相对路径>"]
}
```

判定规则:

- **`failure_class`** 仅当 `state=failed` 时有意义,由 agent 自报;PM 核对不靠它,**兜底一律按 `liveness` 计**。
- **`updated_at`** 必须持续刷新。**进程退出但 `state` 缺失或仍为 `working` → 一律记 `liveness` 失败**(无刷新证据按最保守判)。
- `state` 与 git 层事实(分支/PR 存在、非空 diff、`commits ≥ 1`、`pr` 已填)才可信;`test_signal` 只作文书,绝不当证据。

## 分支命名

先问用户要名字;默认 `feature-<编号>-<slug>`。**名字里不要 `/`**。

## 安全说明(无沙箱)

- coding agent **无沙箱**、bypassPermissions 运行,是可信体。**Phase 0 是启动时的一次性快照**:用它冒烟并让人确认。**中途 agent 自装工具/MCP 属"可信前提下"的已知风险,无再拦**——把这两句如实写成权衡,而不是"最后一道控制"。
- 每个 agent 限定在它自己的独立 worktree,绝不用主检出目录。
- 把密钥挡在一切会活过子进程的地方之外。
