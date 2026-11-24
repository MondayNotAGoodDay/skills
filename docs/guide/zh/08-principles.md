# 用原则名称引导

pstack 把 24 条原则做成独立技能。`/poteto-mode` 在每个多步骤任务开始时读它们的索引，应用任务触发的那些，并在回复里点名每条被应用的原则以及它改变了哪个决定。

你不调用原则。你用它们的名字来引导。每个名字指向一条智能体已经读过的完整规则，所以一个短语比一段指令更精确地改道工作。

## 引导的实战

假设智能体正要在三个现有适配器上再焊一个新的：

```text
use subtract before you add. delete the obsolete adapters first, then design what's left.
```

假设它声称成功因为构建通过了：

```text
apply prove it works. run the real import flow and show me the written records.
```

假设两个并行尝试正要写同一个分支：

```text
separate before serializing shared state. give each attempt its own worktree, no locks.
```

每个短语之所以落地，是因为背后的规则足够具体。智能体仍然必须在回复里说清这条规则改变了哪个决定。一条没有决定支撑的原则引用，意味着智能体只点了名而没有应用它。

## 24 条速览

核心原则决定做多少、何时重新想设计：

- [Laziness Protocol（惰性协议）](../../../skills/pstack/principle-laziness-protocol/SKILL.md) 优先删除和能解决问题的最小改动。
- [Foundational Thinking（基础性思考）](../../../skills/pstack/principle-foundational-thinking/SKILL.md) 在写逻辑之前先选定核心数据结构。
- [Redesign from First Principles（从第一性原理重新设计）](../../../skills/pstack/principle-redesign-from-first-principles/SKILL.md) 把新需求当作第一天就存在的东西来整合。
- [Attack the Premise（攻击前提）](../../../skills/pstack/principle-attack-the-premise/SKILL.md) 写下两个以上失败修复共享的前提，在下一次修复前直接检验它。
- [Subtract Before You Add（先减后加）](../../../skills/pstack/principle-subtract-before-you-add/SKILL.md) 在往上面盖东西之前先删掉赘肉。
- [Minimize Reader Load（最小化读者负担）](../../../skills/pstack/principle-minimize-reader-load/SKILL.md) 折叠读者必须在脑子里同时持有的层次和隐藏状态。
- [Outcome-Oriented Execution（面向结果的执行）](../../../skills/pstack/principle-outcome-oriented-execution/SKILL.md) 让重写收敛到目标设计，而不是保留一次性的兼容状态。
- [Experience First（体验优先）](../../../skills/pstack/principle-experience-first/SKILL.md) 选用户的结果，而不是实现的方便。
- [Exhaust the Design Space（穷尽设计空间）](../../../skills/pstack/principle-exhaust-the-design-space/SKILL.md) 没有先例时做两个或三个相互竞争的原型。
- [Build the Lever（造杠杆）](../../../skills/pstack/principle-build-the-lever/SKILL.md) 做出能完成或证明这项工作的脚本，让评审者能重跑。当智能体反复手动做同一件事时，让它写出它希望拥有的工具或技能。如果一个脚本能每次以同样方式做一步，就用脚本，把智能体留给需要判断的决定。

架构原则决定状态、校验和兼容性住在哪里：

- [Model the Domain（为领域建模）](../../../skills/pstack/principle-model-the-domain/SKILL.md) 把反复出现的规则编码进一个结构，而不是散落的条件分支。
- [Boundary Discipline（边界纪律）](../../../skills/pstack/principle-boundary-discipline/SKILL.md) 在边界校验，在内部信任类型。
- [Type System Discipline（类型系统纪律）](../../../skills/pstack/principle-type-system-discipline/SKILL.md) 让非法状态无法被表示。
- [Make Operations Idempotent（让操作幂等）](../../../skills/pstack/principle-make-operations-idempotent/SKILL.md) 让重试收敛到同一终态。
- [Migrate Callers Then Delete Legacy APIs（先迁移调用方再删旧 API）](../../../skills/pstack/principle-migrate-callers-then-delete-legacy-apis/SKILL.md) 迁移和删除在同一波完成。
- [Separate Before Serializing Shared State（先分离再串行化共享状态）](../../../skills/pstack/principle-separate-before-serializing-shared-state/SKILL.md) 在加协调之前先移除共享。

验证原则定义什么算证据：

- [Prove It Works（证明它真的能用）](../../../skills/pstack/principle-prove-it-works/SKILL.md) 验证真实产物，不是代理指标。
- [Fix Root Causes（修根因）](../../../skills/pstack/principle-fix-root-causes/SKILL.md) 改代码之前先复现并追到原因。
- [Sequence Work into Verifiable Units（把工作切成可验证的单元）](../../../skills/pstack/principle-sequence-verifiable-units/SKILL.md) 每个小单元以一次检查收尾，再开始下一个。
- [Test Behavior, Not Implementation（测行为，不测实现）](../../../skills/pstack/principle-test-behavior-not-implementation/SKILL.md) 断言代码用户能观察到的东西，这样行为坏掉测试才失败，内部改动也不会让它失败。
- [Explain the Number（解释数字）](../../../skills/pstack/principle-explain-the-number/SKILL.md) 在任何人信任或报告一个测量数字之前，说清是什么限制了它、排除它量到了别的东西。[`/benchmark-checklist`](../../../skills/pstack/benchmark-checklist/SKILL.md) 把它变成七个从真实运行中回答的问题。

委派原则让并行工作保持清醒：

- [Guard the Context Window（守住上下文窗口）](../../../skills/pstack/principle-guard-the-context-window/SKILL.md) 把批量阅读路由给子代理，发现留在主会话。
- [Never Block on the Human（绝不为人阻塞）](../../../skills/pstack/principle-never-block-on-the-human/SKILL.md) 在可逆工作上继续推进，把结果呈上来。

还有一条元原则：

- [Encode Lessons in Structure（把教训编码进结构）](../../../skills/pstack/principle-encode-lessons-in-structure/SKILL.md) 把你重复过两次的建议变成 lint、检查或脚本。[`/correct`](../../../skills/pstack/correct/SKILL.md) 把它应用到整个仓库，见[把它变成你的](./09-make-it-yours.md#fix-the-environment-with-correct)。

不用背这份清单。现在扫一眼，然后当你发现智能体做了这里某个名字本可阻止的事时再回来查。这就是词汇量留存的方式。

下一篇：[把它变成你的](./09-make-it-yours.md)。
