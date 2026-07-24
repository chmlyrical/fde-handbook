# FDE W4D2 评测题 · LangGraph

> 配套手册：《FDE-W4D2-LangGraph-学习手册.html》
> 主题：State / Node / Edge / Conditional Edge / Tool Node / Checkpointer / Interrupt / Command / HITL
> 场景主线：特药理赔 Agent（36 岁候选人，Java + 大数据 + 医疗保险背景）
> 题型：L1 基础 / L2 进阶 / L3 深度 / L4 场景，共 13 题
> 计分：每题满分 10 分；参考答案折叠在 `<details>` 中，建议先自答再看。

---

## L1 基础（概念识别，每题 10 分）

### Q1. LangGraph 里 State、Node、Edge 分别是什么？它们如何配合？
- **考察点**：图的三大基本构件。
- **评分维度**：
  - State 定义与 reducer（3 分）
  - Node 是 (state)→更新 函数（3 分）
  - Edge 固定流转、三者 compile 成图（4 分）

<details>
<summary>参考答案</summary>

- **State**：贯穿整张图的共享数据，节点返回更新、按 reducer 合并（messages 用 append，结论用 replace）。
- **Node**：一个 `(state) -> 部分更新` 的函数，读 State、做计算、返回要合并的字段。
- **Edge**：节点间的固定流转（START→node→END）。
三者通过 `StateGraph(State)` 加 node、加 edge、compile 成一张可运行图。

</details>

### Q2. 普通 Edge 和 Conditional Edge（条件边）有什么区别？
- **考察点**：理解"模型如何参与控制流"。
- **评分维度**：
  - 普通边固定走向（3 分）
  - 条件边按 State 动态选分支（4 分）
  - 条件边对应 Agentic Workflow 的决策点（3 分）

<details>
<summary>参考答案</summary>

- **普通 Edge**：固定从一个节点走到另一个（Workflow 的确定性部分）。
- **Conditional Edge**：提供一个路由函数读 State，返回分支名，动态选下一个节点。模型输出（如 tool_calls、risk_level）作为 State 的一部分，间接驱动分支选择——这正是"模型参与控制流"、Agentic Workflow 的体现。

</details>

### Q3. ToolNode 自动帮你做了哪些事？
- **考察点**：工具如何在图里被调用与回填。
- **评分维度**：
  - 解析 tool_calls（3 分）
  - 逐个执行工具（3 分）
  - 结果 append 回 messages（4 分）

<details>
<summary>参考答案</summary>

ToolNode 自动：① 读取 State 里最后一条 assistant 消息的 `tool_calls`；② 逐个分发到对应工具函数执行；③ 把每个结果包装成 `tool` 消息 append 回 messages。它把 Day1 手写的"解析→执行→回填"封装好，你的 Java 微服务包成 `@tool` 即可挂载。

</details>

---

## L2 进阶（机制理解，每题 10 分）

### Q4. 描述用 StateGraph 组成一张图的完整步骤。
- **考察点**：能否把零散概念串成"建图流程"。
- **评分维度**：
  - 定义 State（2 分）
  - add_node（2 分）
  - add_edge / add_conditional_edges 含 START/END（3 分）
  - compile 挂 checkpointer（3 分）

<details>
<summary>参考答案</summary>

1. 定义 State（TypedDict + reducer）。
2. `StateGraph(State)` 创建 builder。
3. `add_node(name, fn)` 加节点。
4. `add_edge(START, 首节点)`、`add_edge(末节点, END)`，以及 `add_conditional_edges` 加动态分支。
5. `builder.compile(checkpointer=...)` 得到可运行图。

</details>

### Q5. Reducer（append / replace）有什么不同？选错会怎样？
- **考察点**：State 合并机制，经典易错点。
- **评分维度**：
  - append 累加 vs replace 覆盖（5 分）
  - 选错后果（messages 被覆盖丢历史 / 结论字段不断累积）（5 分）

<details>
<summary>参考答案</summary>

- **append**（如 messages）：返回的新值追加到旧值后，不覆盖。
- **replace**（默认）：返回的字段整体覆盖旧值。
选错后果：本该 append 的 messages 用了 replace → 历史被整体覆盖，模型丢失上下文；本该 replace 的结论字段（如 risk_level）用了 append → 字段不断累积成列表，下游读到错误结构。应按"是否要累积"选 reducer。

</details>

### Q6. Checkpointer 有哪三种主要作用？生产环境用哪种后端？
- **考察点**：持久化机制与工程落地。
- **评分维度**：
  - 崩溃恢复 / 多轮共享 / 支撑中断（6 分）
  - 生产用 Postgres/Redis，不用 MemorySaver（4 分）

<details>
<summary>参考答案</summary>

三种作用：① 崩溃断点续跑（进程挂了用同 thread_id 从断点继续）；② 同 thread_id 多轮 invoke 共享 State；③ 支撑 Interrupt 实现 HITL。
生产用持久化后端（PostgresSaver / RedisSaver），MemorySaver 仅测试用——重启即丢全部进度。thread_id 用业务唯一值（如理赔单号）。

</details>

### Q7. 工具（Tool）在 LangGraph 图里要满足什么要求？为什么？
- **考察点**：把 Day1 幂等等考点迁移到图。
- **评分维度**：
  - 幂等（图会重放）（4 分）
  - ToolNode 错误要回填而非中断（3 分）
  - 不要把人工审批塞进工具（3 分）

<details>
<summary>参考答案</summary>

- **必须幂等**：图可能重放节点（尤其中断恢复时），非幂等工具会放大副作用（重复扣款/发短信）。
- **错误要回填**：ToolNode 默认异常会中断图，应配置 `handle_tool_errors` 把错误当 tool 消息回填（对应 Day1 "错误也回填"铁律）。
- **人工审批不是工具**：需要暂停等人的事要用 `interrupt()`，不能塞进 ToolNode 同步等返回值。

</details>

---

## L3 深度（原理与权衡，每题 10 分）

### Q8. Interrupt 和 Checkpointer 是什么关系？为什么实现 HITL 缺一不可？
- **考察点**：两个核心机制协同的底层原理。
- **评分维度**：
  - Interrupt 负责暂停交还控制权（3 分）
  - Checkpointer 负责存暂停前状态（3 分）
  - 缺 Checkpointer 无法恢复（4 分）

<details>
<summary>参考答案</summary>

- **Interrupt**：在节点里调用，让图暂停、把控制权交回调用方。
- **Checkpointer**：按 thread_id 把每步 State 快照存下，包括中断前的状态。
关系：Interrupt 负责"暂停"，Checkpointer 负责"把暂停前的状态存好"。若只 Interrupt 不配 Checkpointer，一暂停状态就没了，恢复时从不了断点——HITL 无法实现。两者配合：interrupt 暂停 → 审核员输入 → `Command(resume=...)` 同 thread_id 唤醒 → 从断点继续。

</details>

### Q9. 如何在特药理赔 Agent 中用条件边实现"高风险转人工、低风险自动过"？
- **考察点**：把条件边 + HITL 组合到具体业务。
- **评分维度**：
  - risk_check 后用条件边按 risk_level 路由（4 分）
  - high → human_approve（interrupt）（3 分）
  - low/medium → END 自动过（3 分）

<details>
<summary>参考答案</summary>

在 `risk_check` 节点后加 Conditional Edge，路由函数读 `state["risk_level"]`：返回 `"human"` 走到 `human_approve` 节点（内部 `interrupt()` 暂停等人审批），返回 `"done"` 直接走到 END 自动出结论。这样低风险案件模型自动过、高风险案件由审核员拍板，兼顾效率与合规。

</details>

### Q10. 为什么说"路由函数必须是纯函数、不能有副作用"？结合图的重放机制说明。
- **考察点**：理解图执行模型（重放）对代码约束。
- **评分维度**：
  - 图会重放节点（4 分）
  - 副作用会重复执行（发短信/写库）（3 分）
  - 应在节点外/工具内做副作用，路由只读 State（3 分）

<details>
<summary>参考答案</summary>

LangGraph 在中断恢复、重试时会**重放**节点和路由函数。若路由函数里有副作用（如发短信、写数据库），重放会重复执行，造成重复通知/脏数据。因此路由函数应是纯函数，只根据 State 返回分支名；真正的副作用放在 ToolNode 调用的工具里（且工具要幂等），或放在图的边界（调用方处理）。

</details>

### Q11. 对比 LangGraph 与手写 Agent Loop（Day1），它解决了哪些工程痛点？
- **考察点**：框架选型的整体思维。
- **评分维度**：
  - 持久化/断点续跑（3 分）
  - 中断恢复/HITL（3 分）
  - 可可视化可审计、复杂分支清晰（4 分）

<details>
<summary>参考答案</summary>

手写 Loop 在玩具脚本够用，但企业长流程（如理赔）有痛点：① 状态不持久化，进程崩了要从头跑；② 无法中途暂停等人审批；③ 多分支用 if/else 写很乱、难审计。LangGraph 用 StateGraph 显式表达流程（可可视化、可审计），用 Checkpointer 持久化、用 Interrupt 暂停恢复，把 Day1 的循环升级成"可控、可恢复、可合规"的生产系统。

</details>

---

## L4 场景（特药理赔 Agent 实战，每题 10 分）

### Q12. 请用 LangGraph 为特药理赔 Agent 设计一个含"模型决策→工具→人工审批"的图结构（口述节点与边即可），并说明 thread_id 怎么设。
- **考察点**：综合组图能力 + 持久化标识设计。
- **评分维度**：
  - 节点设计（call_model/tools/risk_check/human_approve）（4 分）
  - 边与条件边连接（3 分）
  - thread_id = 理赔单号，支撑审计与恢复（3 分）

<details>
<summary>参考答案</summary>

节点：`call_model`（调模型）、`tools`（ToolNode）、`risk_check`（风险判断）、`human_approve`（interrupt 暂停等人）。
边：`START→call_model`；`call_model` 条件边（有 tool_calls→tools，否则→risk_check）；`tools→call_model`（结果回填再决策）；`risk_check` 条件边（high→human_approve，否则→END）；`human_approve→risk_check`（审批后继续）。
thread_id 用理赔单号（如 `claim_8848`），保证一笔理赔全程状态隔离、可追溯、可断点恢复。

</details>

### Q13. 特药理赔 Agent 在 human_approve 节点 interrupt 后，审核员在后台系统点了"批准"。请描述从"暂停"到"恢复并出结论"的完整调用链与所需参数。
- **考察点**：Interrupt / Command(resume) / Checkpointer 协同的端到端流程。
- **评分维度**：
  - interrupt 暂停 + Checkpointer 存状态（3 分）
  - 审核员输入经前端传到后端（2 分）
  - `graph.invoke(Command(resume="approved"), cfg)`，cfg 同 thread_id（5 分）

<details>
<summary>参考答案</summary>

1. 图执行到 `human_approve`，`interrupt({"question":..., "risk":...})` 被调用 → 图暂停，当前 State 已由 Checkpointer 按 thread_id 存好，控制权交回调用方（后端可把待审批案件推给审核员界面）。
2. 审核员在后台点"批准"，前端把决策（"approved"）发回后端。
3. 后端用**同一 thread_id** 的配置 `cfg`，调用 `graph.invoke(Command(resume="approved"), cfg)`。
4. 图从 `human_approve` 断点恢复，拿到 resume 值，返回 `{"approved": True}`，再走到 `risk_check` 条件边判为 `done`，最终到达 END 生成结论并回写。
关键点：必须同一 thread_id + `Command(resume=...)`；Checkpointer 保证状态不丢；全程轨迹留痕可审计。

</details>

---

## 评分汇总表

| 题号 | 层级 | 主题 | 满分 | 自评分 | 达标要求 |
|------|------|------|------|--------|----------|
| Q1 | L1 | State/Node/Edge | 10 | / | 三者定义+配合 |
| Q2 | L1 | 普通边 vs 条件边 | 10 | / | 条件边=模型参与控制流 |
| Q3 | L1 | ToolNode 作用 | 10 | / | 解析/执行/回填 |
| Q4 | L2 | 组图步骤 | 10 | / | 四步完整 |
| Q5 | L2 | Reducer 机制 | 10 | / | append/replace+选错后果 |
| Q6 | L2 | Checkpointer 作用 | 10 | / | 三作用+持久化后端 |
| Q7 | L2 | 工具要求 | 10 | / | 幂等/回填/非审批 |
| Q8 | L3 | Interrupt+Checkpointer | 10 | / | 协同实现 HITL |
| Q9 | L3 | 条件边做风险路由 | 10 | / | high→human/low→END |
| Q10 | L3 | 路由纯函数 | 10 | / | 重放→无副作用 |
| Q11 | L3 | 对比手写 Loop | 10 | / | 持久化/中断/可审计 |
| Q12 | L4 | 设计理赔图 | 10 | / | 节点+边+thread_id |
| Q13 | L4 | 中断恢复端到端 | 10 | / | 同 thread_id+Command |
| **合计** | — | — | **130** | **/** | — |

### 达标线
- **达标线①（组图）**：Q1~Q4 + Q7 得分 ≥ 40/50，能讲清 State/Node/Edge/Conditional Edge 如何组成一张图，并能结合特药理赔举例。
- **达标线②（HITL）**：Q6 + Q8 + Q9 + Q13 得分 ≥ 35/40，能讲清 Checkpointer 与 Interrupt 在 HITL 里的作用及端到端恢复流程。
- **总分达标**：≥ 100/130（约 77%）可视为本日面试达标；L4 两题（Q12、Q13）必须各 ≥ 7 分，否则需重练实战组图。
- **建议**：L3/L4 失分多时，重读手册 s5（组图）、s7（Checkpointer）、s8（Interrupt）、s10（实战），并亲手跑通一个带 interrupt 的最小例子。
