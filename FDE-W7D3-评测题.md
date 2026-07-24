# FDE W7D3 评测题 · Spring AI MCP Client/Server 与 Observability

> 对应手册：`FDE-W7D3-SpringAI-MCP与可观测-学习手册.html`
> 候选人背景：36 岁，Java + 大数据 + 医疗保险。场景统一用「保险知识库 / 特药理赔 Agent」。
> 评分：每题按"考察点 + 评分维度"给分，L1/L2 答到要点即过，L3/L4 看深度与工程权衡。

---

## 一、L1 基础（概念辨识，4 题）

### 1. 什么是 MCP？它和 W2 手写的"保单/规则 Server"是什么关系？
- **考察点**：MCP 协议本质与演进关系。
- **评分维度**：① 模型连接外部工具/数据的开放协议；② 标准化 tools/resources/prompts；③ W2 是自定义协议，MCP 是标准化放大。
<details><summary>参考答案</summary>

MCP（Model Context Protocol）是统一的开放协议，把"模型/Agent 如何连接外部工具与数据"标准化为 tools/resources/prompts。W2 我们手写的保单/规则 Server 是自定义协议；MCP 就是把这类 Server 标准化——任何兼容 MCP 的 Client 都能发现并调用，工具方与模型方互不需知道对方技术栈。W2 是"原理+手写"，W7D3 是"标准协议+框架支持"。

</details>

### 2. MCP Client 调用远程工具和 W7D2 的本地 @Tool 有何本质区别？
- **考察点**：本地 vs 远程工具。
- **评分维度**：① 位置（同进程 vs 远端）；② 发现（编译期扫描 vs 运行时 listTools）；③ 网络/团队边界。
<details><summary>参考答案</summary>

本质区别：本地 @Tool 是同进程 Java 方法，编译期注解扫描；MCP Client 调的是远端 Server 进程，运行时通过 listTools 动态发现并转成 ToolCallback。后者有网络开销、跨团队、工具可独立部署扩缩。ChatClient 调用体验一致，但远程工具要额外处理超时/重试/降级。

</details>

### 3. MCP 有哪两种传输方式？分别适用什么场景？
- **考察点**：transport 选型。
- **评分维度**：① Stdio（同机子进程，调试）；② HTTP/SSE（独立部署，生产）；③ 保险场景选 HTTP。
<details><summary>参考答案</summary>

Stdio：Client 启动 Server 子进程、通过 stdin/stdout 通信，适合同机、随用随起的本地工具/调试。HTTP/SSE：Client 通过 HTTP 连已部署的 Server，适合独立部署、多 Client 共享的生产服务。保险平台的保单 Server 应选 HTTP/SSE，可水平扩展、多应用共享、便于加鉴权限流。

</details>

### 4. Observability 三支柱是什么？为什么 AI 应用比传统应用更需要它？
- **考察点**：可观测性基础。
- **评分维度**：① Logs/Metrics/Traces；② 调用不可预测、成本按 token、链路长。
<details><summary>参考答案</summary>

三支柱：结构化日志（Logs）、聚合指标（Metrics）、链路追踪（Traces）。AI 应用更需要：LLM 调用不可预测（高时延/可能失败/输出随机）、成本来自 token 需按 trace 统计、工具调用链长（模型→工具→回灌）需链路串联定位。没有 Trace，一次理赔答错无法定位是哪一层问题。

</details>

---

## 二、L2 进阶（机制与用法，4 题）

### 5. 写出 MCP Client 连接远端 Server 并把工具注册到 ChatClient 的代码骨架。
- **考察点**：Client API 熟练度。
- **评分维度**：① HttpClientSseClientTransport；② McpClient.sync + initialize；③ listTools 转 ToolCallback + defaultTools。
<details><summary>参考答案</summary>

```java
McpClientTransport transport = HttpClientSseClientTransport.builder("http://mcp-policy-svc:8081").build();
McpSyncClient client = McpClient.sync(transport).build();
client.initialize();
List<ToolCallback> callbacks = client.listTools(null).tools().stream()
    .map(t -> new McpToolCallback(t, client)).toList();
ChatClient chatClient = ChatClient.builder(chatModel).defaultTools(callbacks).build();
```
要点：用 HTTP/SSE 传输连已部署 Server；initialize 建立会话；listTools 动态发现并转 ToolCallback。生产要加超时+重试+降级防 Server 不可用带崩链路。

</details>

### 6. 如何把现有 Java 方法暴露成标准 MCP Server？
- **考察点**：Server 侧。
- **评分维度**：① @Tool 标注方法；② @EnableMcpServer（MCP Server starter）；③ 任何 Client 可调用。
<details><summary>参考答案</summary>

用 `@Tool` 标注要暴露的方法（写清 description/@ToolParam），在 Spring Boot 启动类加 `@EnableMcpServer`（引入 MCP Server starter），框架自动把这些 @Tool 暴露为标准 MCP 协议。这样保单系统团队的 Java 资产无需改造即可被任意 MCP Client（Spring/Python/JS）调用，实现工具资产化。注意 Server 侧必须做鉴权与输入校验。

</details>

### 7. 一个 AI 应用同时作为 MCP Client 和 Server 时，架构长什么样？请画图说明。
- **考察点**：双栈架构。
- **评分维度**：① 上层 Agent 作为 Server 暴露自身能力；② 同时作为 Client 消费底层 Server；③ 工具网络/MCP 网格。
<details><summary>参考答案</summary>

```
上游 Agent ──MCP──> 我的应用(MCP Server: 特药推理能力)
                         │ 同时作为 MCP Client
                         └──MCP──> 保单 Server / 规则 Server / 药品 Server
```
底层是保单/药品/规则三个 MCP Server（各团队维护），上层理赔 Agent 应用既消费（Client）又暴露（Server），形成工具网络。Gateway 可在 MCP 传输前做统一鉴权限流，构成"MCP 网格"。这正是 W2 单 Server 思想在 Spring AI 下的放大。

</details>

### 8. AI 调用至少要埋哪些 Trace 字段？在 Advisor 里怎么埋？
- **考察点**：Trace 字段 + 埋点实现。
- **评分维度**：① trace_id/model/tool/token/latency/status；② MDC 传 trace_id + Micrometer 记指标；③ trace_id 来自入口不新建。
<details><summary>参考答案</summary>

至少埋：trace_id（全链路 ID）、model（实际模型）、tool（工具名）、prompt/completion tokens、latency_ms、status（成功/失败/降级）。在 Advisor 里：从 MDC 取入口传入的 trace_id（不新建），调用前后用 Micrometer Timer/Counter 记录 model/tool/latency，token 取 ChatResponse 真实 Usage。这样一次理赔可聚合分析。直接对齐 W4 的 Trace 字段规范。

</details>

---

## 三、L3 深度（取舍与对比，3 题）

### 9. MCP Server 暴露出去后，有哪些必须自己补的安全与质量问题？
- **考察点**：MCP 不是银弹。
- **评分维度**：① 鉴权/作用域；② 输入校验；③ 权限/幂等（有副作用）；④ description 影响准确率。
<details><summary>参考答案</summary>

MCP 只解决连接标准化，不解决工具本身问题：① 鉴权——暴露即对外开放，必须做 token/scope 防越权查他人保单；② 输入校验——防恶意/越界参数；③ 权限与幂等——有副作用工具（创建工单）要权限+幂等+确认；④ description 质量——即使被别人调，工具描述仍影响调用准确率，要写清。保险场景合规尤其严。

</details>

### 10. 为什么 trace_id 必须在入口生成、Advisor 里只取不新建？工具调用 Trace 怎么记才对？
- **考察点**：Trace 串联正确性。
- **评分维度**：① 入口生成贯穿全链路；② Advisor 新建会断链；③ 工具展开子 span 而非扁平一条；④ token 取真实值。
<details><summary>参考答案</summary>

trace_id 在入口（Gateway/Controller）生成并贯穿全链路，Advisor 只取 MDC 里的，否则每段对话各自生成 id，无法串联定位。工具调用 Trace 要展开成子 span（模型→tool→回灌）保留因果，扁平记一条会丢失"哪步慢/哪步错"。token/cost 必须从 ChatResponse.getMetadata().getUsage() 取真实值，不可估算。且要和 W7D4 Gateway 出口的 trace_id 一致。

</details>

### 11. 作为 Java 大数据 + 医保候选人，如何把 W7D3 的可观测能力做成"平台级"？
- **考察点**：背景与平台能力结合。
- **评分维度**：① 统一 Trace 贯穿 Gateway→工具；② 成本进数仓分摊；③ 质量大盘告警；④ 合规可追溯。
<details><summary>参考答案</summary>

① 统一 Trace：所有 AI 调用经 Advisor 埋点，trace_id 贯穿 Gateway→应用→工具，对接现有 APM（Prometheus+链路追踪）；② 成本可控：token/cost 指标进 Kafka+数仓，按业务线/险种核算 AI 成本；③ 质量可查：工具错误率、拒答率、幻觉率进监控大盘并告警；④ 合规可审：日志含 trace_id+用户标识，理赔决策全程可追溯（监管刚需）。结合大数据（数仓分析）+医保（审计刚需）背景，可观测性从"会用 Micrometer"升到"平台能力"。

</details>

---

## 四、L4 场景（综合设计，2 题）

### 12. 场景：保险平台要把"保单系统（Java 团队）"和"特药目录（Python 团队）"都接入理赔 Agent，并要保证调用可观测、故障不雪崩。请设计方案，并指出达标线①要讲清的两点。
- **考察点**：综合应用 + 达标线映射。
- **评分维度**：① 两团队各暴露 MCP Server；② Agent 作为 Client 用 McpSyncClient 接入；③ 超时/重试/降级防雪崩；④ 明确达标线①=Client+Server 双栈。
<details><summary>参考答案</summary>

方案：
- 保单团队用 `@Tool + @EnableMcpServer` 暴露 Java 保单 MCP Server；特药团队用 Python MCP SDK 暴露特药目录 MCP Server（体现跨语言解耦）。
- 理赔 Agent 作为 MCP Client，用 McpSyncClient（HTTP/SSE）连两个 Server，listTools 发现后转 ToolCallback 注册到 ChatClient，像调本地工具一样调。
- 韧性：每个 Client 配超时 + 指数退避重试 + 降级（Server 不可达时返回"暂无法查询，转人工"），避免雪崩。
- 可观测：Advisor 埋 trace_id/model/tool/token/latency，对接 APM。
达标线①要讲清：① MCP Client 用 McpSyncClient 连远端、动态发现 tools 转 ToolCallback；② MCP Server 用 @Tool + @EnableMcpServer 暴露 Java 方法为标准协议；同一应用可双栈形成工具网络。

</details>

### 13. 场景：监管要求"每笔特药理赔决策的完整链路可审计"，包括用了哪个模型、调了哪些工具、花了多少 token。请你设计 Trace 方案，并说明和 W4/W7D4 的衔接。
- **考察点**：架构权衡（最高阶）。
- **评分维度**：① W4 规范→W7D3 实现；② 入口生成 trace_id 贯穿；③ 工具子 span；④ 与 W7D4 Gateway 出口 trace_id 一致；⑤ 审计落库。
<details><summary>参考答案</summary>

方案（规范→实现→衔接）：
- W4 定义 Trace 字段规范（trace_id/model/tool/step/token/cost/latency/status/error）——这是"要记什么(Why)"。
- W7D3 用 Advisor 埋点 + Micrometer 实现——"怎么记(How)"：入口（Gateway/Controller）生成 trace_id 写入 MDC，Advisor 贯穿取用；工具调用展开为子 span（模型→tool→回灌）；token/cost 取 ChatResponse 真实 Usage。
- W7D4 Gateway 在 HTTP 出口也记同一 trace_id 与 model/token，必须与应用层 trace_id 一致，否则链路断裂——Gateway 管接入层、Advisor 管应用层，二者同一 id 串联。
- 审计落库：Trace 数据（含 user_id、决策结果、引用条款）进数仓/对象存储，按监管留存期保留，支持按 trace_id 回放任意一笔理赔的完整链路。
结论：W4 规范、W7D3 实现、W7D4 Gateway 出口，三者靠同一 trace_id 串成端到端可审计链路。

</details>

---

## 五、评分汇总表

| 题号 | 层级 | 主题 | 分值(建议) | 达标要点 |
|------|------|------|-----------|----------|
| 1 | L1 | MCP 与 W2 关系 | 5 | 开放协议 + 标准化放大 |
| 2 | L1 | Client vs 本地 @Tool | 5 | 远端/动态发现/网络 |
| 3 | L1 | 两种传输 | 5 | Stdio 调试 / HTTP 生产 |
| 4 | L1 | Observability 三支柱 | 5 | Logs/Metrics/Traces + AI 为何需要 |
| 5 | L2 | MCP Client 代码 | 8 | transport + initialize + listTools |
| 6 | L2 | 暴露 MCP Server | 8 | @Tool + @EnableMcpServer |
| 7 | L2 | 双栈架构图 | 8 | Client+Server 工具网络 |
| 8 | L2 | Trace 字段+埋点 | 8 | 字段集 + MDC + Micrometer |
| 9 | L3 | MCP 安全质量 | 10 | 鉴权/校验/幂等/description |
| 10 | L3 | trace_id 串联正确性 | 10 | 入口生成 + 子 span + 真实值 |
| 11 | L3 | 候选人平台能力 | 10 | 统一Trace+成本+质量+合规 |
| 12 | L4 | 跨团队接入设计 | 13 | 双 Server + Client + 韧性 |
| 13 | L4 | 可审计 Trace 方案 | 13 | W4规范→W7D3实现→W7D4衔接 |

**总分 108（建议按百分制折算）。达标线：**
- **合格线（70 分）**：L1 全对 + L2 至少 3 题达标 + 能讲清达标线①（Spring AI 怎么同时做 MCP Client 和 Server）。
- **优秀线（90 分）**：L1/L2 全达标 + L3 两题有深度 + 能讲清达标线②（Observability 怎么接 W4 的 Trace 字段——规范 vs 实现、同一 trace_id 串联、与 W7D4 Gateway 出口一致）。

> 达标线①：能讲清 Spring AI 怎么同时做 MCP Client（McpSyncClient 连远端、listTools 动态发现转 ToolCallback）和 MCP Server（@Tool + @EnableMcpServer 把 Java 方法暴露成标准协议），以及双栈工具网络。
> 达标线②：能讲清 Observability 怎么接 W4 的 Trace 字段（W4 定规范/Why，W7D3 用 Advisor+Micrometer 实现/How；trace_id 入口生成贯穿全链路、与 W7D4 Gateway 出口一致；工具展开子 span；token 取真实值）。
