# 构建变更并清理 diff

构建类剧本共享同一条纪律：说出你观察到的，让剧本索要证据。本页展示每类常见构建任务该在提示词里放什么，然后是让 diff 保持可评审的清理习惯。

## 给每个构建剧本喂你知道的信息

bug 提示词陈述症状，并要求先复现：

```text
/poteto-mode this command emits two records after a retry. repro first, then fix and verify.
```

feature 提示词陈述行为以及什么不许变：

```text
/poteto-mode add a --json flag. text output stays byte-identical. verify both forms.
```

重构提示词在结构移动之前先钉死行为：

```text
/poteto-mode move parsing into one module, zero behavior change. record the current output first and prove it's unchanged after.
```

perf 提示词给出测量值，而不是印象：

```text
/poteto-mode startup takes 1.8s on this fixture. trace it, fix the measured cause, show me before and after.
```

这些都会路由到各自的剧本（[Bug fix](../../../skills/pstack/poteto-mode/playbooks/bug-fix.md)、[Feature](../../../skills/pstack/poteto-mode/playbooks/feature.md)、[Refactoring](../../../skills/pstack/poteto-mode/playbooks/refactoring.md)、[Perf issue](../../../skills/pstack/poteto-mode/playbooks/perf-issue.md)），剧本补上你没打出来的步骤：修复前先复现、实现前先说清数据形状、重构前先钉住行为、优化前先 profile。

想持续改进某个数字，有 [Hillclimb 剧本](../../../skills/pstack/poteto-mode/playbooks/hillclimb.md)。给它指标、目标和尝试次数下限，它用冻结的测量装置一次循环一个假设。赢的保留，其余全部回退。

两个 perf 剧本都会对数字跑 [`/benchmark-checklist`](../../../skills/pstack/benchmark-checklist/SKILL.md)。Perf issue 审查它的基线和之后的每个数字，Hillclimb 在冻结装置前审查装置。[验证与交付](./06-verify-and-ship.md#vet-a-measured-number-with-benchmark-checklist)讲什么时候要自己敲它。

有时你想先要原因再谈修复。面对活的症状——泄漏、空闲 CPU 空转、视觉故障——[Runtime forensics 剧本](../../../skills/pstack/poteto-mode/playbooks/runtime-forensics.md)会给运行中的进程做插桩。面对你已经抓到的 profile，[Trace forensics 剧本](../../../skills/pstack/poteto-mode/playbooks/trace-forensics.md)读产物并把热点帧映射回源码。两者都只返回诊断，不返回修复：

```text
/poteto-mode here's a cpuprofile from the slow startup. tell me where the time goes and which source lines own it. no fix yet.
```

## 用 `/tdd` 先写失败的测试

当 bug 有一条便宜的本地测试路径时，整个提示词可以只有两个词：

```text
/tdd implement
```

在上下文里这就够了。[`/tdd`](../../../skills/pstack/tdd/SKILL.md) 写出因预期原因失败的最小测试，然后写修复，再重跑测试。如果某个测试需要大范围的脚手架或脆弱的 mock，技能会明说，并改用最接近的可执行检查。别在真实命令证据更强的地方硬塞测试。

## 按名字加载 TypeScript 规则

[`typescript-best-practices`](../../../skills/pstack/typescript-best-practices/SKILL.md) 把类型系统原则变成具体规则：可判别联合、边界处的 `unknown`、穷尽变体、从 schema 派生类型。它不会自动加载，所以任务碰到 `.ts` 或 `.tsx` 文件时，输入 `/typescript-best-practices`。

## 提交前先清理

[开 PR 剧本](../../../skills/pstack/poteto-mode/playbooks/opening-a-pr.md)在每次提交前对 diff 跑一遍 deslop，poteto-mode 还会对 PR 描述和提交正文应用 [`/unslop`](../../../skills/pstack/unslop/SKILL.md)。pstack 里没有自带 deslop 技能。如果你没装一个，用大白话要同样的效果：删掉复述性注释、没有依据的防御、死掉的兼容路径和无关改动。

对行文，`/unslop` 接收目标和你额外的规则：

```text
/unslop the readme changes, no emdashes
```

你会发展出自己的简写。像 `unslop that, tighten it` 这样的简短提示词，技能也能读懂意图。

## 用 `/no-comments` 剥掉注释

注释需要单独一遍，而且不能由写它们的智能体来做。作者会为自己的注释辩护，就像你会为你的辩护。所以评审前，把它们交给新鲜的眼睛：

```text
/no-comments the diff
```

[`/no-comments`](../../../skills/pstack/no-comments/SKILL.md) 派生 [Comment Sicko](../../../skills/pstack/no-comments/references/comment-sicko.md)，一个只读评审者，带一张很短的保留清单：许可证头、公共 API 的文档注释、解释代码做不到的事的链接、由你无法重塑的外部依赖强制的行为。其余全部删除。你自己代码里的惊喜不会得到这种待遇——那种注释会以重构标记的形式回来，而 `/no-comments` 会在根因上修复它接受的标记。当注释声称一个约束、"不要删除"时，技能会提议把这个声称编码成类型、测试或 lint。无论哪种，注释都会被拿掉。

分工值得记清：deslop 清理代码里的 slop，`/unslop` 清理行文里的 slop，`/no-comments` 把注释交给没写过它们的评审者。

**坑：** 清理不是可选的润色。带着复述性注释和防御性赘肉的 diff 在评审者眼里像没做完，而多余的代码正是下一个 bug 藏身的地方。如果 diff 显得注水，在提交前说 `deslop it`，别等评审指出来。

下一篇：[验证并交付](./06-verify-and-ship.md)。
