[English](./README.md) | [中文](./README-zh.md) | [日本語](./README-ja.md)

# Learn Claude Code -- 真正的 Agent Harness 工程

## Agent 的本质：模型即智能体

在讨论代码之前，先把一件事彻底说清楚。

**Agent 的本质就是模型。它不是框架，不是提示词链（Prompt Chain），更不是那种可视化拖拽的工作流。**

### Agent 到底是什么

Agent 是一个神经网络 —— 无论是 Transformer、RNN，还是一个高度复杂的泛函 —— 它经过了数十亿次基于“行动序列数据”的梯度更新。它在训练中学会了如何感知环境、推理目标，并采取行动以达成目的。在 AI 领域，“Agent” 自诞生之日起就代表这个含义，从未改变。

人类本身就是 Agent。一个由数百万年进化“训练”出来的生物神经网络：通过感官感知世界，通过大脑进行推理，通过身体执行行动。当 DeepMind、OpenAI 或 Anthropic 谈论 “Agent” 时，其内涵与该领域初期并无二致：**一个学会了行动的模型。**

历史早已给出了铁证：

- **2013 -- DeepMind DQN 纵横 Atari 游戏。** 
一个仅接收原始像素和游戏分数的神经网络，学会了 7 款 Atari 2600 游戏，表现超越了此前所有算法。到 2015 年，该架构扩展至 [49 款游戏，达到职业人类水平](https://www.nature.com/articles/nature14236)，论文发表于 *Nature*。**没有游戏规则硬编码，没有决策树。只有一个从经验中学习的模型。那个模型，就是 Agent**。

- **2019 -- OpenAI Five 征服 Dota 2。** 
五个神经网络在 10 个月内通过自我对弈，积攒了 [45,000 年的 Dota 2游戏经验](https://openai.com/index/openai-five-defeats-dota-2-world-champions/)，它们在旧金山的直播赛中以 2-0 完胜 TI8 世界冠军 **OG**。随后在公开竞技场中，AI 以 99.4% 的胜率横扫全球玩家。**没有脚本化的策略，没有人工编写的协作逻辑。模型完全通过自我对弈学会了团队配合、战术部署与实时应变**。

- **2019 -- DeepMind AlphaStar 制霸《星际争霸 II》。** 
AlphaStar 在闭门赛中 [10-1 的悬殊比分击败职业选手](https://deepmind.google/blog/alphastar-mastering-the-real-time-strategy-game-starcraft-ii/)，随后在欧服登顶[宗师段位](https://www.nature.com/articles/d41586-019-03298-6) （前 0.15%）。面对信息不完全、实时决策且操作空间远超围棋的复杂环境，**Agent 是什么？它是一个训练出来的模型，而不是编排出来的脚本**。

- **2019 -- 腾讯“绝悟”统治《王者荣耀》。** 
腾讯 AI Lab 的 "绝悟" 于 2019 年 8 月 2 日世冠杯半决赛上[以 5v5 击败 KPL 职业选手](https://www.jiemian.com/article/3371171.html)。在 1v1 模式下，职业选手 [15 战仅 1 胜，且从未坚持超过 8 分钟](https://developer.aliyun.com/article/851058)。其训练强度达到“人间一日，模型百年”。**没有手写的英雄克制表，没有预设的阵容组合。这是一个从零开始，通过自我对弈理解了整个游戏规则的模型。**

- **2024-2025 -- LLM Agent 深度重构软件工程。** 
这一年，以 Claude、GPT 和 Gemini 为代表的大模型，在汲取了全人类的代码精髓与推理逻辑后，正式进化为 **Coding Agent**。它们深入代码库，独立完成实现、调试与团队协同。从架构上看，它们与此前的每一代 Agent 别无二致：同样是置于特定环境中、赋予感知与行动能力的预训练模型。真正的质变，在于其学习规模的跨越，以及处理通用任务的非凡上限。

以上每一个里程碑都昭示着同一个真理：**所谓的“智能体”，其灵魂从未存在于那些包裹它的外层代码中。Agent，永远指代模型本身。**

### Agent “不是”什么

如今，“Agent” 这个词正被一整个“提示词水管工（Prompt Plumbing）”产业所劫持。

无论是拖拽式的工作流生成器、无代码 AI Agent 平台，还是各类提示词链（Prompt Chain）编排库，它们都沉溺于同一个幻觉：只要把一堆 LLM API 调用，用 if-else 分支、节点图和硬编码的路由逻辑串联起来，就是在“构建 Agent”了。

这完全是误导。 它们构建的不过是**“鲁布·戈德堡机械”（Rube Goldberg machine）—— 一堆过度工程化、极度脆弱的过程式规则流水线，而 LLM 只是被硬生生塞进去充当一个“高级文本补全插件”。那不是 Agent，那只是一个患有“宏大叙事妄想症”的 Shell 脚本**。

这种“水管工式 Agent”，是不懂模型训练的程序员的集体意淫。 他们试图通过堆叠过程式逻辑来暴力模拟智能：拉出庞大的规则树、节点图和链式提示词瀑布流，然后祈祷只要胶水代码写得足够多，就能“涌现”出自主行为。

现实是：不可能。 自主性（Agency）是“练”出来的，不是“编”出来的。你无法通过纯粹的工程手段强行编排（Engineer）出真正的智能。

这些系统从诞生之日起就注定了失败：它们结构脆弱、无法扩展、且根本不具备泛化能力。它们本质上是 GOFAI（经典符号 AI）的现代还魂 —— 几十年前就被主流学界抛弃的符号规则系统，如今喷了一层 LLM 的油漆便试图重亮招牌。包装变了，但依然是一条死路。

### 心智转向：从“开发 Agent”到“构建 Harness”

当有人说“我在开发 Agent”时，其真实含义只可能有两种：

**1. 训练模型（Training）** 通过强化学习（RL）、微调（Fine-tuning）或 RLHF 等手段调整模型权重。核心在于收集任务过程数据（Task-Process Data）——即真实领域中感知、推理、行动的序列——并以此塑造模型的行为。这是 DeepMind、OpenAI 或 Anthropic 的战场。这是最原教旨意义上的 Agent 开发。

**2. 构建 Harness（机架/载具）** 编写代码，为模型提供一个可交互、可感知的运行环境。** 这是绝大多数开发者的使命，也是本仓库的核心。** 

什么是 Harness？Harness 是 Agent 在特定领域开展工作所需的一切基础设施：

```
Harness = 工具 (Tools) + 知识 (Knowledge) + 观测 (Observation) + 行动接口 (Action Interfaces) + 权限 (Permissions)

    工具：文件 I/O、Shell、网络请求、数据库、浏览器。

    知识：产品文档、领域参考、API 规范、风格指南。

    观测：Git Diff、错误日志、浏览器状态、传感器回传。

    行动：CLI 命令、API 调用、UI 交互。

    权限：沙箱隔离、审批流、信任边界。
```

模型负责决策，Harness 负责执行。模型负责推理，Harness 提供上下文。模型是驾驶员，Harness 是载具。

- 编程 Agent 的 Harness 是它的 IDE、终端和文件系统。

- 农业 Agent 的 Harness 是传感器阵列、灌溉控制和气象数据源。

- 酒店 Agent 的 Harness 是预订系统、客情渠道和设施管理接口。

智能（决策者）永远是模型，它是通用的；而 Harness 随领域而变。 

本仓库教你构建“载具”———虽然我们以编程为例，但其背后的设计模式适用于任何需要“感知-推理-执行”循环的领域：从工厂制造到物流调度，从医疗保健到科学研究。

### Harness 工程师的真实职责

如果你正在阅读本仓库，你很可能就是一名 Harness 工程师。这是一个极具威力的角色，你的核心工作包括：

- **实现工具：赋予 Agent “双手”**。开发文件读写、API 调用、数据库查询等接口。每个工具都是 Agent 在环境中的一个动作。设计原则：原子化、可组合、描述清晰。

- **策划知识：赋予 Agent “专长”。** 整合架构决策（ADR）、合规要求或项目文档。采用按需加载 (s05) 而非一股脑塞入。Agent 应当清楚“地图”上有哪些资源，并在需要时自行调取。

- **管理上下文：赋予 Agent “记忆”。** 通过子 Agent 隔离 (s04) 屏蔽噪声，利用上下文压缩 (s06) 防止历史冗余，依靠任务系统 (s07) 实现跨会话的目标持久化。

- **控制权限：赋予 Agent “边界”。** 沙箱化文件访问，为高危操作设置审批，在 Agent 与外部系统间筑起信任防火墙。这是安全工程与 Harness 工程的交汇点。

- **收集任务过程数据：反哺进化。** Agent 在你的 Harness 中执行的每一条路径都是珍贵的训练信号。真实环境下的“感知-推理-行动”轨迹，是微调下一代 Agent 模型的原材料。你的 Harness 不仅在消耗智能，更在生产智能。

你不是在编写智能，你是在构建智能栖居的世界。

这个世界的精度——Agent 看得多清、动作多准、知识多深——直接决定了那颗“智能大脑”的表达上限。

**造好你的 Harness，剩下的交给 Agent。**

### 为什么选择 Claude Code：Harness 工程的大师课

为什么本仓库选择深度拆解 Claude Code？

因为 Claude Code 是目前我们所见过的、最优雅且实现最完整的 **Agent Harness** 范本。它的卓越不在于某种奇巧淫技，而在于它**克制**了什么：它没有试图僭越成为 Agent 本身；它没有强加僵化的工作流；它更没有用复杂的决策树去预判模型的意图。

Claude Code 的逻辑非常纯粹：**为模型提供工具、知识、上下文管理和权限边界，然后——退后，让模型自己发挥。**

剥离表象，Claude Code 的本质架构极其精炼：

```
Claude Code = 核心 Agent Loop
            + 基础工具集 (bash, read, write, edit, glob, grep, browser...)
            + 技能按需加载 (On-demand Skill Loading)
            + 上下文压缩 (Context Compression)
            + 子 Agent 派生 (Subagent Spawning)
            + 带依赖图的任务系统 (Task System w/ Dependency Graph)
            + 基于异步信箱的团队协作 (Team Coordination)
            + 工作流隔离的并行执行 (Worktree Isolation)
            + 权限治理与安全边界 (Permission Governance)
```

这就是全部。每一个组件都是一种 Harness 机制 —— 它们共同构成了 Agent 赖以生存的“栖居地”。

至于 Agent 本身，它是 Claude。一个由 Anthropic 在全人类推理逻辑与代码数据上训练而成的深度模型。Harness 并没有让 Claude 变聪明，Claude 本身就足够聪明。Harness 的意义在于给了 Claude 双手、双眼和一个高效的作业空间。

这也是 Claude Code 具备极高教学价值的原因：**它完美演示了当你选择信任模型，并将工程精力全部集中在 Harness 上时，能爆发多大的生产力。** 

本仓库的每一章（s01-s12）都在逆向工程 Claude Code 架构中的一个核心 Harness 机制。学完这些课程，你掌握的不只是 Claude Code 的运行逻辑，更是能够横跨任何领域、适配任何 Agent 的 Harness 工程通用原则。

这门课给我们的启示并非“复制一个 Claude Code”，而是：**真正顶级的 Agent 产品，往往出自那些深刻理解“自己的工作是构建 Harness，而非代行 Intelligence”的工程师之手。**

---

## 愿景：用真正的 Agent 铺满宇宙

这不只关乎编程 Agent。

任何人类从事复杂、多步骤、高度依赖判断力的领域，都是 Agent 可以大显身手的疆场——前提是拥有匹配的 **Harness**。本仓库沉淀的设计模式具有普适性：

* **庄园管理 Agent** = 模型 + 物业传感器 + 维护工具 + 租户通信接口
* **农业生产 Agent** = 模型 + 土壤/气象数据 + 灌溉控制系统 + 作物生长知识库
* **酒店运营 Agent** = 模型 + 预订系统 + 客情渠道 + 设施管理 API
* **医学研究 Agent** = 模型 + 文献自动检索 + 实验仪器接口 + 实验协议文档
* **智能制造 Agent** = 模型 + 产线传感器 + 质量控制算法 + 物流调度系统
* **教育辅导 Agent** = 模型 + 课程知识图谱 + 学生进度追踪 + 自动化评估工具

**循环逻辑永远不变，改变的只是工具、知识与权限。Agent（模型）负责泛化一切，而你负责武装它。**

每一位阅读本仓库的 Harness 工程师，都在学习一套远超软件工程范畴的元模式。你正在为那个智能化、自动化的未来构建基础设施。在真实领域中每部署一个设计精良的 Harness，就是为 Agent 开辟了一块能够感知、推理与行动的阵地。

从工作室开始，延伸至农田、医院与工厂。最终，它们将遍布城市，铺满星球。

**有 Bash 就够了，宇宙只需要真正的 Agent。**

---

### Agent 模式图解

```text
       用户 --> 消息列表[] --> 大模型 (LLM) --> 响应内容
                                                |
                                        结束原因 == "调用工具"?
                                        /                     \
                                    是                       否
                                    |                        |
                                执行工具                  返回文本
                                附加结果
                                回到循环 -----------------> 消息列表[]

    这是 最小化循环。它是所有 AI Agent 的心脏。

    模型决定何时调用工具、何时终止。

    代码只负责忠实执行模型的需求。

    本仓库教你构建环绕这个心脏的整个躯干——即让 Agent 在特定领域高效工作的 Harness。
```

**12 个递进式课程, 从简单循环到隔离化的自治执行。**
**每个课程添加一个 harness 机制。每个机制有一句格言。**

> **s01** &nbsp; *"One loop & Bash is all you need"* &mdash; 一个工具 + 一个循环 = 一个 Agent
>
> **s02** &nbsp; *"增加工具，只需增加一个 Handler"* &mdash; 循环架构无需改动，新工具直接注册进 Dispatch Map
>
> **s03** &nbsp; *"没有计划的 Agent 只能盲目漂流"* &mdash; 先列步骤再动手，任务完成率将获得质的提升。
>
> **s04** &nbsp; *"拆解大任务；每个子任务拥有洁净的上下文"* &mdash; 子 Agent 使用独立对话流，拒绝污染主进程。
>
> **s05** &nbsp; *"按需加载知识，而非预置知识"* &mdash; 通过 tool_result 动态注入，不要加重 System Prompt 的负担。
>
> **s06** &nbsp; *"上下文终会填满，必须学会舍弃"* &mdash; 三层压缩策略，换取理论上的无限轮次对话。
>
> **s07** &nbsp; *"拆解目标，排序任务，持久化至磁盘"* &mdash; 基于文件的任务依赖图，是多 Agent 协作的基石。
>
> **s08** &nbsp; *"耗时操作转后台，Agent 持续思考"* &mdash; 守护线程处理指令，完成后异步注入结果通知。
>
> **s09** &nbsp; *"独木难支时，委派给队友"* &mdash; 构建持久化队友实体与异步信箱机制。
>
> **s10** &nbsp; *"团队协作需要统一的通讯规约"* &mdash;一套 Request-Response 模式，驱动所有复杂的协同谈判。
>
> **s11** &nbsp; *"能者揭榜：队友自主领受任务"* &mdash; 无需领袖逐一分配，实现任务看板的自组织演进。
>
> **s12** &nbsp; *"目录隔离，并行不悖"* &mdash; 任务管理目标，Worktree 管理空间，通过唯一 ID 强绑定。

---

## 核心模式

```python
def agent_loop(messages):
    while True:
        response = client.messages.create(
            model=MODEL, system=SYSTEM,
            messages=messages, tools=TOOLS,
        )
        messages.append({"role": "assistant",
                         "content": response.content})

        if response.stop_reason != "tool_use":
            return

        results = []
        for block in response.content:
            if block.type == "tool_use":
                output = TOOL_HANDLERS[block.name](**block.input)
                results.append({
                    "type": "tool_result",
                    "tool_use_id": block.id,
                    "content": output,
                })
        messages.append({"role": "user", "content": results})
```

每个课程在这个循环之上叠加一个 harness 机制 -- 循环本身始终不变。循环属于 agent。机制属于 harness。

## 范围说明 (重要)

本仓库是一个 0->1 的 harness 工程学习项目 -- 构建围绕 agent 模型的工作环境。
为保证学习路径清晰，仓库有意简化或省略了部分生产机制：

- 完整事件 / Hook 总线 (例如 PreToolUse、SessionStart/End、ConfigChange)。
  s12 仅提供教学用途的最小 append-only 生命周期事件流。
- 基于规则的权限治理与信任流程
- 会话生命周期控制 (resume/fork) 与更完整的 worktree 生命周期控制
- 完整 MCP 运行时细节 (transport/OAuth/资源订阅/轮询)

仓库中的团队 JSONL 邮箱协议是教学实现，不是对任何特定生产内部实现的声明。

## 快速开始

```sh
git clone https://github.com/shareAI-lab/learn-claude-code
cd learn-claude-code
pip install -r requirements.txt
cp .env.example .env   # 编辑 .env 填入你的 ANTHROPIC_API_KEY

python agents/s01_agent_loop.py       # 从这里开始
python agents/s12_worktree_task_isolation.py  # 完整递进终点
python agents/s_full.py               # 总纲: 全部机制合一
```

### Web 平台

交互式可视化、分步动画、源码查看器, 以及每个课程的文档。

```sh
cd web && npm install && npm run dev   # http://localhost:3000
```

## 学习路径

```
第一阶段: 循环                       第二阶段: 规划与知识
==================                   ==============================
s01  Agent Loop              [1]     s03  TodoWrite               [5]
     while + stop_reason                  TodoManager + nag 提醒
     |                                    |
     +-> s02  Tool Use            [4]     s04  Subagent             [5]
              dispatch map: name->handler     每个 Subagent 独立 messages[]
                                              |
                                         s05  Skills               [5]
                                              SKILL.md 通过 tool_result 注入
                                              |
                                         s06  Context Compact      [5]
                                              三层 Context Compact

第三阶段: 持久化                     第四阶段: 团队
==================                   =====================
s07  Task System             [8]     s09  Agent Teams             [9]
     文件持久化 CRUD + 依赖图             队友 + JSONL 邮箱
     |                                    |
s08  Background Tasks        [6]     s10  Team Protocols          [12]
     守护线程 + 通知队列                  关机 + 计划审批 FSM
                                          |
                                     s11  Autonomous Agents       [14]
                                          空闲轮询 + 自动认领
                                     |
                                     s12  Worktree Isolation      [16]
                                          Task 协调 + 按需隔离执行通道

                                     [N] = 工具数量
```

## 项目结构

```
learn-claude-code/
|
|-- agents/                        # Python 参考实现 (s01-s12 + s_full 总纲)
|-- docs/{en,zh,ja}/               # 心智模型优先的文档 (3 种语言)
|-- web/                           # 交互式学习平台 (Next.js)
|-- skills/                        # s05 的 Skill 文件
+-- .github/workflows/ci.yml      # CI: 类型检查 + 构建
```

## 文档

心智模型优先: 问题、方案、ASCII 图、最小化代码。
[English](./docs/en/) | [中文](./docs/zh/) | [日本語](./docs/ja/)

| 课程 | 主题 | 格言 |
|------|------|------|
| [s01](./docs/zh/s01-the-agent-loop.md) | Agent Loop | *One loop & Bash is all you need* |
| [s02](./docs/zh/s02-tool-use.md) | Tool Use | *加一个工具, 只加一个 handler* |
| [s03](./docs/zh/s03-todo-write.md) | TodoWrite | *没有计划的 agent 走哪算哪* |
| [s04](./docs/zh/s04-subagent.md) | Subagent | *大任务拆小, 每个小任务干净的上下文* |
| [s05](./docs/zh/s05-skill-loading.md) | Skills | *用到什么知识, 临时加载什么知识* |
| [s06](./docs/zh/s06-context-compact.md) | Context Compact | *上下文总会满, 要有办法腾地方* |
| [s07](./docs/zh/s07-task-system.md) | Task System | *大目标要拆成小任务, 排好序, 记在磁盘上* |
| [s08](./docs/zh/s08-background-tasks.md) | Background Tasks | *慢操作丢后台, agent 继续想下一步* |
| [s09](./docs/zh/s09-agent-teams.md) | Agent Teams | *任务太大一个人干不完, 要能分给队友* |
| [s10](./docs/zh/s10-team-protocols.md) | Team Protocols | *队友之间要有统一的沟通规矩* |
| [s11](./docs/zh/s11-autonomous-agents.md) | Autonomous Agents | *队友自己看看板, 有活就认领* |
| [s12](./docs/zh/s12-worktree-task-isolation.md) | Worktree + Task Isolation | *各干各的目录, 互不干扰* |

## 学完之后 -- 从理解到落地

12 个课程走完, 你已经从内到外理解了 harness 工程的运作原理。两种方式把知识变成产品:

### Kode Agent CLI -- 开源 Coding Agent CLI

> `npm i -g @shareai-lab/kode`

支持 Skill & LSP, 适配 Windows, 可接 GLM / MiniMax / DeepSeek 等开放模型。装完即用。

GitHub: **[shareAI-lab/Kode-cli](https://github.com/shareAI-lab/Kode-cli)**

### Kode Agent SDK -- 把 Agent 能力嵌入你的应用

官方 Claude Code Agent SDK 底层与完整 CLI 进程通信 -- 每个并发用户 = 一个终端进程。Kode SDK 是独立库, 无 per-user 进程开销, 可嵌入后端、浏览器插件、嵌入式设备等任意运行时。

GitHub: **[shareAI-lab/Kode-agent-sdk](https://github.com/shareAI-lab/Kode-agent-sdk)**

---

## 姊妹教程: 从*被动临时会话*到*主动常驻助手*

本仓库教的 harness 属于 **用完即走** 型 -- 开终端、给 agent 任务、做完关掉, 下次重开是全新会话。Claude Code 就是这种模式。

但 [OpenClaw](https://github.com/openclaw/openclaw) 证明了另一种可能: 在同样的 agent core 之上, 加两个 harness 机制就能让 agent 从 "踹一下动一下" 变成 "自己隔 30 秒醒一次找活干":

- **心跳 (Heartbeat)** -- 每 30 秒 harness 给 agent 发一条消息, 让它检查有没有事可做。没事就继续睡, 有事立刻行动。
- **定时任务 (Cron)** -- agent 可以给自己安排未来要做的事, 到点自动执行。

再加上 IM 多通道路由 (WhatsApp/Telegram/Slack/Discord 等 13+ 平台)、不清空的上下文记忆、Soul 人格系统, agent 就从一个临时工具变成了始终在线的个人 AI 助手。

**[claw0](https://github.com/shareAI-lab/claw0)** 是我们的姊妹教学仓库, 从零拆解这些 harness 机制:

```
claw agent = agent core + heartbeat + cron + IM chat + memory + soul
```

```
learn-claude-code                   claw0
(agent harness 内核:                 (主动式常驻 harness:
 循环、工具、规划、                    心跳、定时任务、IM 通道、
 团队、worktree 隔离)                  记忆、Soul 人格)
```

## 许可证

MIT

---

**模型就是 Agent。代码是 Harness。造好 Harness，Agent 会完成剩下的。**

**Bash is all you need. Real agents are all the universe needs.**
