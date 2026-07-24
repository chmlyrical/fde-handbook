# FDE W7D2 评测题 · Spring AI Tool Calling 与 Chat Memory

> 对应手册：`FDE-W7D2-SpringAI工具调用与记忆-学习手册.html`
> 候选人背景：36 岁，Java + 大数据 + 医疗保险。场景统一用「保险知识库 / 特药理赔 Agent」。
> 评分：每题按"考察点 + 评分维度"给分，L1/L2 答到要点即过，L3/L4 看深度与工程权衡。

---

## 一、L1 基础（概念辨识，4 题）

### 1. 什么是 Tool Calling？为什么叫 Agent 的"手"？
- **考察点**：工具调用本质。
- **评分维度**：① 模型决定调哪个方法 + 填参；② 框架本地执行 + 回灌；③ 让模型能查真实系统而非瞎编。
<details><summary>参考答案</summary>

Tool Calling 让 LLM 在对话中决定调用哪个本地函数、填什么参数，框架负责把 Java 方法暴露成工具描述、本地执行并把结果回灌。叫 Agent 的"手"是因为它让模型具备"操作外部系统"的能力——查保单、校验特药目录，而不是凭记忆编造。对应 W2 的自研工具调度循环，Spring AI 把它做成了内建能力。

</details>

### 2. @Tool 注解里的 description 起什么作用？写得差会怎样？
- **考察点**：工具描述对调用准确率的影响。
- **评分维度**：① 转成工具 JSON Schema 给模型；② 决定何时调、填什么参；③ 写差→选错工具/填错参。
<details><summary>参考答案</summary>

框架把 `@Tool` 的 description 和方法签名转成工具 JSON Schema 发给模型，模型据此判断"何时调用、填什么参数"。description 相当于"给模型看的产品文档"，写不清会导致模型错误选择工具或填错参数（如把保单号当药品名）。`@ToolParam` 同理要描述参数格式/示例。

</details>

### 3. ChatMemory 解决什么问题？为什么模型"无状态"反而是好事？
- **考察点**：会话记忆动机。
- **评分维度**：① 多轮对话历史外置存储；② 模型无状态→易水平扩展；③ 记忆由平台托管。
<details><summary>参考答案</summary>

ChatMemory 是 Spring AI 对多轮对话历史存储的抽象接口，按 conversationId 存取 Message 列表。模型本身无状态，对话上下文靠外部存储维持（"会话状态外置"）。模型无状态的好处是：易水平扩展、调用无副作用、状态治理（隔离/过期/审计）集中在平台层而不是模型里。

</details>

### 4. InMemoryChatMemory 能直接上生产吗？为什么？
- **考察点**：记忆实现的部署约束。
- **评分维度**：① 不能；② JVM 内存重启丢、不跨实例；③ 生产需 Redis/Jdbc 外部存储。
<details><summary>参考答案</summary>

不能直接上生产。InMemoryChatMemory 存在 JVM 内存，进程重启即丢失，且多实例部署时会"会话串台/丢失"。生产必须用 Redis（分布式）或 Jdbc（审计持久化）等外部存储实现，并通过 conversationId 关联，保证跨实例连续。

</details>

---

## 二、L2 进阶（机制与用法，4 题）

### 5. 写出用 @Tool 定义一个"查保单"工具、并注册到 ChatClient 的代码骨架。
- **考察点**：API 熟练度。
- **评分维度**：① @Tool + @ToolParam 写 description；② defaultTools 或 .tools 注册；③ 调用带 conversationId（若结合记忆）。
<details><summary>参考答案</summary>

```java
@Tool(description = "根据保单号查询保单基本信息：投保人、险种、生效日、状态")
public String queryPolicy(@ToolParam(description = "保单号，如 PA2024XXXX") String policyNo) {
    return policyService.getPolicy(policyNo).toJson();
}
// 注册
ChatClient client = ChatClient.builder(chatModel).defaultTools(policyTools).build();
// 调用
client.prompt().user("查保单 PA2024XXXX").tools(policyTools).call().content();
```
description 必须写清"何时用、参数格式、返回结构"。注意工具结果回灌前可脱敏，防敏感保单数据进上下文。

</details>

### 6. 描述工具调用从发请求到拿到最终答案的完整流程。
- **考察点**：执行链路。
- **评分维度**：① 带 schema 请求；② 模型返回 tool_calls；③ 框架执行 Java 方法；④ 结果作为 tool message 回灌；⑤ 模型续生成。
<details><summary>参考答案</summary>

1. ChatClient 携带工具 schema 请求模型；2. 模型返回 `tool_calls`（工具名 + 参数 JSON），不生成最终文本；3. Spring AI 按名找到 ToolCallback，本地反射执行 Java 方法；4. 执行结果作为 `tool` 角色消息追加进对话历史；5. 模型拿到结果继续生成，直到不再请求工具、输出最终答案。这与 W2 自研 Agent 的"action→observation→thought"循环完全对应。

</details>

### 7. 如何用 MessageChatMemoryAdvisor 把 RedisChatMemory 接进对话？调用时怎么关联会话？
- **考察点**：记忆 + Advisor 接线。
- **评分维度**：① Advisor 链注入；② conversationId 参数；③ 顺序（记忆在改写/RAG 前）。
<details><summary>参考答案</summary>

```java
ChatMemory mem = RedisChatMemory.builder(redisConnectionFactory).build();
ChatClient client = ChatClient.builder(chatModel)
    .defaultAdvisors(MessageChatMemoryAdvisor.builder(mem).build())
    .build();
client.prompt()
    .advisors(a -> a.param(ChatMemory.CONVERSATION_ID, "user-7788"))
    .user("我上次问的特药能报多少？").call().content();
```
记忆 Advisor 应在改写/RAG Advisor 之前，保证注入完整历史；Trace/日志 Advisor 可放最外层。

</details>

### 8. 工具方法执行抛异常，框架和工程上分别该怎么处理？
- **考察点**：工具执行健壮性。
- **评分维度**：① 异常要转成可回灌的"错误结果"而非炸链路；② 工程上加权限/幂等/确认（有副作用工具）；③ 结果裁剪防上下文过长。
<details><summary>参考答案</summary>

框架层：工具抛异常应被捕获并转成可回灌模型的"错误结果"消息，让模型能据此解释或重试，而不是让异常直接中断对话链路。工程层：有副作用的工具（创建工单、扣款）必须加权限校验、幂等保护、关键操作人工确认；工具结果要裁剪/脱敏，避免敏感或超长内容撑爆上下文。

</details>

---

## 三、L3 深度（取舍与对比，3 题）

### 9. 把 W7D2 的 Tool Calling 与 W2 自研 Tool Calling 做对比，并说明 Spring AI 多解决了哪些工程问题。
- **考察点**：原理到框架的演进认知。
- **评分维度**：① W2 手写调度循环；② Spring AI 内建循环；③ 额外解决：注册/多工具管理/与记忆+Advisor 集成。
<details><summary>参考答案</summary>

W2 我们手写"模型返回 action → 执行器执行 → observation 回灌 → 继续思考"的调度循环。Spring AI 把这套循环内建在 ChatClient 里，开发者只声明 `@Tool` 即可。它额外解决了工程问题：工具如何声明与自动注册（ToolCallback）、多个工具怎么管理（schema 体积/选择干扰）、以及工具如何与 ChatMemory（多轮）、Advisor（日志/重试/Trace）自然集成。即 W2 给"原理 + 轮子"，W7D2 给"生产级封装"。

</details>

### 10. 长会话场景下，ChatMemory 会带来哪些成本与风险？给出至少三种缓解手段。
- **考察点**：记忆的生产代价。
- **评分维度**：① token 成本线性增长；② 超长丢上下文；③ 隐私堆积；④ 手段：窗口裁剪/过期 TTL/摘要压缩/加密。
<details><summary>参考答案</summary>

代价：每轮都把历史重发给模型，长会话 token 成本线性增长；超过模型有效窗口后"记不住前面"；记忆含用户隐私（保单号/身份证）堆积有合规风险。
缓解：① 窗口裁剪——`get(id, n)` 只取最近 n 条；② 过期 TTL——Redis 设过期、Jdbc 定时清理；③ 摘要压缩——另一轮 LLM 把历史压成小结；④ 加密存储 + 访问审计。保险场景尤其重视加密与隔离。

</details>

### 11. 作为 Java 大数据 + 医保背景候选人，如何用 Tool Calling + ChatMemory 支撑"特药理赔 Agent"？
- **考察点**：背景与平台能力结合。
- **评分维度**：① 工具：查保单/查特药目录/创建工单；② 记忆：多轮连续 + 跨实例；③ 治理：Advisor 审计/Trace + 副作用保护；④ 大数据接入监控。
<details><summary>参考答案</summary>

① 工具层：声明 `queryPolicy`（查保单）、`checkSpecialDrug`（校验特药目录）、`createClaimTicket`（创建工单，带权限+幂等+人工确认）等 `@Tool`，description 写清触发条件；② 记忆层：用 RedisChatMemory 托管多轮对话，conversationId 由登录态派生，跨实例连续、设 TTL 防膨胀；③ 治理层：MessageChatMemoryAdvisor + AuditAdvisor 组合，工具结果脱敏后回灌，全程埋 trace_id；④ 把调用日志接入 Kafka+数仓做成本/质量分析。突出"实时查真实系统 + 多轮连续 + 强约束可审计"。

</details>

---

## 四、L4 场景（综合设计，2 题）

### 12. 场景：保险客服 Agent 要求"能连续多轮对话、能实时查保单、工具出错不能中断对话、敏感信息不进模型上下文"。请设计方案，并指出达标线②要讲清的两点。
- **考察点**：综合应用 + 达标线映射。
- **评分维度**：① ChatMemory(Redis) + conversationId；② @Tool 查保单 + 异常转可回灌错误；③ 结果脱敏/裁剪；④ 明确达标线②=记忆接不同存储 + 与 W3 关系。
<details><summary>参考答案</summary>

方案：
- 记忆：RedisChatMemory + MessageChatMemoryAdvisor，conversationId 由登录态派生，保证多轮连续且跨实例；设 TTL 防无限增长。
- 工具：`queryPolicy` 用 `@Tool` 声明，执行异常捕获后转为"查询失败，请核对保单号"类错误结果回灌，对话不中断。
- 脱敏：工具结果回灌前只保留必要字段（如状态/险种），剔除身份证号等 PII，或整体加密存储。
- 治理：AuditAdvisor 记 Trace，工具调用次数/失败率监控。
达标线②要讲清：① ChatMemory 怎么接不同存储（InMemory/Redis/Jdbc，通过 Advisor 接入，conversationId 关联，按跨实例/持久化/成本选型）；② 与 W3 会话状态的关系（W3 是设计原则——外置/隔离/持久化/过期，W7D2 的 ChatMemory 是 Java 落地实现）。

</details>

### 13. 场景：平台要支持"100 万保险用户同时在线客服"，会话记忆用 Redis 还是 Jdbc？请做选型并设计"隔离 + 过期 + 成本"三位一体方案。
- **考察点**：架构权衡（最高阶）。
- **评分维度**：① 选 Redis（高并发/低延迟/原生 TTL）；② 隔离：conversationId=用户+会话，防串号；③ 过期：TTL + 活跃续期；④ 成本：窗口裁剪 + 监控告警。
<details><summary>参考答案</summary>

选型：**Redis**。百万级并发下 Redis 内存存储低延迟、天然支持 TTL 与分布式，优于 Jdbc（磁盘 IO、量大查询慢）。Jdbc 仅在"监管强审计需落库"时作为补充归档。
三位一体方案：
- 隔离：conversationId = `userId:sessionId`，由网关/登录态强制注入，模型不可自定义，防串号泄露隐私；
- 过期：每次访问 `EXPIRE` 续期（如 30 分钟无活动过期），冷会话自动淘汰；
- 成本：写入时只保留最近 N 条（窗口裁剪）+ 超长摘要压缩，监控每会话 token 用量，超阈值告警并触发压缩；
- 合规：Redis 启用传输/静态加密，访问全量审计到数仓。
呼应 W3 会话状态设计原则与 W4 成本字段。

</details>

---

## 五、评分汇总表

| 题号 | 层级 | 主题 | 分值(建议) | 达标要点 |
|------|------|------|-----------|----------|
| 1 | L1 | Tool Calling 本质 | 5 | 模型决策+框架执行+回灌 |
| 2 | L1 | @Tool description | 5 | 转 schema，决定调用准确率 |
| 3 | L1 | ChatMemory 动机 | 5 | 状态外置，模型无状态易扩展 |
| 4 | L1 | InMemory 能否生产 | 5 | 不能，需外部存储 |
| 5 | L2 | @Tool 定义+注册 | 8 | 注解+defaultTools |
| 6 | L2 | 调用完整流程 | 8 | schema→tool_calls→执行→回灌→续生成 |
| 7 | L2 | 记忆+Advisor 接线 | 8 | Advisor 注入 + conversationId |
| 8 | L2 | 工具异常处理 | 8 | 转可回灌错误 + 副作用保护 |
| 9 | L3 | vs W2 自研 | 10 | 框架多解决的工程问题 |
| 10 | L3 | 长会话成本风险 | 10 | 裁剪/过期/压缩/加密 |
| 11 | L3 | 候选人平台能力 | 10 | 工具+记忆+治理+监控 |
| 12 | L4 | 客服 Agent 设计 | 13 | 连续+查保单+不中断+脱敏 |
| 13 | L4 | Redis vs Jdbc 选型 | 13 | 隔离+过期+成本三位一体 |

**总分 108（建议按百分制折算）。达标线：**
- **合格线（70 分）**：L1 全对 + L2 至少 3 题达标 + 能讲清达标线①（@Tool 定义与注册到 ChatClient 的方式）。
- **优秀线（90 分）**：L1/L2 全达标 + L3 两题有深度 + 能讲清达标线②（Chat Memory 接不同存储的实现差异 + 与 W3 会话状态设计原则的关系）。

> 达标线①：能讲清 Spring AI Tool Calling 怎么定义（@Tool/@ToolParam）和注册（defaultTools / .tools，ToolCallback 自动执行+回灌）。
> 达标线②：能讲清 Chat Memory 怎么接不同存储（InMemory/Redis/Jdbc，通过 Advisor 接入、conversationId 关联），和 W3 会话状态的关系（W3 设计原则 → W7D2 的 ChatMemory 是 Java 落地实现）。
