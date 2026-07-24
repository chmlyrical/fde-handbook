# FDE W4D5 评测题 · Trace 与成本

> 配套手册：`FDE-W4D5-Trace与成本-学习手册.html`
> 候选人背景：36 岁，Java + 大数据 + 医疗保险。所有场景以「特药理赔 Agent」为例。
> 题量：13 题（L1 基础 ×4 / L2 进阶 ×4 / L3 深度 ×3 / L4 场景 ×2）
> 用法：先口头/书面作答，再展开 `<details>` 对照参考答案，按评分维度自评。

---

## L1 基础（概念识别）

### Q1. Trace 和普通的"打日志"有什么区别？为什么 Agent 特别需要 Trace？
- **考察点**：Trace 的本质（Span 树 vs 扁平日志）。
- **评分维度**：① 是否点出"树状/父子关系/可聚合"；② 是否说出 Agent 链路长、分支多、成本高、需评测数据。
- **达标关联**：达标线① 前置概念。

<details>
<summary>参考答案</summary>

Trace 是带父子关系的 Span 树，能还原因果链、可跨请求聚合；普通日志是扁平文本，难定位链路。Agent 特别需要 Trace，因为一次回答背后是多次 LLM + 检索 + 工具 + 人工审批，链路长、分支多，且成本波动大、评测依赖真实数据、医疗需合规复盘。

</details>

### Q2. 列出你定义的固定 Trace 字段，并说明其中哪几个最关键。
- **考察点**：15 个固定字段记忆与重点识别。
- **评分维度**：① 能列出大部分字段（trace_id/user_id_hash/tenant_id/model_provider/model_name/prompt_version/knowledge_version/agent_version/tool_name/input_tokens/output_tokens/latency_ms/cost/status/error_type）；② 能指出四个 version 字段是隐藏重点；③ 能说清 user_id_hash 脱敏。

<details>
<summary>参考答案</summary>

15 字段：trace_id、user_id_hash、tenant_id、model_provider、model_name、prompt_version、knowledge_version、agent_version、tool_name、input_tokens、output_tokens、latency_ms、cost、status、error_type。最关键是四个 version（prompt/knowledge/agent/model）——让"效果变化"可归因到"哪次改动"；user_id_hash 必须脱敏。

</details>

### Q3. 请说出你定义的 7 个自定义 Span 类型。
- **考察点**：自定义 Span 记忆。
- **评分维度**：① 列出 llm.call / rag.retrieve / rerank.call / agent.step / tool.call / human.approval / eval.case；② 各说一个关键属性（如 rag 的命中数、human 的等待时长）。

<details>
<summary>参考答案</summary>

llm.call（model、token、prompt_version）、rag.retrieve（query、top_k、命中数、knowledge_version）、rerank.call（重排前后变化）、agent.step（step_index、选定工具）、tool.call（tool_name、状态码）、human.approval（审批人、决定、等待时长）、eval.case（case_id、指标、期望/实际）。

</details>

### Q4. 为什么 user_id_hash 要脱敏而不是存明文？
- **考察点**：隐私合规意识。
- **评分维度**：① 医疗数据合规（身份证/病历属敏感）；② 仍保留用户级分析能力；③ 不泄露给下游。

<details>
<summary>参考答案</summary>

医疗场景身份证、病历、手机号是高度敏感 PII，明文进 Trace 违反合规（如个人信息保护法）。用哈希脱敏后，仍能做用户级成本/质量分析，又不泄露明文，下游系统也无法反推个人。

</details>

---

## L2 进阶（机制理解）

### Q5. 描述一次 Agent 调用的 Trace 层级结构（用文字画树）。
- **考察点**：Span 树结构。
- **评分维度**：① 根是请求；② agent.step 为决策步；③ 下挂 llm/rag/rerank/tool/human.approval；④ 每 Span 带固定字段子集。

<details>
<summary>参考答案</summary>

根 Span=本次请求（带 15 固定字段）；下面若干个 agent.step（每步决策）作为父 Span；每个 agent.step 下挂 rag.retrieve、rerank.call、llm.call、tool.call，高风险步挂 human.approval；评测时挂 eval.case。每个 Span 带固定字段子集 + 业务属性（如 rag 命中数、human 等待时长）。

</details>

### Q6. 埋点时"上下文传播"是什么意思？自动和手动埋点如何分工？
- **考察点**：埋点工程机制。
- **评分维度**：① 跨服务 trace_id/父 Span 传递（W3C traceparent）；② 自动覆盖 llm.call（框架集成）；③ 手动覆盖 rag/rerank/tool/human.approval/eval；④ 建议封装 SDK 自动注入固定字段。

<details>
<summary>参考答案</summary>

上下文传播指跨进程/服务时，把 trace_id 和父 Span 上下文通过请求头/上下文对象传递，保证 Span 挂到同一棵树（分布式最易断链）。分工：自动埋点覆盖 llm.call（LangChain/Langfuse/OTel 集成自动出），手动埋点覆盖 rag/rerank/tool/human.approval/eval。建议封装内部 SDK 自动注入固定字段，避免漏填。

</details>

### Q7. 用 Trace 排错"答非所问"的标准流程是什么？
- **考察点**：Trace 排错应用。
- **评分维度**：① 用 trace_id 拉 Span 树；② 下钻到失败/异常 Span；③ rag 命中 0 → 知识库问题，查 knowledge_version；④ llm 慢 → 上下文/厂商；⑤ tool 失败 → error_type；⑥ human.approval 久 → 流程瓶颈。

<details>
<summary>参考答案</summary>

用 trace_id 拉完整 Span 树，下钻到具体失败/异常慢的 Span：rag.retrieve 命中 0 → 知识库/检索问题，查 knowledge_version；llm.call 慢 → 上下文过长或厂商排队；tool.call 失败 → 看 error_type；human.approval 久等 → 审批队列瓶颈（非模型问题）。版本字段能秒定位"哪次改动引入"。

</details>

### Q8. 流式输出下，Trace 的 token 和 latency 统计要注意什么？
- **考察点**：流式与 Trace 的耦合。
- **评分维度**：① 流式不降总延迟，只改善体感；② 单列 TTFT（首 token 延迟）；③ output_tokens 必须等流结束（end 事件）才结算；④ 钩子里别写重逻辑。

<details>
<summary>参考答案</summary>

流式只改善体感、不降总延迟，应单列 TTFT 作为用户体感指标。output_tokens 必须等流完全结束（end 事件）才落库结算，否则会少算成本。LangChain 的 on_llm_new_token 钩子可做增量处理，但钩子里别写重逻辑以免拖慢流或 OOM。

</details>

---

## L3 深度（成本与评测设计）

### Q9. 如何做 Agent 的成本归因？端到端成本包含哪些部分？
- **考察点**：成本归因维度与构成。
- **评分维度**：① 按 tenant/模型/prompt/tool 维度下钻；② 端到端=模型+检索+工具+人工；③ 点出"人工审批也是成本"；④ 反直觉根因（检索片段撑爆 input、重试放大）。

<details>
<summary>参考答案</summary>

成本靠 token/cost 字段按 tenant（谁烧钱）、model（贵否）、prompt_version（提示改动是否变贵）、tool_name（哪类工具贵）、user_id_hash（异常高频）下钻。端到端成本=模型 API + 检索/重排 + 工具调用 + 人工审批人力（human.approval 等待×人力单价）。常见反直觉根因：rag 召回太多撑爆 input_tokens、重试放大成本。

</details>

### Q10. 如何把 Trace 用于评测？eval.case Span 有什么价值？
- **考察点**：Trace 与评测的关系。
- **评分维度**：① 历史真实 Trace 当回归 case；② 线上聚合质量看板；③ 失败 Trace 沉淀为评测集；④ eval.case Span 让评测与生产同栈、可解释。

<details>
<summary>参考答案</summary>

Trace 是评测燃料：历史真实 Trace 当回归 case，新版本自动跑对比；线上实时聚合 status/error_type/latency 出质量看板；失败 Trace 筛选后沉淀为评测集。eval.case Span 把每个评测用例也写成可观测 Span（case_id、指标、期望/实际、评分），让"评测"和"生产"用同一观测栈，避免两套体系对不齐，且评分可解释。

</details>

### Q11. Langfuse、OpenTelemetry、LangChain Callbacks 三者如何选型与配合？
- **考察点**：技术选型论证。
- **评分维度**：① Langfuse 专而快（Agent 观测/评测）；② OTel 通用不锁、打通公司 APM；③ Callbacks 做流式/细粒度钩子；④ 推荐 OTel 为底座、Langfuse 上层，避免双写不一致。

<details>
<summary>参考答案</summary>

Langfuse 是 LLM/Agent 专用观测平台，开箱即用做 Trace 可视化与评测；OpenTelemetry 是通用可观测性标准，便于并入公司统一 APM（SRE 只认 Jaeger/Prometheus）、避免厂商锁定；LangChain Callbacks 在代码层做流式与细粒度钩子。推荐 OTel 为底座、Langfuse 做上层，通过 OTel 导出器打通，避免两套各自埋点导致数据不一致。大数据背景还可把 Trace 旁路进数仓做自定义聚合。

</details>

---

## L4 场景（真实业务综合）

### Q12. 特药理赔 Agent 上线后，老板问"这个 Agent 一个月花多少钱、钱花在哪"——请用 Trace 设计一份成本报告。
- **考察点**：成本归因的综合呈现。
- **评分维度**：① 端到端成本四部分拆解；② 按 tenant 出账单（多机构）；③ 按 model 看是否该换便宜模型；④ 按 human.approval 量化人力；⑤ 给出优化建议（降输入/换模型/降人工触发率）。

<details>
<summary>参考答案</summary>

报告分四块：① 模型成本（按 model_provider/model_name 汇总 token×单价，看贵模型是否必要）；② 检索/重排成本；③ 工具调用成本；④ 人工审批人力（human.approval 等待时长×人力单价，按 tenant 拆分）。按 tenant_id 出各机构账单用于定价/限流。优化建议：压缩检索片段降 input_tokens、简单步换便宜小模型、降人工审批触发率（优化触发规则）。所有数字来自 Trace 的 token/cost/等待时长聚合。

</details>

### Q13. 一次理赔出现"答复正确但极慢（30 秒）"，用户投诉。请用 Trace 定位可能原因并给出排查顺序。
- **考察点**：用 Trace 排错的综合能力。
- **评分维度**：① 列排查顺序（拉树→看各 Span latency/status）；② 可能原因覆盖：rag 召回过多、llm 上下文长、厂商排队、human.approval 等待、重试；③ 区分"模型慢"和"流程慢"；④ 对应优化。

<details>
<summary>参考答案</summary>

排查顺序：用 trace_id 拉 Span 树，按 latency_ms 降序看哪些 Span 最慢。可能原因：① rag.retrieve 召回过多片段导致后续 llm 的 input_tokens 暴涨、推理慢；② llm.call 上下文过长（历史未压缩）；③ 厂商排队/限流（model_provider 维度）；④ human.approval 等待（若走审批则端到端含审批时长，属流程慢非模型慢）；⑤ 重试放大（查重试次数）。优化：精简检索、压缩历史、加 prompt 缓存、优化审批触发/队列。关键是先区分"模型慢"还是"流程慢"再对症下药。

</details>

---

## 评分汇总表

| 层级 | 题号 | 主题 | 满分(自评分) | 关键失分点 |
|------|------|------|------|------|
| L1 基础 | Q1 | Trace vs 日志 | /5 | 未提 Span 树 |
| L1 基础 | Q2 | 固定字段 | /5 | 漏 version/脱敏 |
| L1 基础 | Q3 | 自定义 Span | /5 | 漏 human.approval |
| L1 基础 | Q4 | 脱敏合规 | /5 | 未提合规 |
| L2 进阶 | Q5 | Trace 层级 | /10 | 树结构错 |
| L2 进阶 | Q6 | 埋点传播 | /10 | 未提跨服务 |
| L2 进阶 | Q7 | Trace 排错 | /10 | 未下钻 Span |
| L2 进阶 | Q8 | 流式 Trace | /10 | 误认流式更快 |
| L3 深度 | Q9 | 成本归因 | /10 | 漏人工成本 |
| L3 深度 | Q10 | Trace 评测 | /10 | 漏 eval.case |
| L3 深度 | Q11 | 技术选型 | /10 | 未提不锁定 |
| L4 场景 | Q12 | 成本报告 | /15 | 未拆四部分 |
| L4 场景 | Q13 | 慢请求排查 | /15 | 未区分模型/流程 |

**总分**：/130

### 达标线
- **达标线①（40 分）**：Q1~Q5 能完整讲清"一次 Agent 调用的 Trace = 15 固定字段 + 一棵 Span 树（agent.step 为决策步，下挂 llm/rag/rerank/tool/human.approval）"，且 Q2 字段无重大遗漏。
- **达标线②（40 分）**：Q7（排错）+ Q9（成本）+ Q10（评测）合计 ≥32，且能说明 Trace 如何同时服务"排错 / 成本归因 / 评测"三件事。
- **总分 ≥ 90 / 130** 视为本日达标；L4 两题任一并达到 12/15 视为具备"场景讲述"能力。
