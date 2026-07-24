# FDE W7D4 评测题 · AI Gateway 自研核心

> 对应手册：`FDE-W7D4-AI网关自研核心-学习手册.html`
> 候选人背景：36 岁，Java + 大数据 + 医疗保险。场景统一用「保险知识库 / 特药理赔 Agent」。
> 评分：每题按"考察点 + 评分维度"给分，L1/L2 答到要点即过，L3/L4 看深度与工程权衡。

---

## 一、L1 基础（概念辨识，4 题）

### 1. AI Gateway 在企业 AI 架构里处于哪一层？它和 Spring AI（W7D1~D3）是什么关系？
- **考察点**：Gateway 分层定位。
- **评分维度**：① 接入层（应用与 Provider 之间）；② 跨业务线治理；③ 与 Spring AI 应用层分层互补。
<details><summary>参考答案</summary>

AI Gateway 横在所有 AI 应用和所有模型 Provider 之间，是接入层，集中做路由/重试/Fallback/成本/Trace/限流等跨业务线治理。Spring AI（W7D1~D3）在应用层做业务编排、Advisor 治理、Tool、Memory。两者分层互补：Gateway 管全局横切，Spring AI 管业务级。不是二选一。

</details>

### 2. AI Gateway 至少包含哪六大核心模块？
- **考察点**：模块清单。
- **评分维度**：① Model Adapter/路由；② 超时重试；③ Fallback；④ Token 成本；⑤ Trace Hook；⑥ 限流。
<details><summary>参考答案</summary>

六大模块：Model Adapter/Provider 路由（统一结构+选对模型）、超时重试（抗抖动）、Fallback（主备切换保可用）、Token 成本统计（成本可控）、Trace Hook（全链路可观测）、简单限流（保护配额+公平）。各模块对应"稳/省/清"的治理目标。

</details>

### 3. W1D4 的多模型接入原型和 W7D4 企业 Gateway 主要区别是什么？
- **考察点**：演进认知。
- **评分维度**：① 原型只统一 HTTP；② 企业版加了限流/成本/Trace/Fallback/可配置路由；③ 从"能用"到"可治理可运营"。
<details><summary>参考答案</summary>

W1D4 是轻量多模型统一接入原型（统一 HTTP + 响应结构）；W7D4 在此基础上加了：可配置多维路由、指数退避重试、Fallback、多维度限流、token 成本统计与预算、trace_id 全链路。演进主线是"能调通→调得稳→调得起→调得清"，从个人 demo 到平台中枢。

</details>

### 4. 限流一般有哪些维度？常用什么算法？
- **考察点**：限流基础。
- **评分维度**：① 用户/Provider/业务线/Token 速率；② 令牌桶最常用；③ 分布式用 Redis。
<details><summary>参考答案</summary>

维度：按 API Key/用户、按 Provider、按业务线、按 Token 速率。算法：令牌桶（允许突发、最常用）、漏桶（恒定速率）、滑动窗口（精确计数）。分布式部署下要用 Redis 集中计数，否则单机限流在多实例失效。被限流返回 429 + Retry-After。

</details>

---

## 二、L2 进阶（机制与用法，4 题）

### 5. Provider 路由可以按哪些维度做？为什么规则必须可配置而非硬编码？
- **考察点**：路由设计。
- **评分维度**：① 场景/健康/成本/用户分层；② 运营调规则要即时生效不能发版；③ 优先级清晰防冲突。
<details><summary>参考答案</summary>

路由维度：按场景（简单问答→Qwen、推理→DeepSeek、长文→Claude）、按模型健康（报错率高则切备选）、按成本/配额（超预算降级）、按用户/业务线（VIP 走强模型）。规则必须外置到配置中心而非硬编码——运营调一次场景归属不应发版；且多维度叠加要定义清晰优先级避免冲突。规则改了要能热更新+灰度+回滚。

</details>

### 6. 重试和 Fallback 分别针对什么？有什么注意事项？
- **考察点**：韧性设计。
- **评分维度**：① 重试只针对超时/429/5xx+指数退避；② Fallback 主模型持续失败切备；③ 总超时上限+fallback 标记+记录。
<details><summary>参考答案</summary>

重试：只重试可重试错误（超时、429、5xx），指数退避+抖动防风暴；不重试 4xx 业务错。Fallback：主模型持续失败切备选模型，返回结果并标记 `fallback=true` 供上游感知。注意：重试+Fallback 叠加会放大大延迟，要设总超时上限；Fallback 到弱模型质量下降，上游应感知并可能走人工复核；两者都要记录事件供质量分析。

</details>

### 7. Token 成本统计为什么必须在 Gateway 做？统计哪些维度、怎么接数仓？
- **考察点**：成本治理。
- **评分维度**：① Gateway 唯一看到全流量；② provider/模型/业务线/用户；③ 真实 usage→Kafka→数仓；④ 算重试/Fallback/缓存。
<details><summary>参考答案</summary>

Gateway 是唯一能看到所有 AI 流量的点，在它统计最全、一致。维度：按 provider/模型/业务线/用户统计 token 与花费，从响应 `usage` 取真实值。链路：实时写入 Kafka→数仓，做成本分摊与预算告警。注意要计入重试/Fallback 的额外 token、扣除缓存命中，且近实时告警避免超支才发现。呼应 W4 cost 字段、W7D3 数仓分析。

</details>

### 8. Gateway 的 Trace Hook 怎么和 W4/W7D3 串成全链路？
- **考察点**：Trace 串联。
- **评分维度**：① 入口生成 trace_id；② 出口记 model/provider/token/latency；③ 应用层 Advisor 续用同一 id；④ W4 规范/W7D3 实现/W7D4 接入层。
<details><summary>参考答案</summary>

Gateway 在入口（HTTP 接入）生成 trace_id，出口记录 model/provider/token/latency/status；应用层（W7D3）Advisor 续用同一 trace_id（从 MDC 取）。W4 定义 Trace 字段规范（Why），W7D3 在 Advisor 实现（应用视角），W7D4 在 Gateway 实现（接入层视角），三处用同一 trace_id 串成端到端链路。Gateway 是 trace 起点，务必在此生成并向下透传。

</details>

---

## 三、L3 深度（取舍与对比，3 题）

### 9. 全面对比"全自研 Gateway" 和 "直接用 LiteLLM"，指出各自的坑。
- **考察点**：架构权衡深度。
- **评分维度**：① LiteLLM 开箱即用覆盖大部分模块；② 全自研重复造轮子（ChatClient/限流/成本）；③ 纯 LiteLLM 深度定制受限；④ 决策看技术栈/合规。
<details><summary>参考答案</summary>

LiteLLM 开箱即用：多模型路由、重试、Fallback、成本追踪（含 UI）、限流（Redis）、OpenTelemetry Trace——覆盖 W7D4 大部分模块，省人力。坑：深度定制（特殊路由、内部审计字段、合规留痕）受扩展点限制；Python 技术栈对 Java 团队有运维成本。
全自研坑：重复造 ChatClient/重试/限流/成本轮子，浪费人力且易有 bug，不推荐从零写。
结论：用 LiteLLM/Spring AI 当"轮子"，自研当"车体"——通用能力用现成，企业独有逻辑（内部鉴权/审计/成本分摊/强合规）自研薄层。

</details>

### 10. 保险/金融场景为什么要"自研或深度集成"而不是纯用 LiteLLM？给出具体理由。
- **考察点**：合规与内部系统。
- **评分维度**：① 审计留痕接自有系统；② 内部鉴权/工单/账期；③ Java 技术栈；④ 成本分摊到内部账。
<details><summary>参考答案</summary>

金融/保险有强合规：① 审计留痕必须进自有系统（监管可追溯每笔理赔决策），LiteLLM 自带 UI 不一定满足内部审计字段；② 内部鉴权（对接企业 SSO/工单）、成本分摊到内部账期，需原生对接；③ 团队多为 Java 技术栈，纯 Python LiteLLM 运维成本高；④ 路由策略可能要按内部业务规则（如"理赔优先于营销"）定制。所以常"LiteLLM 做底层 + 自研薄封装接企业治理"，而非纯用。

</details>

### 11. 作为 Java 大数据 + 医保候选人，怎么把 W7D4 Gateway 讲成"集团级 AI 基础设施"？
- **考察点**：背景与平台能力结合。
- **评分维度**：① 统一管控多险种配额；② 合规内建（鉴权/审计）；③ 成本运营（分摊/告警）；④ 韧性（重试/Fallback）；⑤ 大数据接数仓。
<details><summary>参考答案</summary>

定位为集团级 AI 基础设施：① 统一管控——所有 AI 流量经 Gateway，多险种/多 Agent 共享配额与统一成本视图；② 合规内建——鉴权、审计留痕、敏感词拦截在 Gateway 做，业务零改动获得合规；③ 成本运营——按业务线分摊 AI 成本、预算告警；④ 韧性——重试/Fallback 保证理赔等关键链路在模型抖动时不中断；⑤ 大数据——成本/Trace 进 Kafka+数仓做运营分析。结合 Java（技术栈）+大数据（数仓）+医保（合规刚需），凸显"平台中枢"而非"会用工具"。

</details>

---

## 四、L4 场景（综合设计，2 题）

### 12. 场景：保险集团要建统一 AI Gateway，承载车险/健康险/理赔三条业务线，要求"核心理赔线在模型故障时仍可用、成本按业务线核算、调用全程可审计"。请设计 Gateway 方案，并指出达标线①要讲清的模块。
- **考察点**：综合应用 + 达标线映射。
- **评分维度**：① 路由（按业务线优先级）；② 重试/Fallback 保理赔可用；③ 成本按业务线统计+预算；④ Trace 全链路审计；⑤ 明确达标线①=六大模块。
<details><summary>参考答案</summary>

方案：
- 路由：按业务线/场景多维路由，配置"理赔线优先于营销线"策略，外置配置中心热更新；
- 韧性：主模型超时指数退避重试，持续失败 Fallback 到备选模型并标 fallback=true，保证理赔不中断；设总超时上限；
- 成本：Gateway 从 usage 统计 token，按 provider/模型/业务线/用户维度进 Kafka→数仓，超预算降级+告警；
- 审计：入口生成 trace_id，出口记 model/provider/token/latency/status，与 W7D3 应用层同一 id 串联，落库满足监管回放；
- 限流：按业务线令牌桶，防某线吃光配额。
达标线①要讲清六大模块：Model Adapter/路由、超时重试、Fallback、Token 成本、Trace Hook、简单限流，及各自治理目标（稳/省/清）。

</details>

### 13. 场景：团队在"全自研 Java Gateway"和"直接上 LiteLLM"之间争论，请你做架构决策，并设计"LiteLLM 当轮子 + 自研薄层"的混合方案（含哪些自研、哪些用现成）。
- **考察点**：最高阶架构决策。
- **评分维度**：① 结论：不全自研也不纯用；② 现成：路由/重试/Fallback/限流/成本基础；③ 自研：内部鉴权/审计/成本分摊/定制路由/合规；④ Java 栈考量。
<details><summary>参考答案</summary>

决策：不全自研（避免重复造 ChatClient/限流/成本轮子），也不纯用 LiteLLM（深度定制与合规受限）。采用"LiteLLM/Spring AI 当轮子 + 自研薄层接企业治理"的混合：
- 用现成（LiteLLM Proxy 或 Spring AI）：多模型接入、负载均衡、重试、Fallback、基础限流、成本追踪、OpenTelemetry Trace——这些是通用能力。
- 自研薄层（Java）：① 内部鉴权对接企业 SSO/工单；② 审计留痕按监管字段落自有库；③ 成本分摊到内部账期与业务线；④ 定制路由策略（如理赔优先）；⑤ 把 LiteLLM 的 trace 与内部 APM 对齐到同一 trace_id。
理由：保留对核心治理（合规/成本/审计）的完全控制，同时不重复造基础件，契合 Java 技术栈与金融强合规。呼应 W7D3 的"规范→实现"与 W4 的 Trace 字段。

</details>

---

## 五、评分汇总表

| 题号 | 层级 | 主题 | 分值(建议) | 达标要点 |
|------|------|------|-----------|----------|
| 1 | L1 | Gateway 分层定位 | 5 | 接入层 vs Spring AI 应用层 |
| 2 | L1 | 六大核心模块 | 5 | 路由/重试/Fallback/成本/Trace/限流 |
| 3 | L1 | W1D4→W7D4 演进 | 5 | 加治理模块，从 demo 到平台 |
| 4 | L1 | 限流维度与算法 | 5 | 四维+令牌桶+Redis |
| 5 | L2 | 路由维度+可配置 | 8 | 场景/成本/健康/用户，配置热更新 |
| 6 | L2 | 重试 vs Fallback | 8 | 可重试错+退避；主备切换+标记 |
| 7 | L2 | 成本统计 | 8 | Gateway 唯一全流量+数仓 |
| 8 | L2 | Trace 串联 | 8 | 入口生成+应用层续用同一 id |
| 9 | L3 | 自研 vs LiteLLM | 10 | 各自坑+轮子车体决策 |
| 10 | L3 | 金融为何自研 | 10 | 审计/内部对接/Java栈/合规 |
| 11 | L3 | 候选人平台能力 | 10 | 统一管控+合规+成本+韧性+数仓 |
| 12 | L4 | 集团 Gateway 设计 | 13 | 理赔可用+成本核算+可审计 |
| 13 | L4 | 混合架构决策 | 13 | 现成+自研薄层分界 |

**总分 108（建议按百分制折算）。达标线：**
- **合格线（70 分）**：L1 全对 + L2 至少 3 题达标 + 能讲清达标线①（AI Gateway 核心模块：路由/重试/Fallback/限流/Trace 各自作用）。
- **优秀线（90 分）**：L1/L2 全达标 + L3 两题有深度 + 能讲清达标线②（自研 Gateway 和直接用 LiteLLM 的取舍：通用能力用现成、企业独有逻辑自研，轮子+车体）。

> 达标线①：能讲清 AI Gateway 核心模块——Model Adapter/路由（多维可配置选模型）、超时重试（只重试可重试错）、Fallback（主备切换保可用）、Token 成本（按业务线统计+预算）、Trace Hook（入口生成 trace_id 串联）、简单限流（令牌桶多维）。
> 达标线②：能讲清自研 Gateway 和直接用 LiteLLM 的取舍——LiteLLM 开箱即用覆盖通用能力（路由/重试/Fallback/成本/限流/Trace），中小团队直接用；但深度定制、内部系统对接、Java 技术栈、金融强合规时自研或用"LiteLLM 当轮子 + 自研薄层接企业治理"，保留对核心治理的控制。
