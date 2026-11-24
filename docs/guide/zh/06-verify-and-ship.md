# 验证结果并开 PR

"能编译"不是证据。[Prove It Works 原则](../../../skills/pstack/principle-prove-it-works/SKILL.md)让智能体在报告成功前检查真实产物，而你的工作是让"真实产物"可检查。本页覆盖：陈述完成条件、核实测量数字、为你的应用生成验证技能、形式化验证测试很少触及的性质、开 PR 并推动它合并。

验证是大多数智能体工作里最慢的一步，因为它通常要等人。让智能体能做验证，你就不再是瓶颈。跳过它，跑更多智能体只会给你更多没检查过的工作要审。

![一架原型飞机飞过真实考核航线，她拿秒表计时，机器人们拍摄并对照清单检查；终端上显示 verify: pass, evidence: captured。](../images/verification.jpg)

## 一开始就说清完成条件

在第一个提示词里写明什么叫做完，用什么词都行：

```text
/poteto-mode add json output to this command. text output stays byte-identical, the json parses, both run against the sample project. show me the evidence.
```

现在智能体有三个能跑的检查，而不是一个要讨好的心情。回复回来时，应该带着确切的命令和输出。如果某个检查跑不了，好回复会说 "inconclusive"。没有证据的自信回复是警报。

让检查匹配变更：

- CLI 改动跑真实命令。
- UI 改动在运行中的应用里走一遍改过的流程。当必须逐像素对齐参考时，[Visual parity 剧本](../../../skills/pstack/poteto-mode/playbooks/visual-parity.md)用冻结基线做截图 diff，而不是靠肉眼判断。
- 解析器或迁移重放一份已保存的输入。
- perf 改动对比前后的 profile。
- 存储改动读回写入的值。

把证据要成你自己能检查的产物：先失败后通过的测试、前后对比视频、trace、截图。如果修复已经合并，让它在 main 上再跑一次同样的检查。产物胜过看似合理的解释，因为你可以质疑它，而不必重放整个运行。

对于一个你不完全信任的小 diff，[`/blast-radius`](../../../skills/pstack/blast-radius/SKILL.md)能找出它可能在别处弄坏什么。它找到让这个改动安全的那一个事实，并通过跑代码证明它，而不是靠论证。

## 用 `/benchmark-checklist` 核实测量数字

前后对比数字是最容易被无意弄错的证据。热缓存、单侧的 debug 构建、或根本没进计时区域的工作，都能造出一个貌似可信的加速。报告或据此行动之前，输入：

```text
/benchmark-checklist vet the export speedup before it goes in the pr
```

[`/benchmark-checklist`](../../../skills/pstack/benchmark-checklist/SKILL.md) 问七个问题，每个都要真实运行的证据：

1. 什么限制了这个数字？为什么不可能是它的两倍？
2. 每一侧是不是都按生产环境的方式调优过？
3. 结果会不会突破物理极限，比如磁盘带宽或核数？
4. 有没有报错或输出错误结果？
5. 交替运行能复现吗？有没有中位数和区间？
6. 端到端看重要吗？在用户等待的路径上？
7. 工作真的发生在计时区域内吗？

结论会是 faster、slower、no measurable difference 或 inconclusive，附运行次数、区间和限制因素。说不出限制因素、或某一侧没按调优方式跑时，它会给 inconclusive。`/poteto-mode` 已经在 Perf issue 和 Hillclimb 剧本内部跑这份清单，所以当你在剧本之外自己测了东西、或别人给的数字好得可疑时，你再自己敲它。这是 [Explain the Number 原则](../../../skills/pstack/principle-explain-the-number/SKILL.md)的可执行版本。

## 创建项目验证技能

上面 UI 那条背后藏着一个真实要求：智能体需要一个能脚本化驱动你应用的方式。项目里有就好，没有就运行：

```text
/create-verification-skill
```

[`/create-verification-skill`](../../../skills/pstack/create-verification-skill/SKILL.md) 访谈的是仓库，不是你。它弄清用户碰得到什么、应用怎么本地启动、什么能驱动它（优先现有 harness，否则浏览器加 CDP、PTY 或纯 HTTP）、什么证据能证明行为、以及两个实例能否并行跑。它只问代码答不了的问题。

它会写出 `.agents/skills/verify-<app>/`：面向智能体的说明，含 Launch、Doctor、Drive、Evidence、Cleanup 各节，附带它自带脚本的 Helpers 节，以及 `features/` 下的功能地图，索引应用做什么、每个功能用什么结果来证明可用。技能附带一份[可运行的功能地图示例](../../../skills/pstack/create-verification-skill/references/feature-map-example/)，一个 README 索引加每个功能一个文件，使用四个必需的二级标题。交付前，生成器会端到端证明一次这个技能：启动、doctor 检查、驱动一个功能、抓证据、清理，然后确认证据在清理后还在。如果这个证明失败，别用它的产物。

从此，"在应用里验证它"成为任何智能体都能执行的一步，在这个仓库里，不需要任何设置对话。想要特定形式的证据时在提示词里点名：

```text
/poteto-mode build the bulk-archive action. use /verify-<app> to verify your changes and show me a video and screenshots as proof.
```

```text
/poteto-mode repro this with /verify-<app>. if it repros on main, fix it and show me a video as proof.
```

验证技能跑通之后，一个 [`/swarm`](../../../skills/pstack/swarm/SKILL.md) 可以按功能地图条目切分一次完整验证并聚合结果。验证者集群还能在足够大的样本上确认 perf 提升，或在 PR 交付前对应用做回归模糊测试。

把验证技能当基础设施，不要当一次性的东西。把它提交进仓库，让团队里每个人和每个智能体用同样的方式驱动应用。然后[造杠杆](../../../skills/pstack/principle-build-the-lever/SKILL.md)。当智能体反复手写一次性脚本点应用时，要一个小型控制 CLI 让技能调用。智能体省 token，每次运行也可复现。一个被智能体用得好的 CLI 有这些特征：

- 少量可组合的命令，每个做真活，而不是一堆薄命令。
- 任何破坏性操作都有 `--dry-run`。
- 子命令逐步暴露功能，而不是一次全给。
- 报错信息告诉你该怎么做。
- 丰富的 `--help` 文本。
- 机器可读的输出，比如 JSON。

顺手把开发环境也做成可复现的：种子数据、测试用户、一条命令每次都把环境拉起成同样的样子。

## 让验证技能保持诚实

应用会变，功能地图会过期。至少每天跑一次，最好从定时自动化跑，免得靠人记：

```text
/maintain-verification-skill
```

[`/maintain-verification-skill`](../../../skills/pstack/maintain-verification-skill/SKILL.md) 审计生成的技能：并行派一个只读源码阅读者覆盖每个功能，然后跑一遍活体验证驱动每个已映射功能。结局只有三种之一。`clean` 表示覆盖完整、无需交付。`changed` 表示一个 PR 装着已证实的修正，且只动验证技能自己的目录。`blocked` 指明阻塞点。它绝不改产品代码。如果活体验证抓到产品回归，它报告回归，而不是用文档改动把它藏起来。

## 用形式化验证技能证明一个性质

验证技能证明你驱动的路径。有些 bug 住在没人驱动的路径上：两个重叠的重试、写到一半过期的锁、乱序到达的状态变更。测试很少触及那些用例。对这类 bug 代价高的代码，运行：

```text
/create-formal-verification-skill
```

[`/create-formal-verification-skill`](../../../skills/pstack/create-formal-verification-skill/SKILL.md) 从代码里挑目标：代码许下的一个承诺，破坏它会伤害用户、而测试很少触及破坏它的用例。它挑最强的目标，而不是最容易验证的。也可以点名一个领域或一个 bug 作为起点：

```text
/create-formal-verification-skill the job lease and retry logic. a job must never run twice at once.
```

问题决定工具。事件会不会以坏顺序发生，适合对设计的小模型用模型检查器，任何语言里这类问题 TLA+ 是默认。代码对每个输入是否都表现正确，适合跑在代码上的验证器。如果你的语言没有可用的验证器，智能体会明说，并用生成的输入测试真实代码，而不是手写一个函数模型。必须在任意规模下都成立的性质可能需要证明。智能体会告诉你它选了哪个工具、为什么。

它验证一个性质，并在代码的一个破损变体上演示该性质失败——因为一个你从没见它失败过的检查什么也证明不了。当现有代码已经破坏该性质时，你会得到一个反例，是用你代码的术语写成的短步骤序列。性质保持原样，所以发现仍是发现。除非你要求，产品代码不变。

它写出一个项目本地技能，告诉下一个智能体：某次改动是否碰到被验证的代码、用一条命令怎么跑检查、以及怎么区分真 bug 和建模错误。交付前，生成器会在一个破坏该性质的 scratch 改动上按这个技能走一遍，检查必须抓到它。交接说明里写明：用什么工具、什么边界验证了什么、检查发现了什么、以及它不覆盖什么。一次通过覆盖的是模型或边界，不是整个系统。

技能还记录每个检查覆盖哪些代码、检查最后一次与之匹配的提交。跳过技能的合并和重构，可能让检查在一份已不存在的代码描述上继续通过。之后运行 [`/maintain-formal-verification-skill`](../../../skills/pstack/maintain-formal-verification-skill/SKILL.md)。它从代码更新验证、重跑，并演示每个更新后的检查仍能失败，告诉你每个失败是验证的漂移还是代码的 bug。

## 把行为画成状态图

用模型术语写的反例能说服你，但拥有该行为的 PM 或设计师需要用他们的术语看到它。运行：

```text
/statechart what the user sees when they edit a note offline
```

[`/statechart`](../../../skills/pstack/statechart/SKILL.md) 画一张用户所见的 Mermaid 状态图，由 TLA+ 模型生成，所以图和被检查的模型不可能漂移。读者的规则（"离线时你永远看不到 Synced"）变成不变式。图上标出破坏每条规则的状态，报告用读者的话给出通往它的最短路径。交付前，智能体在真实代码里复现每条 bug 路径，免得建模错误以 bug 的身份到达读者。

## 开 PR

```text
/poteto-mode open the pr. small ordered commits, evidence in the description.
```

[开 PR 剧本](../../../skills/pstack/poteto-mode/playbooks/opening-a-pr.md)从 worktree 出发，把工作整理成小而可落地的提交、清理 diff、把一分钟简报写成正文，然后开好一个 ready 的 PR。叠起来的小步跟进胜过一根越长越大的分支。

## 用 Babysit 把 PR 推到可合并

一个打开的 PR 立刻开始积累阻塞：检查失败、评审评论、主干前进。把这份 churn 交给 [Babysit 剧本](../../../skills/pstack/poteto-mode/playbooks/babysit.md)：

```text
/poteto-mode babysit this pr. get it green.
```

Babysit 盯着 PR，GitHub 上有捆绑的 watcher 时用它或 Origin CLI，然后按顺序处理阻塞：冲突、评审线程、CI。它绝不 rebase 或 force-push。冲突或过期的基线会连同需要 rebase 的分支一起交还给你。评论分诊是怀疑论的，因为人和机器人在同一张清单里既提交真发现也提交噪音。真发现得到修复，噪音则带着驳斥依据留在原线程上驳回。当你只想要状态时，问得更小，Babysit 会直接回答而不开循环：

```text
/poteto-mode check on pr 123. anything outstanding?
```

Babysit 停在可合并。即使全绿它也绝不合并，因为合并是另一个决定。

## 用 Shipping 落地整个栈

绿不等于安全。准备落地时说出来：

```text
/poteto-mode land the stack.
```

[Shipping 剧本](../../../skills/pstack/poteto-mode/playbooks/shipping.md)在启动任何动作之前独立验证每个 PR。每个 PR 一个全新智能体在真实运行的应用里证明行为，评判改动的智能体绝不是写它的那个。然后 Shipping 在启动任何动作之前，只从底部开始落地连续的已验证段，一次一个 PR，默认走 GitHub，有 Origin CLI 时走 Origin，并报告第一个打破链条的 PR。躺在未验证 PR 上面的已验证 PR 会等待，因为合并它会把缺口一起拖进来。

下一篇：[睡觉时让它跑](./07-overnight.md)。
