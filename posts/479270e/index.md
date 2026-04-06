# Codex Best Practices


核心思想为：

1. 为每次任务提供正确的上下文
2. 将复杂任务进行拆解
3. 将持久的指导写入`AGENTS.md`
4. 将重复工作转换为`siklls`
5. 使用`MCP`与外部系统连接
6. 自动化稳定的流程

## Context and prompts

一个好的`prompt`应该包括以下4点：

- **Goal:** 你想要修改或建立什么？
- **Context:** 哪些文件、文件夹、文档、示例或错误对这项任务有帮助？推荐通过`@`这些文件来提供上下文。
- **Constraints:** **本次任务**应遵循哪些标准、架构、安全要求或惯例？
- **Done when:** **本次任务**完成的标准。比如测试通过、行为改变，或者某个漏洞被修复。

此外，注意选择合适的推理能力：

- **低：** 适用于范围明确且要求快速响应的任务
- **中和高：** 适用于复杂的任务
- **超高：** 适用于长时间、复杂、推理繁重的任务

## Plan first for difficult tasks

对于复杂、模糊的任务，应该在进行编码前先做`plan`

可以采用以下方式来制作`plan`：

 - **使用计划模式：** 输入 `/plan` 或按下 `shift &#43; tab` 即可切换为 `plan` 模式。（这是最简单有效的方式，强力推荐）
 - **让Codex主动询问：** 让codex主动对任务不清楚，描述模糊的地方对你进行提问，把你模糊的想法变成具体的东西，然后才开始写代码
 - **使用 `PLANS.md`：** 对于特别特别复杂的任务，可以按照官方提供的[模板](https://developers.openai.com/cookbook/articles/codex_exec_plans)编写 `PLANS.md`

## Make guidance reusable with `AGENTS.md`

`AGENTS.md` 就是面向 agents 的 README，它可以让我们不用重复写**相同的提示**，让anget行为更稳定。Codex每次执行任务前会**自动读取**该文件，作为系统级指导。

一个好的 `AGENTS.md` 应该包括以下内容：

- 仓库结构和重要目录
- 构建、测试、运行命令
- 代码规范（使用什么标准，函数和变量命名规则等）
- 工作流程（如何提交PR，commit规范，review要求）
- 特殊注意事项

刚开始的 `AGENTS.md` 应该简洁准确，然后再慢慢将 agent 反复犯的错制作为规则添加进去。需要注意的是 `AGENTS.md` 中的规则尽量不要超过 **20** 条（规则越多，规则之间发生冲突的可能性旧越大，且浪费tokens）。此外，写入的每条规则应该 **简洁准确** （明确路径，明确命令，明确术语）且是**长期需要遵守**的。

一定要记住 `AGENTS.md` 是 **执行约束**，不是详细文档！！！

### The &#34;hierarchical system&#34; of AGENTS.md

Codex 会按层级**逐层发现并加载 AGENTS.md**，加载顺序为：
全局层（`~/.codex/AGENTS.md`）➜ 项目根目录（`repo/AGENTS.md`）➜ 子目录（路径上所有 `AGENTS.md`）➜ 当前文件所在目录的 `AGENTS.md`（最近）

这些 `AGENTS.md` 中的规则是**叠加**的！！！所以，**不要编写互斥的规则**！！！当存在互斥规则时，最底层的规则优先级大（即离文件所在目录越近的`AGENTS.md`中的规则优先级越大, 但是 `prompt`中的约束是**最大**的）。

一个核心原则就是：
&gt; **越上层越通用，越下层越具体，每层只写该层能决定的事情**

## Turn repeatable work into Skills

官方推荐：当一个任务满足以下3个条件时就应该抽象成 `Skill`：

- 可重复
- 有固定步骤
- 可以标准化输入输出

`SKILL`目录的命名应该简短明确，单词之间用`-`连接，比如：`code-review`, `benchmark-analysis`等。`SKILL.md`应该放在`.agents/skills/`下（这样codex就能自动发现了），比如：

```bash
.agents/
└── skills/
    ├── code-review/
    └── benchmark-analysis/
```

每个`SKILL`目录的具体结构为：

```bash
skills/&lt;skill-name&gt;/
├── SKILL.md        # 必须
├── scripts/        # 可选（脚本）
├── references/     # 可选（文档）
└── examples/       # 可选（示例）
```

`SKILL.md`推荐结构：

```bash
---
name: &lt;skill-name&gt;
description: &lt;What this skill does and when to use it.&gt;
---

# When to use

Use this skill when:
- &lt;condition 1&gt;
- &lt;condition 2&gt;

# Inputs

Required inputs:
- &lt;input 1&gt;
- &lt;input 2&gt;

# Steps

1. Understand the request.
2. Inspect the relevant context.
3. Perform the task.
4. Validate the result.
5. Summarize clearly.

# Output format

- Summary:
- Details:
- Validation:
- Risks / Notes:

# Constraints

- Stay within scope.
- Follow project rules.
- State assumptions clearly.
```

调用`skill`有两种方式：

1. **显示调用：** 在 prompt 中指定（使用 `skill: code-review`）
2. **隐式调用：** Codex 会根据任务描述和Skill 描述自动选择合适的 `Skill`（该方式更常见）

需要记住的是：**一个 `Skill` 只做一件事** ！！！

## Configure Codex for consistency

在实际开发中，Codex 最大的问题往往不是能力，而是**结果不稳定**：

- 同样的任务，不同结果
- 同一项目，不同行为
- 不同环境，输出差异很大

官方给出的确保一致性的核心思路是：
&gt; **“固定环境 &#43; 固定规则 &#43; 固定流程”**

`AGENTS.md` 用于固定规则，`SKILL.md` 用于固定流程，`config.toml` 用于固定环境。

`config.toml`控制：

- 使用哪个模型
- 推理强度
- 是否允许执行命令
- 是否需要用户确认
- sandbox 权限范围

`config.toml`的具体样例，可以参考[官方模板](https://developers.openai.com/codex/config-sample)。此外，Codex也支持分层配置：

```bash
~/.codex/config.toml        ← 全局默认
repo/.codex/config.toml     ← 项目配置
CLI 参数                    ← 临时覆盖
```

优先级：

```bash
CLI &gt; 项目配置 &gt; 全局配置
```

## 总结

以上内容只是Codex最佳实践的部分内容，由于我对其他内容（MCP，automations）目前还没有使用的需求，就不在文章中写了，有需要者可以自行查阅[官方文档](https://developers.openai.com/codex/learn/best-practices)。

## 参考资料

1. https://developers.openai.com/codex/learn/best-practices
2. https://developers.openai.com/codex/guides/agents-md#how-codex-discovers-guidance


---

> 作者: [zyz](https://github.com/YouZhiZheng)  
> URL: https://YouZhiZheng.github.io/posts/479270e/  

