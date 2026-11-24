# 提示词配方与坑

值得直接抄的提示词，然后是每个人都会踩一次的错误。把路径和完成条件换成你自己的。这些配方刻意写得非正式——实际使用中就是这么敲的，技能也读得懂意图。

![她品尝一道做好的菜，机器人们照着菜谱盒做菜，柜台上方别着写着 /how、/tdd 和 /loop 的卡片。](../images/recipes.jpg)

## 理解一个陌生的子系统

```text
use /how first to understand how this initialization works. then use /why to figure out why it broke recently.
```

先机制，后历史。每个技能的报告都会告诉你它搜了哪些来源，所以你知道答案建立在什么上。

## 动代码之前先复述一份嘈杂的报告

```text
/poteto-mode read this thread. restate the underlying issue in your own words, in plain english. don't change any code yet.
```

误读会在复述阶段暴露，那时纠正只花一条消息。在智能体说出自己的理解之前，把你自己的理论藏起来。`/in-your-own-words` 只要复述，不走剧本。

## 发送前先打磨提示词

```text
/sharpen write the onboarding doc for the new billing service.
```

智能体会说出专家会做而你没要求的事、他们会从什么原始素材出发、他们的方法和工具、以及一个外部检查来证明结果是对的。它交还一份改写后的提示词，不做任务本身。读起来对就发出去。

## 挑之前先做原型

```text
/poteto-mode prototype a few options for the settings layout. put them behind a switcher and send me screenshots of each.
```

你从能跑的东西里挑，而不是从描述里挑。智能体一路会自己回答布局和时序问题。

## 把定下来的设计变成计划

```text
/poteto-mode turn this design into a plan. small verifiable PRs, each with its own proof.
```

只在设计定下来之后要。计划就是交付物，它会点名将来执行它的剧本。

## 给设计找个第二意见

```text
ask /arena for a second opinion on this thread and our approach
```

你当前的设计变成几个候选之一，综合结果会告诉你评审团是找到了更好的东西，还是确认了你已有的。相对于一个难以回退的设计，这点成本很小。

## 并行检查独立切片

```text
/swarm check every package under packages/ against its check.sh. one worker per package. one report.
```

每个工作节点负责一个包。父会话等齐每个切片，返回一份 `PASS`、`ISSUES` 或 `BLOCKED` 报告，而不是原始工作节点输出。

## 怀疑地评审一个分支

```text
/interrogate the whole branch, but skeptically. don't change anything yet. no nitpicks unless it's an actual bug or regression in behavior.
```

这些限定词干真活。"don't change anything yet" 保持只读，nitpick 规则预先过滤噪音，让验证过的发现值得你花时间。

## 通过失败的测试修 bug

```text
/poteto-mode repro the duplicate write first. if there's a cheap test path, /tdd it. then fix and rerun.
```

"if there's a cheap test path" 很重要。用脆弱 mock 硬塞测试证明的东西比跑真实命令少，而剧本被允许这么说。

## 带证据复现并修复一个报告

```text
/poteto-mode repro this with /verify-<app>. if it repros on main, fix it and show me a video as proof.
```

"if it repros on main" 让运行在 bug 已经消失时提前停。视频让你在读 diff 之前先检查修复。

## 证明一个测试很少触及的性质

```text
/create-formal-verification-skill the retry queue. a message must never be delivered twice.
```

你得到一个在声明范围内每个用例都被检查的性质；如果代码已经破坏它，会得到用你代码术语写的反例；以及一个下一个智能体在该代码任何改动时都会运行的项目技能。

## 发出去之前先核实数字

```text
/benchmark-checklist vet this 40% speedup before it goes in the pr description
```

你得到 faster、slower、no measurable difference 或 inconclusive，附运行次数、区间和限制这个数字的因素。

## 别再手动纠正同一个错

```text
/correct agents keep adding new config flags without registering them in the schema
```

修复以架构、类型、lint 或测试的形式落在仓库里，下一个智能体就犯不了这个错。

## 问怎么做但不启动工作

```text
/poteto-help how do i get poteto-mode to stay on every turn?
```

你得到一个答案、一段可以发送的提示词、一个来源链接。在你发送那段提示词之前，什么都不会跑。

## 离开时让运行保持诚实

```text
im going to bed, keep going autonomously until every fixture passes. do not stop. keep a decision log i can audit in the morning.
```

完整契约在[过夜那一页](./07-overnight.md)。任务和完成条件已经在对话里时，短格式就够用。

## 纠正一条跑偏的运行

引导提示词只有一行：

```text
i said the goal is to repro. i did not ask for a fix yet.
```

```text
apply prove it works. show me the real output, not the build log.
```

```text
/unslop that, no emdashes
```

你很少需要更多字。你需要的是对的名字，[原则那一页](./08-principles.md)就是词汇表。

## 用大白话拿回复

```text
/bro
```

这就是整个提示词。[`/bro`](../../../skills/pstack/bro/SKILL.md) 把上一条消息像一个人跟另一个人说话一样复述，没术语、更短。当一条回复技术上很充分而你还是没看懂时用它。

## 赶上一个你留着跑的会话

```text
/ketchup
```

[`/ketchup`](../../../skills/pstack/ketchup/SKILL.md) 用大白话告诉你自上次消息以来变了什么，然后列出每个等你拍板的决定，附倾向、证据和风险。它汇报时什么都不改。

想看过程而不是读汇报，运行 [`/ketchup-video`](../../../skills/pstack/ketchup-video/SKILL.md)。它用一段带字幕的幻灯片短片展示发生了什么，下面用文字列出等你拍板的决定。

## 这些坑

- **在提示词里枚举技能。** "use /architect then /arena then /interrogate" 会重排剧本已经排好的步骤。说目标和约束。只有要覆盖默认值时才点名技能。
- **含糊的完成条件。** "make it better" 给循环没有可检查的东西。给一个能通过或失败的命令或产物。
- **先抛出你对原因的猜测。** 智能体会去搜你指的任何地方。先让它复述问题，再分享你的直觉。
- **接受第一个设计。** 一次尝试锁定模型最先想到的形状。要原型或 `/architect`，从证据里挑。
- **打磨抽象计划。** 对没有代码支撑的计划做对抗式评审，会发明永远不会发生的风险。先用原型解决悬而未决问题，再评审建成的东西。
- **同一 worktree 里的并行智能体。** 它们互相覆盖，没人说得清哪个智能体改了什么。跑成云端子代理，或说 "own worktree per attempt"。
- **没信任循环就开循环。** 一个不能自我验证的循环只会更快地产出没检查过的工作。先把验证技能跑通。
- **信任没核实过的数字。** 热缓存或被跳过的代码路径能伪造加速。数字流出去之前先跑 `/benchmark-checklist`。
- **手动纠正同一个错。** 会话里的纠正只帮一次运行。`/correct` 修仓库，让之后的运行不再重蹈。
- **拿 `/arena` 做覆盖。** `/arena` 重复同一个设计或代码任务书，然后挑基准候选嫁接最好的部分。`/swarm` 切分切片或声明过的赛跑臂并聚合一份报告。
- **接受每条评审评论。** 机器人和人类在同一张清单里既提交真发现也提交噪音。`/interrogate` 只保留重跑能证明的发现，列出丢弃了什么、为什么，你可以任意一边推翻。
- **把 `inherit` 当模型 id。** `inherit` 的意思是"不做模型覆盖，子代理跑在父会话的模型上"。[安装](./01-setup.md)讲了各角色。
- **凭绿构建报告成功。** 构建只证明能编译。要真实命令、流程、存储值或 profile，并期待回复里带证据。
- **手写 `SKILL.md`。** 走 [Authoring or modifying a skill 剧本](../../../skills/pstack/poteto-mode/playbooks/authoring-a-skill.md)，让校验和评审发生。

这就是这份指南。如果你是跳着读的，回到[安装](./01-setup.md)跑一个真实任务。习惯靠用养成，不是靠读。

回到[指南索引](./README.md)。
