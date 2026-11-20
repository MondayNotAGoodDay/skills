# skills

[English](README.md) | 中文

面向开放 [skills.sh](https://skills.sh) 生态的个人 agent skills。包含 **pstack**——[Cursor pstack](https://github.com/cursor/plugins/tree/main/pstack) 的 harness 无关 fork。

## 安装

```bash
# 安装本仓库全部 skills（跟踪 main；skills update 可拉取新提交）
npx skills add MondayNotAGoodDay/skills

# 只装某一个
npx skills add MondayNotAGoodDay/skills --skill eli5
npx skills add MondayNotAGoodDay/skills --skill poteto-mode

# 钉住某个 GitHub Release 标签（skills.sh 把 @vX.Y.Z 当作 git ref）
npx skills add MondayNotAGoodDay/skills@v0.1.1 --skill poteto-mode
```

只列出、不安装：

```bash
npx skills add MondayNotAGoodDay/skills --list
```

更新已安装的 skills：

```bash
npx skills check
npx skills update -y
```

在 skills.sh 浏览：https://skills.sh/MondayNotAGoodDay/skills

## 发布

版本是形如 `vX.Y.Z` 的 Git 标签。推送此类标签会触发 [`.github/workflows/release.yml`](.github/workflows/release.yml)，发布可被 skills.sh 钉住的 GitHub Release。

```bash
# 在已更新的 main 上
git tag -a v0.1.1 -m "v0.1.1"
git push origin v0.1.1
```

下一次发布用更新的标签（`v0.1.2`、`v0.2.0` 等）。不带标签的安装继续跟踪 `main`；钉住的安装停留在该标签，直到用户再装更新的标签。

## pstack

pstack 是一套严谨的工程工作流：`poteto-mode` 把任务路由到 playbook，其他 skills（`why`、`arena`、`swarm`、`interrogate`，以及 `principle-*`）按步骤需要被调用。需要时也可以用 `/how`。请读[指南](docs/guide/README.md)。

上游面向 Cursor。本 fork 经一次配置即可在任意 harness 上运行（opencode、Devin、Cursor、Claude Code 等）。

### 配置

每个 harness 跑一次：

```text
/setup-pstack
```

Skills 从不点名某个 harness 的工具或模型。它们只说「用只读权限 spawn 角色 X」「问用户」。`/setup-pstack` 通过读取本 harness 的工具 schema，弄清这些事怎么做，为每个角色选模型和推理预算，并写成一份简短的 **pstack profile**，写入该 harness 的常驻说明。开箱默认：代码委派、文案、判断用 Opus 5.5，力度是 xhigh，没有角色超过 xhigh。评审面板之外，只有你点名 Fable 5.2，或同一份简报上两次 Opus 5.5 的 xhigh 运行都没通过检查，才会用它。评审面板各留一席给 Fable 5.2、Opus 5.5、Gemini 4 Neon。`large` 保留这些默认。profile 带一行 `# defaults: 3`。没有这行的 profile 来自旧配置；下次运行会替换它认得的旧默认，并保留你的其他选择。若 harness 把模型绑在 agent 定义上而不是 spawn 调用上（Devin 如此），它会生成对应的 agent 文件。然后用成品配方真正 spawn 子代理，证明可用。

没有 profile 时 skills 仍可跑：spawn 通用子代理且不覆盖模型；若 harness 不能 spawn，则依次跑各角色。

`/poteto-mode` 对应二十二个 playbook。大范围、跨模块或无人值守的工作走 `figure-it-out`。被启动的 agent，其 kickoff 指向 [worker.md](skills/pstack/poteto-mode/worker.md)。`/architect` 画两种形状。`/reflect` 自己读记录，改 skill 之前先等你点头。

### 入口

| Skill | 何时使用 |
| --- | --- |
| [setup-pstack](skills/pstack/setup-pstack/SKILL.md) | 让 pstack 适配当前 harness。先跑这个。 |
| [poteto-mode](skills/pstack/poteto-mode/SKILL.md) | 任何需要严谨流程的任务的默认入口。 |
| [poteto-help](skills/pstack/poteto-help/SKILL.md) | 刚接触 pstack，或不确定该用哪个 skill。 |
| [how](skills/pstack/how/SKILL.md) / [why](skills/pstack/why/SKILL.md) | 一次只读梳理某子系统怎么工作 / 从提交与 PR 讨论看它为何如此。 |
| [architect](skills/pstack/architect/SKILL.md) / [arena](skills/pstack/arena/SKILL.md) / [swarm](skills/pstack/swarm/SKILL.md) | 画两种形状；设计很大或有争议时改走 arena / N 路并行尝试 / N 个并行工人。 |
| [interrogate](skills/pstack/interrogate/SKILL.md) | 对手试图打穿分支或 PR，只有跑通证明的发现才留下。 |
| [ketchup](skills/pstack/ketchup/SKILL.md) | 用白话和小图补上你上次发言之后本对话里发生了什么，并列出所有在等你的调用，让你只看回复就能决定。 |
| [ketchup-video](skills/pstack/ketchup-video/SKILL.md) | 把 `/ketchup` 补课做成带字幕的短幻灯视频，等你的调用作为下方文字。 |
| [in-your-own-words](skills/pstack/in-your-own-words/SKILL.md) | 开工前用自己的话复述目标与问题。 |
| [sharpen](skills/pstack/sharpen/SKILL.md) | 发送前把提示改得更深而不是更长。改写会要求代理用实验能拍板的选择先做原型，产品与偏好选择留给你。不替你做任务。 |
| [create-formal-verification-skill](skills/pstack/create-formal-verification-skill/SKILL.md) | 证明测试很少覆盖到的一个性质，并留下可复查的项目 skill。 |
| [maintain-formal-verification-skill](skills/pstack/maintain-formal-verification-skill/SKILL.md) | 被覆盖的代码变更后，把形式化验证拉回一致，并说明失败是漂移还是 bug。 |
| [statechart](skills/pstack/statechart/SKILL.md) | 检查 TLA+ 模型，画成用户可见的 Mermaid 状态图，标出每个打破规则的状态，并给出到每个状态的最短路径。 |

其余（`blast-radius`、`recall`、`reflect`、`tdd`、`unslop`、`no-comments`、24 个 `principle-*` 等）在 [skills/pstack/](skills/pstack/)。

### 与上游的差异

- Spawn 块使用 `role` / `access` / `run`，而不是 Cursor 的 `Task` 参数。模型 id 写在 [setup-pstack/references/roles.md](skills/pstack/setup-pstack/references/roles.md)。普通 skill 正文不点名它们。
- `poteto-agent` 和 Comment Sicko 是提示文件（[poteto-agent](skills/pstack/poteto-mode/references/poteto-agent.md)、[comment-sicko](skills/pstack/no-comments/references/comment-sicko.md)），不是 Cursor agent 定义。
- 转录、循环、提问工具是 profile 里的能力，不是具名工具。
- 未移植：`make-bot-ui` 与 Benny 自动化包。二者依赖 Cursor Automations。

[UPSTREAM.md](UPSTREAM.md) 记录本 fork 跟随的上游提交。提示正文跟该提交；spawn 块走 profile。

## 其他 skills

| Skill | 何时使用 |
| --- | --- |
| [eli5](skills/other/eli5/SKILL.md) | 极简图示讲解（`/eli5 <topic>`） |
| [show-me](skills/other/show-me/SKILL.md) | 当前话题的可视化图 / 代码形状草图 |
| [html-plan](skills/other/html-plan/SKILL.md) | 把实现计划做成一页可审阅的 HTML（`/html-plan <要做什么>`） |

## 目录结构

```text
skills/
  pstack/          pstack 工作流 skills
    <skill-name>/
      SKILL.md
      references/  可选：skill 按需读取的长参考
      runtime/     可选：skill 运行的脚本
      examples/    可选：完整示例
  other/           独立 skills（eli5、show-me、html-plan）
    <skill-name>/
      SKILL.md
skills.sh.json     skills.sh 仓库页分组
docs/guide/        pstack 指南
third_party/       上游许可证
```

每个 `SKILL.md` 有 YAML frontmatter：`name`（与目录名相同）和 `description`。多数还设了 `disable-model-invocation: true`，因此只有被点名时才会运行。

## 许可证

MIT。pstack 派生自 [cursor/plugins](https://github.com/cursor/plugins/tree/main/pstack)，Copyright (c) 2026 Lauren Tan，MIT，见 [third_party/pstack/LICENSE](third_party/pstack/LICENSE)。`html-plan` 来自 [anthropics/claude-plugins-community](https://github.com/anthropics/claude-plugins-community/tree/main/html-plan)，Copyright (c) 2026 Thariq Shihipar，MIT，见 [third_party/html-plan/LICENSE](third_party/html-plan/LICENSE)。`eli5` 改编自同一社区仓库；`show-me` 来自 [humanlayer/skills](https://github.com/humanlayer/skills)。[UPSTREAM.md](UPSTREAM.md) 记录每一条上游提交。
