# 把它变成你的

poteto-mode 是一个人的风格。它底下的剧本、路由和模型角色，同样适合你的风格。本页覆盖：生成你自己的模式、从一次会话中沉淀教训、修好仓库让智能体不再重复犯错、编写一个聚焦的技能，以及在信任一个技能改动之前先测试它。

从比你想的小处开始。第一天不需要很多技能，甚至不需要整个这个仓库。平实地提需求，观察智能体在哪里失败，同一个失败出现两次时再加一个技能或检查。想保留你的改动，fork 仓库并用 `npx skills add <you>/skills` 从你的 fork 安装。

## 用 `/automate-me` 生成你自己的模式

```text
/automate-me
```

你不用描述自己的风格，因为 [`/automate-me`](../../../skills/pstack/automate-me/SKILL.md) 从你的历史里读出来。它挖掘当前工作区里你最近的会话记录，找出你反复表现出的偏好——回复方式、委派、验证、代码、行文、流程——然后问你哪些模式真的是你。它通过一个技能编写技能起草 `.agents/skills/<your-name>-mode/SKILL.md`（如果装了的话），把草稿过一遍 [`/unslop`](../../../skills/pstack/unslop/SKILL.md)，并从 worktree 开一个 PR，让你像评审任何改动一样评审它。

习惯漂移时再跑一次：

```text
/automate-me update my mode skill with everything since its last edit
```

更新模式只挖自该技能上次改动以来的历史。你没否认过的规则保留，有新证据的修订，只为新模式加章节。

## 用 `/reflect` 沉淀一次会话的教训

刚做完一个让你学到东西的任务时，运行：

```text
/reflect that took way too long. capture what we learned so the next run doesn't repeat it.
```

[`/reflect`](../../../skills/pstack/reflect/SKILL.md) 读会话记录，只保留会改变未来智能体工作的教训，把提案分进 `Accepted`、`Rejected` 和 `Backlog`。任何技能改动之前它都等你的批准。只有当一个提案会改变未来的决定时才批准它。一次奇怪的会话是轶事，不是规则。

## 用 `/correct` 修好环境

当你一遍又一遍纠正智能体犯同一个错时，修复应该落在仓库里，而不是你的下一条提示词里。按约束力排序：

1. 用架构或更好的数据结构让这个错误不可能发生。
2. 用类型拦住它，或用一个报错信息指明修法的 lint / CI 检查拦住它。
3. 用测试抓住它。
4. 写成文档或智能体规则。没有东西在智能体跳过规则时失败，所以它排最后。

人工评审不在清单上。一个必须在每个 PR 上抓住同一个错的评审者，正是这个工具要修的问题。[`/correct`](../../../skills/pstack/correct/SKILL.md) 做这项工作：

```text
/correct agents keep calling the database client directly instead of going through the repository layer
```

它读最近的提交、回退、评审评论和解释变通办法的注释，然后把错误归成类别。发生两次的类别才算数。它按频率从高到低修最常见的类别，每类一个提交，选能奏效的最高层级，并证明每个新检查都能在一个真实的历史错误上失败。它还在智能体指令文件里维护一张表，把每条规则和执行它的东西配对，所以一条没有任何东西执行的规则会显形为重复出现。回复列出每个类别及其证据、所选层级、以及为什么更高层级行不通。

不带参数运行，它会自己从历史里找类别。`/reflect` 和 `/correct` 分工：`/reflect` 从单次会话改进技能，`/correct` 改仓库让一个错误类别无法卷土重来。修复是新边界时把它和 `/architect` 搭配。[并行跑多个项目](./07-overnight.md#run-many-projects-in-parallel)里有一条在整个仓库上两者兼顾的提示词。

## 编写一个聚焦的技能

当你已经知道想沉淀的工作流时：

```text
/poteto-mode write a skill for verifying database migrations in this repo
```

写技能会匹配 [Authoring or modifying a skill 剧本](../../../skills/pstack/poteto-mode/playbooks/authoring-a-skill.md)，它会路由到技能编写技能（如果装了）、校验 frontmatter 和链接，并通过开 PR 剧本交付结果。面向智能体的行文标准比面向人类的更高，因为一句没用的话会变成某个未来智能体遵循的指令。让剧本守住这个标准，别手写 `SKILL.md`。

两个特例有自己的生成器。必须驱动你的应用并证明行为的技能是验证技能，用 [`/create-verification-skill`](../../../skills/pstack/create-verification-skill/SKILL.md) 和 [`/maintain-verification-skill`](../../../skills/pstack/maintain-verification-skill/SKILL.md)。用模型检查器、证明或其他验证器检查代码性质的技能是形式化验证技能，用 [`/create-formal-verification-skill`](../../../skills/pstack/create-formal-verification-skill/SKILL.md) 和 [`/maintain-formal-verification-skill`](../../../skills/pstack/maintain-formal-verification-skill/SKILL.md)。[验证与交付](./06-verify-and-ship.md#prove-a-property-with-a-formal-verification-skill)两种都讲了。

## 用 `/technical-writing` 把文档写到标准

技能不是你交付的唯一行文。文档、RFC、README、PR 描述和提交信息：

```text
/technical-writing review the readme changes
```

[`/technical-writing`](../../../skills/pstack/technical-writing/SKILL.md) 应用一套分层标准。它的目标是疲惫的工程师一遍就能读懂的行文。它先定文档模式（教程、操作指南、参考或解释），然后逐句打磨：谁做什么、一句一个意思、没有两种读法。用它评审你或智能体刚写的东西，或者在要文档时一开始就点名它。

## 盲测一个技能改动

技能改动影响每个未来会话，所以要像对待实验一样测试它。采用别人的技能也一样。在保留它之前，先确认它让智能体在你的工作上变好了。

```text
/poteto-mode run the eval playbook on this skill change. same task for both variants, candidates stay blind.
```

当一个技能反复失手而你知道它该做什么时，在一个任务里改并测：

```text
/poteto-mode update the review skill so it flags missing migrations, and eval the change.
```

一开始就要求评估能让改动保持诚实。从一次糟糕会话写出的修复倾向于过拟合那次会话，积累多了技能就会漂移。评估在漂移交付之前抓住它。

[Eval 剧本](../../../skills/pstack/poteto-mode/playbooks/eval.md)围绕一种失败模式构建：观察者效应。知道自己被评估的智能体行为会不同。所以候选智能体拿到看起来正常的任务、在隔离的目录里，永远听不到 "eval" 或 "candidate" 字样，也永远不知道彼此存在。一个裁判在中性标签下给所有输出打分，链路遵循按每个候选实际读了哪些文件来评分，而不是它声称读了什么。

接受结论之前自己读完每份输出。如果你不同意裁判，先怀疑评分表，再怀疑自己的判断。

**坑：** 不要在任务中途因为技能行为异常就改它。在它自己的 PR 里修，任务继续走。缠进 feature 工作里交付的技能改动对评审不可见，也无法评估。

下一篇：[提示词配方与坑](./10-recipes-and-pitfalls.md)。
