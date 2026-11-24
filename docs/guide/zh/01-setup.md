# 安装 pstack

本页你将安装 pstack、让它适配你的运行环境（harness），并跑完第一个任务。安装只需一条命令加一小段对话。

## 安装技能

在你的项目里，或者任何地方做用户级安装，运行：

```bash
npx skills add MondayNotAGoodDay/skills
```

这会安装仓库里的全部技能。要只装单个技能或查看可用列表，见 [README](../../../README.md)。

## 让 pstack 适配你的运行环境

运行：

```text
/setup-pstack
```

pstack 技能从不点名运行环境的工具或模型。它们只说"spawn 这个角色""问用户"。[`/setup-pstack`](../../../skills/pstack/setup-pstack/SKILL.md) 把这些话翻译成你运行环境里的真实调用，按以下顺序进行：

1. 从会话中的工具 schema 检测运行环境。
2. 弄清运行环境如何派生子代理（subagent）、提问、读取本会话记录（transcript）以及循环。
3. 询问推理预算（reasoning budget），然后列出你可用的模型，为每个角色选一个（代码委派、行文、判断、评审团）。它会把表格给你看并询问要改什么。开箱默认：代码、行文、判断偏好 Opus 5.5 的 xhigh；评审团则在 Fable 5.2、Opus 5.5、Gemini 4 Neon 上各占一席。评审团之外，Fable 5.2 只在你为某个任务点名它、或同一任务书的两次 Opus 5.5 运行未通过检查时才启用。当运行环境不提供其中某个模型时，setup 会挑选该系列最接近的模型。
4. 如果运行环境是把模型绑定在智能体定义上、而不是每次 spawn 时传入，setup 会生成它需要的智能体文件。
5. 把 pstack 配置（profile）写入你的常驻指令。
6. 冒烟测试这套配方：派生一个只读子代理、派生一个会写文件的后台子代理，并把会话记录读回来。

profile 是一小段纯文本，45 行以内，每个会话都会被你的运行环境加载。它记录预算、在你运行环境中 spawn/ask/读历史/循环的确切方式（不支持时标 `unsupported` 并给出回退方案），以及每行一个角色对应的模型名。每个 pstack 技能都会读它。

默认值与 `large` 预算相同。代码、行文、判断角色跑在 Opus 5.5 的 `xhigh` 推理档。评审团上，Fable 和 Opus 席位跑 `high`，Gemini 4 Neon 席位跑 `xhigh`。Swarm 工作节点跑 `high`。没有任何角色超过 `xhigh`。`medium` 把每档降一级，`small` 降两级，两者都更省 token。预算标签指的是 `xhigh` 默认落点，也就是 Gemini 4 Neon 评审席位的档位。当运行环境无法控制 effort 时，预算只影响模型的选择。

你只需要覆盖你在意的部分。删掉某个角色的行，它就变成 `inherit`。重跑 `/setup-pstack` 会保留模型与默认值不同的角色，其余按当前默认值重建。profile 里带一行 `# defaults: 3`。没有这行的 profile 来自旧版 setup，它下次运行会替换它认得出的旧默认值、告诉你替换了哪些，并保留你其他的选择。评审团里保存的 Gemini 4 Carbon 席位、或 Gemini 4 Neon 的 `high` 档位，即使 profile 已有这行，也算旧默认值。下次运行会把该席位移到 Gemini 4 Neon 的 `xhigh` 并告知你。其他档位的 Gemini 4 Neon 席位保持不变。要丢弃它不认识的固定值，先删掉那个角色的行或受管代码块。运行环境升级后，或冒烟测试失败时，再跑一次。

你可能在想：如果你只有一个模型怎么办？那样每个角色都是 `inherit`，评审团只剩一个席位，评审就不具备模型多样性。`inherit` 表示子代理跑在你父会话的模型上、不做覆盖。对于评审团角色，该值是一个列表，每个条目派生一个子代理，所以列表长度决定评审团规模。setup 还会配置 `swarm workers`，即 `/swarm` 的默认模型，除非某次 race 为每条臂点名了模型。

## 验证技能：接受还是拒绝

setup 结束时，`/setup-pstack` 会找一种在你的项目里证明应用行为的方式：一个 `verify-*` 技能，或者现有的运行环境机制。两者都没有时，它会提议用 [`/create-verification-skill`](../../../skills/pstack/create-verification-skill/SKILL.md) 生成一个。

答应的话，它会写出 `.agents/skills/verify-<app>/`，一个项目本地技能，教智能体像用户一样驱动你的应用。交付前它会先实际验证一次这个技能可用。拒绝的话 setup 就继续。你也可以随时自己运行 `/create-verification-skill`。[验证与交付](./06-verify-and-ship.md#create-a-project-verification-skill)有详细介绍。

如果你刚接触 pstack，建议答应。能自我验证的智能体会一直干到检查通过；不能验证的智能体只能把结果交回给你手动检查。在这份指南里，验证技能的回报是最高的。

接着 setup 会提议用 [`/create-formal-verification-skill`](../../../skills/pstack/create-formal-verification-skill/SKILL.md) 生成一个形式化验证技能，除非项目里已有一个。测试只检查有人想到要写的用例。形式化验证技能会在声明的范围内检查代码的一个关键性质，比如"一个任务绝不会同时跑两次"。当你的代码涉及并发、重试、状态机或解析器——即 bug 代价高、而测试很少触及出错用例的场景——就答应。否则拒绝，以后情况变了再跑。[验证与交付](./06-verify-and-ship.md#prove-a-property-with-a-formal-verification-skill)有介绍。

setup 之后，开一个新会话。profile 对新会话生效。

## 控制成本

pstack 在子代理和评审团上会多花 token，这是严谨的代价。想少花：

- 重跑 `/setup-pstack`，选更小的推理预算或更便宜的模型。主会话用强模型、代码角色用便宜快速的模型是不错的搭配。
- 把某个角色设为 `inherit`，让它跑在会话自己的模型上。
- 缩短评审团列表，每个条目都会派生一个子代理。
- 把 `/poteto-mode` 留给需要严谨性的工作。小而显然的改动不需要它。

## 跑你的第一个任务

挑一个真实但小的事情，用你跟同事描述的方式说出来：

```text
/poteto-mode add a --json flag to this command. text output stays byte-identical. verify both.
```

盯着待办清单看。最前面的几项就是匹配到的剧本步骤——这个提示词匹配的是 Feature 剧本。

接下来你可以正常追问。想让 `/poteto-mode` 在整个会话里保持开启，就在你的运行环境支持每轮固定技能的情况下把它 pin 住。否则每个新任务都以 `/poteto-mode` 开头。只调用一次的技能只附着在一条消息上，随着对话推进会淡出。

下一篇：[通过 `/poteto-mode` 路由工作](./02-poteto-mode.md)。
