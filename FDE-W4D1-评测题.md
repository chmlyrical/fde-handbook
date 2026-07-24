# FDE W4D1 评测题 · Agent 核心与 Loop

> 配套手册：《FDE-W4D1-Agent核心与Loop-学习手册.html》
> 主题：Agent / Workflow / Agentic Workflow 区分、ReAct、Tool Calling Loop、停止条件、最大步骤、Token 预算、错误处理与回填
> 场景主线：特药理赔 Agent（36 岁候选人，Java + 大数据 + 医疗保险背景）
> 题型：L1 基础 / L2 进阶 / L3 深度 / L4 场景，共 13 题
> 计分：每题满分 10 分；参考答案折叠在 `<details>` 中，建议先自答再看。

---

## L1 基础（概念识别，每题 10 分）

### Q1. 请区分 Agent、Workflow、Agentic Workflow 三者，并给出一个判断标准。
- **考察点**：核心概念辨析，是否真正理解"控制权/流程确定性"差异。
- **评分维度**：
  - 能否说清三者定义（4 分）
  - 能否指出关键差异"下一步由谁决定"（3 分）
  - 能否给出可操作判断标准（3 分）

<details>
<summary>参考答案</summary>

- **Workflow**：流程预先写死，由代码/配置固定每一步顺序，模型只在确定节点调用。控制权在开发者。
- **Agent**：由模型在运行时自主决策下一步（调哪个工具、是否继续、何时停）。控制权在模型。
- **Agentic Workflow**：骨架由开发者编排（图/流程写死），但在关键分支、工具选择上由模型决策。控制权 = 开发者（骨架）+ 模型（节点内）。
- **判断标准**：看"下一步行动是否由模型在运行时动态决定"。写死的是 Workflow；模型动态选的是 Agent；骨架写死、节点内模型决策的是 Agentic Workflow。

</details>

### Q2. 一个系统"调用了大模型"就一定是 Agent 吗？为什么？
- **考察点**：澄清对 Agent 的常见误解。
- **评分维度**：
  - 能否明确"否"（3 分）
  - 能否举出反例（固定 if/else + 每步调一次 LLM）（4 分）
  - 能否回到判断标准（3 分）

<details>
<summary>参考答案</summary>

不一定。如果流程是 `if/else` 写死的，每步只是固定调一次 LLM 做摘要/抽取，那只是 Workflow。Agent 的本质是"模型动态决定下一步"的控制循环，而非"调用了 LLM"这一事实本身。区分点仍是：下一步是谁决定的。

</details>

### Q3. 什么是 ReAct 范式？它和 Tool Calling 是什么关系？
- **考察点**：经典 Agent 范式与其现代实现的关系。
- **评分维度**：
  - 能否说出 Thought→Action→Observation 模板（5 分）
  - 能否说明 Tool Calling 是 ReAct 的结构化实现（5 分）

<details>
<summary>参考答案</summary>

ReAct = Reason（Thought，想清楚为什么这么做）+ Act（Action，调哪个工具）+ 看到 Observation（工具结果）后继续下一轮。它是 Agent Loop 的思想原型。现代 Tool Calling 把 Action 结构化成 `tool_calls` 字段，把 Observation 结构化成 `role:tool` 消息，思想完全一致，只是更机器可读、更可靠。

</details>

---

## L2 进阶（机制理解，每题 10 分）

### Q4. 描述一个最简单的 Tool Calling Loop 的循环过程（从初始化到停止）。
- **考察点**：是否理解 Agent 的运行骨架，而非只会调框架。
- **评分维度**：
  - 初始化 messages（2 分）
  - 调模型看 tool_calls（2 分）
  - 执行工具 + 回填（2 分）
  - 再决策循环（2 分）
  - 停止条件（无 tool_calls 即结束）（2 分）

<details>
<summary>参考答案</summary>

1. 初始化 `messages`（system + user）。
2. 循环：把 messages 发给模型。
3. 模型返回：若带 `tool_calls` → 逐个解析 name/arguments，本地执行工具，拿到结果。
4. 把结果封装成 `role:"tool"`、`tool_call_id` 对应的消息，回填进 messages。
5. 带更新后的 messages 再调模型。
6. 若模型不再返回 `tool_calls`（直接给文本）→ 循环结束，该文本为最终答案。

</details>

### Q5. Agent 停止循环有哪几种信号？为什么必须设置 max_steps？
- **考察点**：循环终止与失控防护。
- **评分维度**：
  - 三种停止信号（模型给答案 / max_steps / Token 上限）（5 分）
  - 必须设 max_steps 的理由及降级处理（5 分）

<details>
<summary>参考答案</summary>

三种信号：① 模型返回最终答案（无 tool_calls）；② 达到 max_steps（代码硬限制）；③ 达到 Token 预算 / max_tokens。
必须设 max_steps：模型可能因工具反复报"信息不足"、或 A↔B 互调而陷入死循环，不设上限会无限烧 token。达到 max_steps 不能直接报错给业务方，要降级（转人工 / 返回"超时，请补充材料"）。

</details>

### Q6. 工具结果回填有哪两条铁律？工具报错时应如何处理？
- **考察点**：回填机制与错误处理。
- **评分维度**：
  - 带 tool_call_id（3 分）
  - 错误也回填（脱敏）（4 分）
  - 代码层熔断（3 分）

<details>
<summary>参考答案</summary>

两条铁律：① 回填消息必须带 `tool_call_id`，框架靠它把结果对应回那次调用；② 工具出错也要回填（把脱敏后的错误文本作为 tool 消息返回），让模型看到后自行决定重试/换工具/转人工，而非让程序抛异常中断。
同时代码层要加失败熔断（连续 N 次错误直接转人工），不能无限信任模型的"自救"。异常堆栈不能原样回填（可能含密钥/SQL），需脱敏。

</details>

### Q7. max_steps 与 max_tokens 有什么区别？Agent 的成本主要来自哪里？
- **考察点**：参数边界与成本意识。
- **评分维度**：
  - 两者定义区别（4 分）
  - 每轮重发历史导致成本线性增长（4 分）
  - 压缩回填 / 设预算（2 分）

<details>
<summary>参考答案</summary>

- max_steps：循环最多几轮（轮数上限）。
- max_tokens：单轮模型输出 token 上限（也有"总 Token 预算"指整次运行上限）。
区别：前者管"转几圈"，后者管"每轮/总共生成多少 token"。
成本来源：每轮都要把**完整 messages（含之前所有工具结果）重发**给模型，Token 随轮数线性增长。所以要把工具结果只回填必要字段、对长结果截断/摘要，并设总 Token 预算。

</details>

---

## L3 深度（原理与权衡，每题 10 分）

### Q8. 为什么企业场景（如特药理赔）推荐用 Agentic Workflow 而非纯 Agent？请从可控性、可审计、合规角度分析。
- **考察点**：工程落地选型思维，而非盲目追新。
- **评分维度**：
  - 纯 Agent 不可预测/难审计（4 分）
  - Agentic Workflow 兼顾可控与灵活（3 分）
  - 结合理赔合规要求（3 分）

<details>
<summary>参考答案</summary>

纯 Agent 流程由模型动态生成，不可预测、难复盘、难满足医保合规与审计要求（每笔理赔需可解释、可追溯）。Agentic Workflow 把确定性主流程（上传→OCR→抽取→校验→风险→审批→回写）写成图骨架，只在"材料是否完整、风险等级、是否转人工"等节点交给模型决策，既保留灵活性又保证整体可预期、可审计、可合规。特药理赔正是采用此形态。

</details>

### Q9. 从工程角度，一个生产级 Agent Loop 需要哪些保障？（至少列 5 点）
- **考察点**：工程化思维（可观测、幂等、可控、降级、复用）。
- **评分维度**：每点 2 分，满分 10；需覆盖：可观测/Trace、幂等、max_steps+预算+熔断、降级兜底、复用旧系统。

<details>
<summary>参考答案</summary>

1. **可观测**：每轮（输入、tool_calls、工具结果、输出）落 Trace，便于线上复盘。
2. **幂等**：工具要幂等，模型重试不产生副作用（重复扣减/发短信）。
3. **可控**：max_steps + Token 预算 + 熔断，保证不失控、不烧钱。
4. **降级**：任何一步失败有兜底（转人工/默认值/拒答），流程不卡死。
5. **复用旧系统**：Agent 是编排层，挂载已有 Java 微服务、规则引擎、数仓，不重写。
6. （加分）低 Temperature（0~0.3）保证决策与参数稳定。

</details>

### Q10. 为什么 Agent 的工具必须幂等？结合特药理赔举例说明不幂等的后果。
- **考察点**：把 Agent 概念映射到后端工程常识。
- **评分维度**：
  - 模型可能重试调用（3 分）
  - 副作用放大（3 分）
  - 理赔场景具体例子（4 分）

<details>
<summary>参考答案</summary>

模型可能因为没"看到"预期结果或异常重试而重复调用同一工具。若工具不幂等，重试会放大副作用。理赔场景：若"发放理赔款""发送告知短信"工具不幂等，模型重试一次就可能导致重复打款、重复骚扰用户；OCR/查询虽只读，但重复调用也浪费成本。因此工具要设计成"同一参数多次调用结果一致、无额外副作用"（用幂等键/去重表）。

</details>

### Q11. 为什么说"把异常抛给业务方"是 Agent 实现的错误做法？正确做法是什么？
- **考察点**：错误处理哲学——错误是下一次决策的 Observation。
- **评分维度**：
  - 中断 vs 回填的差异（4 分）
  - 脱敏回填让模型自救（3 分）
  - 代码层熔断兜底（3 分）

<details>
<summary>参考答案</summary>

错误直接抛异常会中断整个 Loop，丢掉前面所有上下文，且模型失去"自救"机会。正确做法：把脱敏后的错误信息作为 `role:tool` 消息回填，模型看到 OCR 超时/参数错后，可自行选择"换工具/让用户输入/转人工"——这正是 Agent 比死代码优雅之处。但代码层仍要加熔断（连续失败转人工），不能无限信任模型自救，且异常文本须脱敏（去密钥/SQL）。

</details>

---

## L4 场景（特药理赔 Agent 实战，每题 10 分）

### Q12. 假设特药理赔 Agent 在循环第 3 步反复调用 `ocr_scan` 却一直返回"图片模糊，无法识别"，请描述你的处理方案（含代码层与模型层）。
- **考察点**：将停止条件/错误处理/熔断/降级综合应用到具体场景。
- **评分维度**：
  - 识别为死循环风险（2 分）
  - 脱敏错误回填给模型（2 分）
  - 代码层失败计数/熔断（3 分）
  - 降级：转人工 / 让用户重传（3 分）

<details>
<summary>参考答案</summary>

1. **模型层**：每次 `ocr_scan` 报错都把脱敏后的"图片模糊，识别失败"回填为 tool 消息，让模型自己决定——它可以选择换用另一 OCR 工具、或向用户请求重传清晰图片。
2. **代码层**：对 `ocr_scan` 连续失败做计数，达到阈值（如 3 次）触发熔断，不再无脑重试，直接中断 Loop。
3. **降级**：熔断后返回"材料识别失败，请重新上传清晰图片或转人工审核"，绝不把半成品答案当最终结果。
4. **可观测**：记录每次 OCR 入参、返回、失败计数到 Trace，便于定位是图片质量问题还是服务问题。

</details>

### Q13. 请为特药理赔 Agent 写一个（或口述）带 max_steps 与降级兜底的 Agent Loop 骨架，要求能调用 `ocr_scan` 和 `query_policy` 两个工具。
- **考察点**：综合动手能力，是否把本日所有考点串成可用代码/口述。
- **评分维度**：
  - 循环 + tool_calls 解析（3 分）
  - tool_call_id 回填 + 错误回填（3 分）
  - max_steps 终止（2 分）
  - 降级兜底（2 分）

<details>
<summary>参考答案</summary>

```python
def run_agent(user_req, max_steps=10):
    messages = [{"role":"system","content":"你是特药理赔助手，按需调用工具。"},
                {"role":"user","content":user_req}]
    for step in range(max_steps):
        resp = client.chat.completions.create(model=M, messages=messages,
                                               tools=TOOLS, temperature=0.1)
        msg = resp.choices[0].message
        if not msg.tool_calls:                 # 模型给出最终答案 → 停止
            return msg.content
        messages.append(msg)
        for tc in msg.tool_calls:
            try:
                args = json.loads(tc.function.arguments)
                result = dispatch(tc.function.name, args)
            except Exception as e:
                result = f"[tool_error] {e}"    # 错误也回填（脱敏）
            messages.append({"role":"tool","tool_call_id":tc.id,
                             "content":json.dumps(result, ensure_ascii=False)})
    return "已达最大步骤，转人工审核"            # 降级兜底
```
口述要点：循环"决策→执行→回填"，回填带 `tool_call_id`、错误也回填，模型无 tool_calls 即结束，达 max_steps 返回转人工降级。

</details>

---

## 评分汇总表

| 题号 | 层级 | 主题 | 满分 | 自评分 | 达标要求 |
|------|------|------|------|--------|----------|
| Q1 | L1 | 概念区分 | 10 | / | 说清三者+判断标准 |
| Q2 | L1 | Agent 误解澄清 | 10 | / | 指出"调 LLM≠Agent" |
| Q3 | L1 | ReAct 与 Tool Calling | 10 | / | Thought/Action/Observation |
| Q4 | L2 | Tool Calling Loop | 10 | / | 完整循环骨架 |
| Q5 | L2 | 停止信号 / max_steps | 10 | / | 三信号+必须设上限 |
| Q6 | L2 | 回填铁律 / 错误处理 | 10 | / | tool_call_id+错误回填 |
| Q7 | L2 | max_steps vs max_tokens | 10 | / | 成本来源=重发历史 |
| Q8 | L3 | Agentic Workflow 选型 | 10 | / | 可控/审计/合规 |
| Q9 | L3 | 生产级保障 | 10 | / | ≥5 点工程保障 |
| Q10 | L3 | 工具幂等 | 10 | / | 重试副作用+理赔例子 |
| Q11 | L3 | 异常处理哲学 | 10 | / | 回填而非中断 |
| Q12 | L4 | 死循环场景处理 | 10 | / | 熔断+降级+可观测 |
| Q13 | L4 | 手写 Agent Loop | 10 | / | max_steps+降级+回填 |
| **合计** | — | — | **130** | **/** | — |

### 达标线
- **达标线①（概念）**：Q1~Q3 得分 ≥ 25/30，能一句话区分 Agent / Workflow / Agentic Workflow 并说清判断标准。
- **达标线②（Loop）**：Q4~Q7 + Q13 得分 ≥ 45/50，能写/讲清一个简单 Agent Loop（决策→调工具→回填→判停），并覆盖 tool_call_id 回填、错误回填、max_steps 终止、降级兜底。
- **总分达标**：≥ 100/130（约 77%）可视为本日面试达标；L4 两题（Q12、Q13）必须各 ≥ 7 分，否则需重练实战。
- **建议**：L3/L4 失分较多时，重读手册 s8（工程含义）、s9/s10（实战）、s11（易错点），并亲手跑一遍 Q13 代码。
