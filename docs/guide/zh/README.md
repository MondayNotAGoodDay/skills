# pstack 使用指南

[English](../README.md) | 中文

pstack 在你不再事无巨细地管着智能体时效果最好。你描述想要什么，以及如何判断它做完了。这份指南用贴近实际的提示词教你这个习惯。

你将学到：

1. [安装 pstack](./01-setup.md)。安装这些技能，并让 `/setup-pstack` 把它们适配到你的运行环境（harness）。
2. [通过 `/poteto-mode` 路由工作](./02-poteto-mode.md)。给它一个目标，看它挑选剧本（playbook）。
3. [理解代码](./03-understand.md)。只读调查，然后在改任何代码之前用 `/how`、`/why`、`/teach` 和 `/recall`。
4. [设计变更](./04-design.md)。`/architect`、`/arena`、`/swarm`、`/interrogate`、原型和计划，让代码定型之前先把形状定下来。
5. [构建并清理变更](./05-build-and-clean.md)。构建类剧本、`/tdd`、`/unslop` 和 `/no-comments`。
6. [验证并交付](./06-verify-and-ship.md)。在真实应用上证明行为，用形式化验证覆盖测试难以触及的性质，用 `/benchmark-checklist` 核实数字，然后开一个聚焦的 PR 并推动它合并。
7. [睡觉时让它跑](./07-overnight.md)。先赢得信任再开循环、过夜任务契约、可审计的决策日志、以及能扩展到多个智能体的协调者。
8. [用原则名称来引导](./08-principles.md)。那 24 个能在任务中途纠偏的名称。
9. [把它变成你的](./09-make-it-yours.md)。你自己的模式、用 `/correct` 修复反复出现的错误、以及如何测试技能改动。
10. [提示词配方与坑](./10-recipes-and-pitfalls.md)。可以直接抄的提示词，和要绕开的错误。

第一次请按顺序读。之后每篇都可独立阅读。

卡住了，或者不知道哪个技能合适时，带着问题输入 [`/poteto-help`](../../../skills/pstack/poteto-help/SKILL.md)：

```text
/poteto-help which skill should i use to review this branch?
```

它会回答，给你一段可以发送的提示词，并附上答案来源的技能或指南页面链接。它不会直接开始干活，因为一次 pstack 运行会消耗真实 token，所以你准备好后再发送提示词。只有你输入它才会运行。

## 如果你只能记住一件事

用你自己的话，给智能体一个目标和一个验证方式：

```text
/poteto-mode the export writes duplicate rows when a retry lands mid-run. repro first, then fix and verify.
```

你不需要点名剧本或罗列技能。"repro first" 和一个可验证的结果就是 `/poteto-mode` 需要的全部路由信号。它会匹配 Bug fix 剧本，把步骤复制进待办清单，并在每一步触发时调用对应的技能。

下一篇：[安装 pstack](./01-setup.md)。
