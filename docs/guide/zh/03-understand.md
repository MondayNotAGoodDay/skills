# 改代码之前先理解它

改你不理解的代码，就是让隐蔽回归溜出去的方式，对智能体和对你都一样。智能体通常以两种方式失败：误读你想要什么，或者缺乏把工作做对的上下文。[提示词里放什么](./02-poteto-mode.md#what-goes-in-a-prompt)解决前者，本页解决后者。

pstack 给你四条路。`/how` 解释代码现在做什么。`/why` 挖出它之所以长这样是为什么。`/teach` 把两者揉成一份解释。`/recall` 重建你自己在某个主题上的近期上下文。每一个还都会让智能体用你能核对的话解释自己。这样你才能监督一个可能比你懂代码的智能体。

![一位侦探拿着放大镜研究机器蓝图，机器人在取卷宗；她身后的证据板用 /how 和 /why 串起线索。](../images/understanding.jpg)

## 先做只读调查

原因不明时，要的是发现，不是修复：

```text
/poteto-mode investigate why background jobs time out every few hours. give me what we know, what data you used, and your best hypotheses. don't change any code yet.
```

"don't change any code yet" 会把这条路由到 [Investigation 剧本](../../../skills/pstack/poteto-mode/playbooks/investigation.md)。它追踪代码，遇到动机类问题会加上 `/why`，返回一份带引用的解释。面对选项之间的抉择，它返回一份附权衡表的建议。要求它说"what data you used"，能让智能体把证据和猜测分开。当发现指向修复时，把修复作为新任务开始。

## 用 `/how` 追踪行为

```text
/how do we dedupe notifications? is there an n+1 when we look up subscribers?
```

问你真正想问的问题。[`/how`](../../../skills/pstack/how/SKILL.md) 读代码，并以一位资深工程师给你做子系统入职培训的水平来回答：运行时流程、关键类型、以及不明显的部分。它派生一个只读子代理去探索代码并写出解释。

## 用 `/why` 挖历史

```text
/why was the retry limit set to five? does the reason still hold?
```

[`/why`](../../../skills/pstack/why/SKILL.md) 从记录而不是代码里找答案。它读引入这段代码的提交和 PR 讨论，以及之后的每次改动；那条线索太薄时会看其他来源。答案引用作者写过的话，把智能体自己的推断标注为推断，并说明记录里没有回答的部分——因为"没人写下原因"本身也是一种答案。

两者可以自然组合。当你怀疑历史能解释这团乱麻时，`do why first then how` 是个好提示词。

## 用 `/teach` 真正搞懂

```text
/teach me how this PR changes retries. convince me it fixes the cause and not the symptom.
```

[`/teach`](../../../skills/pstack/teach/SKILL.md) 适合摘要不够的场景。它运行 `/how` 和 `/why`——小改动可能只跑一个——把发现编织成一份朴素解释，一图一图地建立起来。"convince me" 这个框法值得复用：它把解释变成一个你可以检验的论证，而不是一场参观。

它对智能体自己的选择同样有效：

```text
/teach me why you implemented it this way and not with a queue. what did you trade off, and why?
```

教别人对智能体和对你都有帮助。一个必须解释自己工作的智能体，会去读代码、用证据支撑每个论断，而不是自信地说完就走。

## 用 `/recall` 重建你自己的上下文

```text
/recall catch me up on the export work from last week
```

[`/recall`](../../../skills/pstack/recall/SKILL.md) 挖掘你自己最近的会话加上共享记录（issue、历史修复、仍在触发的错误），交还一份简报：事情进行到哪、下一步是什么。你旧会话里有新智能体缺少的上下文，所以在旧主题上开新工作时，先加载它再交给它新输入：

```text
/recall my work on the virtualized list from yesterday, then read this bug report.
```

如果你想接续某个特定会话，那是下面的 Session pickup 剧本，不是 `/recall`。

## 用 Session pickup 接手前人的工作

当另一个智能体（或上周的你）把分支留在半路：

```text
/poteto-mode take over this branch. read the decision log, figure out what's done, and continue from there. don't redo finished work.
```

[Session pickup 剧本](../../../skills/pstack/poteto-mode/playbooks/session-pickup.md)把前人的痕迹当作权威。它重建分支状态和决策、指出接续点，并对照原始目标验证继承来的论断，而不是从零重新推导一切。

**坑：** 不要因为"反正智能体会读代码"就跳过本页的技能。不做追踪建模就动手改代码的智能体，倾向于在第一个看似合理的位置修症状。先 `/how` 比第二个 bug 便宜得多。

下一篇：[设计变更](./04-design.md)。
