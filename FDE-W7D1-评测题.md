# FDE W7D1 评测题 · Spring AI 基础（ChatClient、Advisor、Structured Output）

> 对应手册：`FDE-W7D1-SpringAI基础-学习手册.html`
> 候选人背景：36 岁，Java + 大数据 + 医疗保险。回答请落到"Java 企业 AI 平台"能力，场景示例统一用「保险知识库 / 特药理赔 Agent」。
> 评分建议：每题按"考察点 + 评分维度"给分，L1/L2 答到要点即过，L3/L4 看深度与工程权衡。

---

## 一、L1 基础（概念辨识，4 题）

### 1. ChatClient 与 ChatModel 的关系是什么？业务代码应该面向谁编程？
- **考察点**：Spring AI 的核心抽象分层。
- **评分维度**：① 说清 ChatClient 是门面、ChatModel 是底层实现；② 业务只依赖 ChatClient；③ 能举例 QwenChatModel / DeepSeekChatModel 是可插拔实现。
<details><summary>参考答案</summary>

ChatClient 是面向应用层的统一对话门面，内部委托 ChatModel 实现（如 QwenChatModel、DeepSeekChatModel、AnthropicChatModel）。业务代码应只面向 ChatClient 编程，不依赖具体厂商 SDK，从而实现"换模型不改业务代码"。ChatModel 负责真正的协议适配与 HTTP 调用。

</details>

### 2. Advisor 在 Spring AI 里类比什么设计模式/组件？它围绕对话能做哪些事？
- **考察点**：拦截器链概念迁移。
- **评分维度**：① 类比 Servlet Filter / HandlerInterceptor；② around 执行模型；③ 列举至少 2 类治理（日志/重试/限流/改写/记忆）。
<details><summary>参考答案</summary>

Advisor 类比 Servlet Filter 或 Spring 的 HandlerInterceptor，是围绕一次对话请求的拦截器链。可在调用前后修改 prompt、做横切治理：日志审计、失败重试、限流、提示改写、注入会话记忆等。顺序由 order 控制，治理逻辑与业务解耦。

</details>

### 3. Spring AI 的 Structured Output 底层依赖什么机制把模型输出变成 Java 对象？
- **考察点**：结构化输出的实现原理。
- **评分维度**：① JSON Schema 约束；② Jackson 反射生成 schema + 反序列化；③ 区别于"只 parse 字符串"。
<details><summary>参考答案</summary>

依赖 JSON Schema 约束 + Jackson 反射：框架把 Java 类型（含 @JsonClassDescription / @JsonPropertyDescription）反射成 JSON Schema 注入 prompt 或 response_format，模型输出 JSON 后再反序列化为 Java 对象（BeanOutputConverter 机制）。本质是"类型 → schema → 输出 → 解析"，与 Python 侧 Pydantic 思路一致。

</details>

### 4. 用 Structured Output 拿到 Java 对象后，是否还需要做校验？为什么？
- **考察点**：结构化只保"形"不保"义"。
- **评分维度**：① 明确"要校验"；② 解释框架只保证字段/类型，值可能幻觉；③ 提到 Bean Validation / 业务校验。
<details><summary>参考答案</summary>

需要。结构化输出只保证语法合法、字段/类型符合 schema（"形对"），不保证值语义正确（可能字段填"我不确定"、金额越界、超业务范围）。应叠加 Bean Validation（@NotNull/@Min/@Pattern）+ 业务规则校验 + 失败带反馈重试 + 降级兜底。

</details>

---

## 二、L2 进阶（机制与用法，4 题）

### 5. 写出用 ChatClient 拿到结构化对象 `ClaimDecision` 的最小代码骨架，并说明关键注解作用。
- **考察点**：API 熟练度 + 字段描述。
- **评分维度**：① `call().entity(Class)` 用法；② `@JsonClassDescription`/`@JsonPropertyDescription` 提升 schema 质量；③ 失败处理意识。
<details><summary>参考答案</summary>

```java
@JsonClassDescription("特药理赔审核结论")
public class ClaimDecision {
    @JsonPropertyDescription("理赔是否成立") boolean approved;
    @JsonPropertyDescription("拒赔原因，成立时为空") String rejectReason;
    @JsonPropertyDescription("风险等级 low/medium/high") String riskLevel;
    @JsonPropertyDescription("无法判断时为 true") boolean shouldRefuse;
}
ClaimDecision d = chatClient.prompt()
    .user(u -> u.text("根据理赔材料给出结论：\n{claim}", claim))
    .call().entity(ClaimDecision.class);
```
`@JsonClassDescription`/`@JsonPropertyDescription` 给 schema 加语义描述，显著降低模型瞎填率。解析失败要抛异常走重试/降级。

</details>

### 6. 自定义一个 Advisor 实现"调用耗时打点"，需要重写哪些方法？流式调用要特别注意什么？
- **考察点**：Advisor 扩展点 + 流式陷阱。
- **评分维度**：① `getOrder()` + `adviseCall` 包裹 `chain.nextAroundCall`；② `adviseStream` 透传 Flux；③ 不可 collectList 阻断流式。
<details><summary>参考答案</summary>

实现 `Advisor` 接口：重写 `getOrder()` 定顺序，重写 `adviseCall(AdvisedRequest, CallAdvisorChain)` 在 `chain.nextAroundCall(req)` 前后计时。流式要同时实现 `adviseStream`，并且**必须透传 Flux**（在 Flux 上 map/doOnNext 埋点），不能 `collectList()` 提前聚合，否则失去流式且异常时拿不到部分结果。

</details>

### 7. Spring AI 如何做到"换模型不改业务代码"？给出两种落地方式。
- **考察点**：多模型统一机制。
- **评分维度**：① ChatModel 抽象 + starter 自动装配；② 配置切换（profile/env）；③ 运行时多 bean + 路由。
<details><summary>参考答案</summary>

靠 ChatModel 抽象 + Spring Boot starter 自动装配。两种方式：(1) 配置切换——只改 `spring.ai.qwen.*` / `spring.ai.deepseek.*` 等配置或 profile，注入的 ChatModel 变化，ChatClient 不变；(2) 运行时多 bean——同时注入多个 ChatModel，上层按场景路由（简单问答走 Qwen、推理走 DeepSeek、长文走 Claude）。这正是 W7D4 Gateway 路由在应用层的对应物。

</details>

### 8. 切换底层模型（如从 Qwen 切到 DeepSeek）后，面试官担心的三个隐患是什么？
- **考察点**：统一多模型的实际代价。
- **评分维度**：① 厂商特有参数需透传；② 输出风格/JSON 合规性变化需重测；③ 一个 prompt 未必通吃所有模型。
<details><summary>参考答案</summary>

① 厂商特有参数（如 Claude 必填 max_tokens、Qwen 的 thinking 开关）ChatClient 不自动抹平，需 `.with(ChatOptions)` 透传；② 切换后输出风格、JSON/结构化合规率会变，必须重新压测结构化成功率；③ "统一"不等于能力等价，长上下文、工具调用能力因模型而异，一个 prompt 未必通吃。

</details>

---

## 三、L3 深度（取舍与对比，3 题）

### 9. 把 W1 自研 Model Adapter 和 Spring AI 做个全面对比，并指出企业里两者如何共存。
- **考察点**：框架级 vs 轻量网关的架构判断。
- **评分维度**：① 薄网关 vs 厚框架的维度对比（抽象层级/可控性/开发速度）；② 不把两者对立；③ 提出"Gateway 接入层 + Spring AI 应用层"双栈。
<details><summary>参考答案</summary>

W1 自研 Adapter：薄封装，手写 ModelRouter 统一 HTTP + 响应结构，可控性最高、开发慢。Spring AI：厚框架，提供 ChatClient/Advisor/记忆/工具/向量全套，开发快、受约定约束。
共存方式——**双栈分层**：W1 自研 Adapter 演进为企业 AI Gateway（接入层，做鉴权/路由/限流/成本/Trace）；Spring AI 在应用层做业务编排、Advisor 治理、结构化。两者互补不冲突，不是二选一。

</details>

### 10. 重试 Advisor 应该重试哪些错误？为什么"对答案不满意"不能盲目重试？
- **考察点**：重试语义 + 幂等认知。
- **考察点补充**：联系 W1 带反馈重试。
- **评分维度**：① 只重试传输/限流(429)等可重试错误；② 对话非天然幂等；③ 答案质量问题要靠带反馈重试而非无脑重发。
<details><summary>参考答案</summary>

重试 Advisor 只应针对**传输层/限流错误**（网络抖动、HTTP 429），这类错误重试安全。对话请求本身非天然幂等（同 prompt 两次答案可能不同），对"答案不满意/格式错"盲目重发只是换一个随机结果，无效且浪费 token。格式/语义错误要用 W1 的"带反馈重试"——把校验错误喂回 prompt 让模型修正，而非无脑重发。

</details>

### 11. 作为 Java 大数据 + 医疗保险背景的候选人，你会如何用 Spring AI 支撑"特药理赔 Agent"的平台化能力？
- **考察点**：候选人背景与平台能力结合。
- **评分维度**：① 多模型编排（意图识别/推理/摘要）；② Advisor 内建治理（审计/限流/脱敏/Trace）；③ 结构化兜底（结论可校验可拒答）；④ 大数据监控接入。
<details><summary>参考答案</summary>

① 多模型编排：Qwen 做意图识别、DeepSeek 做理赔推理、Claude 做长保单摘要，ChatClient 统一入口；② 治理内建：把审计、限流、敏感词脱敏做成平台级 Advisor，新 Agent 自动继承；③ 结构化兜底：理赔结论一律 `entity(ClaimDecision.class)` + Bean Validation + 降级，保证下游 Java 系统稳定消费，且 `shouldRefuse` 字段支撑"知识库无答案即拒答"；④ 可观测：Advisor 内埋 trace_id/model/token/延迟，接入现有 Kafka+数仓做成本与质量分析。突出"结论可校验、可拒答、可审计"的强约束场景能力。

</details>

---

## 四、L4 场景（综合设计，2 题）

### 12. 场景：保险知识库问答要求"无答案必须拒答、有答案必须带条款引用、输出必须可解析"。请用 Spring AI 设计端到端方案，并指出达标线②要讲清的两点。
- **考察点**：综合应用 + 达标线映射。
- **评分维度**：① 结构化 Bean（approved/shouldRefuse/clauseIds）；② Advisor 注入知识（QuestionAnswerAdvisor / 自建 RAG Advisor）；③ 校验 + 拒答检测 + 降级；④ 明确达标线②=多模型统一 + 与 W1 取舍。
<details><summary>参考答案</summary>

端到端方案：
- 定义 `ClaimDecision` Bean，含 `approved`、`shouldRefuse`、`clauseIds`（引用条款）、`rejectReason`，用 `@JsonPropertyDescription` 强化 schema；
- 用 `QuestionAnswerAdvisor` 或自建 RAG Advisor 注入保险知识库检索结果（在 Advisor 内完成向量检索，业务方法保持干净）；
- 调用 `call().entity(ClaimDecision.class)`，解析后做 Bean Validation + 业务校验（条款 id 是否存在、金额合理性）；
- 检测 `shouldRefuse==true` 或输出含"未找到" → 不当正常答案返回，转拒答流程；
- 失败带反馈重试 ≤3 次，超限降级（默认值/转人工）；全程 Advisor 埋 Trace。
达标线②要讲清：① Spring AI 如何靠 ChatModel 抽象 + starter 统一多模型（换配置即换模型）；② 与 W1 自研 Adapter 的取舍（薄网关 vs 厚框架，双栈互补）。

</details>

### 13. 场景：公司要自建"企业 AI 平台"，争论"直接用 Spring AI 还是自研 Gateway"。请你做架构决策并说明理由，包含限流/成本/Trace 三个横切能力的归属。
- **考察点**：架构权衡（最高阶）。
- **评分维度**：① 结论：双栈不是二选一；② 限流/成本/审计属 Gateway 接入层（跨业务线统一）；③ Trace 两层都要（Gateway 出口 + Advisor 内）；④ 结合 W7D4 演进。
<details><summary>参考答案</summary>

结论：**两者都要，分层部署**。
- 接入层用自研 AI Gateway（W7D4 演进自 W1 Adapter）：统一做鉴权、Provider 路由、超时重试/Fallback、Token 成本统计、Trace Hook、简单限流——这些是**跨业务线横切治理**，Spring AI 管不到。
- 应用层用 Spring AI：业务编排、Advisor 内治理（日志/重试/记忆/RAG）、Structured Output。
- 限流/成本/审计归属 Gateway（全局配额、统一账单）；Trace 两层都有——Gateway 在 HTTP 出口记 trace_id/model/token，应用层 Advisor 记业务上下文与延迟，两者用同一个 trace_id 串联（呼应 W4、W7D3）。
理由：业务侧追求开发速度用框架，平台侧追求可控与统一治理来自研，互不替代。

</details>

---

## 五、评分汇总表

| 题号 | 层级 | 主题 | 分值(建议) | 达标要点 |
|------|------|------|-----------|----------|
| 1 | L1 | ChatClient/ChatModel | 5 | 门面 vs 实现，业务只依赖 ChatClient |
| 2 | L1 | Advisor 类比 | 5 | Filter 链 + 列举治理 |
| 3 | L1 | Structured Output 原理 | 5 | JSON Schema + Jackson 反射 |
| 4 | L1 | 结构化仍需校验 | 5 | 形对 ≠ 义对 |
| 5 | L2 | entity() 用法 | 8 | 代码骨架 + 描述注解 |
| 6 | L2 | 自定义 Advisor | 8 | getOrder + adviseCall + 流式透传 |
| 7 | L2 | 多模型统一 | 8 | ChatModel 抽象 + 两种落地 |
| 8 | L2 | 换模型隐患 | 8 | 透传/重测/能力差异 |
| 9 | L3 | vs W1 自研 Adapter | 10 | 双栈分层不对立 |
| 10 | L3 | 重试语义 | 10 | 只重试传输错 + 带反馈重试 |
| 11 | L3 | 候选人平台能力 | 10 | 多模型+治理+兜底+监控 |
| 12 | L4 | 拒答场景设计 | 13 | 结构化+Advisor+RAG+降级 |
| 13 | L4 | Gateway 架构决策 | 13 | 双栈 + 横切能力归属 |

**总分 108（建议按百分制折算）。达标线：**
- **合格线（70 分）**：L1 全对 + L2 至少 3 题达标 + 能讲清达标线①（ChatClient/Advisor/Structured Output 各自作用）。
- **优秀线（90 分）**：L1/L2 全达标 + L3 两题有深度 + 能讲清达标线②（多模型统一机制 + 与 W1 自研 Adapter 的取舍，双栈互补）。

> 达标线①：能讲清 ChatClient（统一对话门面）/ Advisor（拦截器链做治理）/ Structured Output（模型输出绑 Java 对象）各自作用。
> 达标线②：能讲清 Spring AI 怎么统一多模型（ChatModel 抽象 + starter 自动装配）、和 W1 自研 Adapter 的取舍（薄网关 vs 厚框架，接入层 Gateway + 应用层 Spring AI 双栈互补）。
