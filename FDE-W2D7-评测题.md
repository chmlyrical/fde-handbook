# FDE W2D7 评测题 · 整合与面试准备（W2 收尾）

> 配套《FDE-W2D7-整合与面试准备-学习手册.html》使用。共 13 题，分 L1 基础 / L2 进阶 / L3 深度 / L4 场景四档。
> 候选人背景：36 岁，Java + 大数据 + 医疗保险。场景统一围绕医疗保险 / 特药理赔 / 保险知识库。
> 评分建议：每题满分 10 分；参考答案折叠在 `<details>` 中，先自答再对照。

---

## L1 基础（概念识记，每题 10 分）

### Q1. 用一句话概括 W2 七天的学习主线，并列出每天主题。
- **考察点**：W2 整体脉络、串联能力。
- **评分维度**：①主线一句话准确；②七天主题齐全且顺序对；③体现"接能力与知识"。
<details>
<summary>参考答案</summary>

主线："让 LLM 接上企业能力与知识"。

- D1 Function Calling（模型调函数）
- D2 采样与结构化（稳定可解析输出）
- D3 Agent 编排（多步工具调度）
- D4 MCP 核心（标准协议模型与握手）
- D5 MCP Server（医保场景 Tool/Resource/Prompt）
- D6 基础 RAG（文档→检索→带引用答案）
- D7 整合收尾（串成基础保险知识库）

面试要能画出这条演进线，而非孤立背概念。
</details>

### Q2. 理赔系统需要哪三类 Tool？各自职责是什么？
- **考察点**：OCR/Database/Rule 三件套。
- **评分维度**：①三类齐全；②职责/输入/输出清晰；③性质（readOnly/权限）正确。
<details>
<summary>参考答案</summary>

- **OCR Tool**：病历/发票识别，输入图片/PDF，输出结构化文本+字段，readOnly（只识别）；
- **Database Tool**：查保单库/理赔记录，输入保单号/条件，输出记录/状态，读为主、受权限约束；
- **Rule Tool**：特药规则校验，输入诊断+药品+保单，输出是否报销+理由+上限，readOnly（只查算）。

三者整合进 MCP Server，输出统一结构化、带来源，供 Agent 编排与 RAG 拼接。
</details>

### Q3. 什么是 Spring AI 的 ChatClient？它解决什么问题？
- **考察点**：ChatClient 概念、模型无关。
- **评分维度**：①统一对话入口；②链式 API；③屏蔽厂商差异、可挂工具。
<details>
<summary>参考答案</summary>

ChatClient 是与 LLM 对话的**统一入口**，用链式 API（prompt().user().system().tools().call()）组装请求，屏蔽不同模型厂商差异——换模型只换注入的 ChatModel 实现，业务代码不变。

它可挂 Function Calling 工具或 MCP 工具回调（McpToolCallback），是 Java 落地 W2 能力的核心。医保团队复用 Spring 生态最顺。
</details>

### Q4. "基础保险知识库"由哪两部分融合而成？
- **考察点**：RAG + MCP 融合（达标线②铺垫）。
- **评分维度**：①RAG 侧（静态知识）；②MCP 侧（动态能力）；③融合方式。
<details>
<summary>参考答案</summary>

= **RAG（静态知识）+ MCP（动态能力）** 融合：
- RAG 侧：保单条款、特药目录、医保政策做成 ES 向量库，混合检索+引用（静态、可更新）；
- MCP 侧：OCR/Database/Rule 做成 Tool，让模型能"行动"（动态、实时、可控）；
- 融合：生成 prompt 同时拼入检索资料（带引用）与工具返回结果，模型基于"资料+实时数据"作答。

静态用 RAG、动态用 MCP，各取所长。
</details>

---

## L2 进阶（理解应用，每题 10 分）

### Q5. 详细对比 Function Calling、MCP、普通 API 三者差异（达标线①）。
- **考察点**：三者本质/决策/集成/适用。
- **评分维度**：①本质区分；②调用决策方；③集成与发现；④适用场景。
<details>
<summary>参考答案</summary>

| 维度 | 普通 API | Function Calling | MCP |
|---|---|---|---|
| 本质 | 代码对代码确定接口 | 模型调函数（单点） | 工具接入标准协议（生态） |
| 决策 | 代码硬编码 | 模型临场决定 | 模型经 Client 发现后决定 |
| 集成 | 手写请求/解析 | 各家模型私有 schema | 统一协议，Server 一次实现多 Client 通用 |
| 发现 | 无（看文档） | 硬编码/手动 | tools/list 动态发现 |
| 能力 | 任意 | 通常仅函数 | Tools+Resources+Prompts |
| 适用 | 确定流程/集成/批处理 | 单模型快速调函数 | 多工具/多模型标准化接入 |

三者不是替代关系，是层次不同，同一系统共存。
</details>

### Q6. 给出"三者选型决策树"，并用医保例子说明。
- **考察点**：选型逻辑、场景落地。
- **评分维度**：①决策树正确；②医保例子贴切（对账/原型/生产）。
<details>
<summary>参考答案</summary>

决策树：
- 需要模型"临场决策调不调/调哪个"吗？
  - 否 → 普通 API（确定流程/服务集成/批处理）
  - 是 → 要"标准化/多工具/多模型/动态发现"吗？
    - 否 → Function Calling（快速让单模型调函数）
    - 是 → MCP（统一协议，Server 一次实现多 Client 通用）

医保例子：① 定时理赔对账 → 普通 API；② 单模型原型"让模型查个保单" → Function Calling 最快；③ 生产理赔 Agent 连 OCR/DB/Rule/政策多源 → MCP。
</details>

### Q7. 普通 API 的定位是什么？它和 MCP Tool 什么关系？
- **考察点**：普通 API 适用、作为 MCP 底层。
- **评分维度**：①代码对代码确定接口；②适用确定流程；③常是 MCP Tool 底层实现。
<details>
<summary>参考答案</summary>

普通 API（REST/gRPC）是代码对代码的确定接口，调用方、参数、流程确定，与 LLM 无关。适用：确定业务流程、服务间集成、性能敏感/批处理路径。

与 MCP 关系：MCP Server 底层往往就是调普通 API——如 Database Tool 调内部保单 REST 服务取数给模型。内部批处理（每日理赔汇总）直接普通 API，不走 LLM，省成本保稳定。别为"接 LLM"把内部批处理也改 MCP。
</details>

### Q8. Spring AI 怎么做 Tool Calling？如何接 MCP Server？
- **考察点**：@Tool、注册、McpToolCallback。
- **评分维度**：①@Tool 示例；②注册到 ChatClient；③McpToolCallback 接 MCP。
<details>
<summary>参考答案</summary>

```java
@Tool(description = "按保单号查询保单状态与额度")
public Map<String,Object> queryPolicy(@ToolParam("保单号") String policyNo) {
    return policyService.query(policyNo);
}
ChatClient client = ChatClient.builder(model).defaultTools(queryPolicyTool).build();
```

接 MCP Server：用 `McpToolCallback`（把远程 Server 的工具转成本地回调）注册给 ChatClient。description 决定模型调不调；接 MCP 时协议由回调负责，业务方法仍保留原权限/审计。
</details>

---

## L3 深度（原理与安全，每题 10 分）

### Q9. 整合后的安全要贯通哪几层？Database Tool 为何是重点？
- **考察点**：安全串联、Database 敏感点。
- **评分维度**：①四层（OAuth/真实鉴权/信任/合规）；②Database 隐私与越权风险；③Annotation 不作边界。
<details>
<summary>参考答案</summary>

四层贯通：
1. 传输/端点：Remote Server 用 OAuth 门禁（D4 s11）；
2. Tool 级业务授权：Host/Server 真实权限（角色/保单状态），**不看 Tool Annotation**（D4 s10）；
3. 来源信任：Server 白名单/沙箱/能力审查（D4 s12）；
4. 数据合规：审计留痕、隐私脱敏（D5 s11）。

Database Tool 是重点：涉及客户隐私与权限，必须封装成受控查询（防注入/越权），真实鉴权在方法内，不靠描述或 Annotation。Annotation 仅 UX 提示。
</details>

### Q10. 端到端理赔流程是怎样的？每一步用到 W2 哪些能力？
- **考察点**：流程串联、能力映射。
- **评分维度**：①流程完整（OCR→DB→RAG→Rule→生成→审计）；②每步对应 D1–D6 能力；③含权限与拒答。
<details>
<summary>参考答案</summary>

1. 用户传病历图 → **OCR Tool** 识别（D5，readOnly，保原文引用）；
2. 模型抽保单号 → **Database Tool** 查状态/额度（D5，受权限约束）；
3. 问"能否报销" → **RAG** 检索特药目录（D6，BM25+向量，带 [n] 引用）；
4. 调 **Rule Tool** 跑规则（D5，低随机、可解释 reason）；
5. 模型综合生成结论，标引用；信息不足则 **should_refuse**（D6）；
6. 全链路同 traceId 审计（D5 s11）。

串联了 D2 低 T 结构化、D3 编排、D4/D5 MCP、D6 RAG——是 W2 能力的"阅兵式"。
</details>

### Q11. 为什么"静态知识用 RAG、动态能力用 MCP"要分开接？混在一起会怎样？
- **考察点**：RAG vs MCP 边界、时效与安全。
- **评分维度**：①静态便宜可更新 vs 动态精确可控；②实时数据塞 RAG 会陈旧/不安全；③融合时优先级。
<details>
<summary>参考答案</summary>

- 静态知识（条款/目录/政策）变化慢，用 RAG 检索便宜、可更新（改文档重建索引即可），适合"基于资料答"；
- 动态/实时数据（某客户当前保单状态）必须精确且最新，用 MCP Tool 实时查，可控可审计；
- 混在一起的问题：把实时保单状态塞进 RAG 索引会变陈旧且不安全（隐私入库）；反之把静态条款全做成 Tool 会浪费调用且无法语义检索。
- 融合时"实时工具结果优先于可能过期的检索资料"，谁说了算要明确。
</details>

---

## L4 场景（综合实战，每题 10 分）

### Q12. 场景题：面试官让你现场设计"基础保险知识库"系统，讲清架构与选型。
- **考察点**：系统整合、三者选型、医保落地。
- **评分维度**：①架构（Host+多Client+多Server+RAG）；②三者选型有依据；③安全与溯源贯通。
<details>
<summary>参考答案</summary>

架构：
- **Host（理赔 Agent）** 内多个 Client 各连一个 Server：OCR/DB/Rule MCP Server（stdio/HTTP）、RAG 检索 Server（ES 知识库）、政策 MCP Server（Remote, OAuth）；
- 工具集汇给模型做编排，结合 RAG 检索资料（带引用）生成理赔结论。

选型：
- 内部确定流程（对账/批处理）→ 普通 API；
- 单模型快速试验 → Function Calling；
- 生产多源标准化接入 → MCP。

安全与溯源：传输 OAuth + Tool 级真实鉴权（不看 Annotation）+ Server 白名单/沙箱 + 全链路审计 trace；RAG 与 MCP 返回都带引用，引用校验防编造。
</details>

### Q13. 场景题：面试官追问"你说 MCP 比普通 API 高级，那我内部两个 Java 服务要对账，是不是也该用 MCP？"
- **考察点**：识破误用、正确选型、沟通。
- **评分维度**：①直接否定误用；②讲清普通 API 适用确定流程；③说明 MCP 是"给 LLM 接"的。
<details>
<summary>参考答案</summary>

**直接否定**：不是"高级就全用"。内部两个 Java 服务对账是**确定流程**——调用方、参数、时机都固定，不需要模型"临场决策"，用普通 API（或 gRPC）最高效、最稳、最易测试，硬上 MCP 反而多一层协议与 LLM 不确定性，得不偿失。

MCP 的价值是"把工具/数据接给 LLM 标准化"——只有当调用需要模型临场决定（调不调、调哪个）且要跨多源多模型标准化时，才用 MCP。对账这种确定批处理，普通 API 完胜。

一句话：MCP 是"给 LLM 接的"，不是"替代服务间 API 的"。
</details>

---

## 评分汇总表

| 题号 | 档位 | 考察主题 | 满分 | 自评 | 考官评 |
|---|---|---|---|---|---|
| Q1 | L1 | W2 主线 | 10 | | |
| Q2 | L1 | 三类 Tool | 10 | | |
| Q3 | L1 | ChatClient | 10 | | |
| Q4 | L1 | 知识库构成 | 10 | | |
| Q5 | L2 | 三者差异 | 10 | | |
| Q6 | L2 | 选型决策树 | 10 | | |
| Q7 | L2 | 普通 API 定位 | 10 | | |
| Q8 | L2 | Spring AI Tool Calling | 10 | | |
| Q9 | L3 | 安全串联 | 10 | | |
| Q10 | L3 | 端到端流程 | 10 | | |
| Q11 | L3 | RAG vs MCP 边界 | 10 | | |
| Q12 | L4 | 系统架构场景 | 10 | | |
| Q13 | L4 | 选型误用追问 | 10 | | |
| **合计** | | | **130** | | |

### 达标线
- **L1+L2（基础进阶，80 分）**：必拿满。能讲清 W2 主线、三件套、ChatClient、三者差异与选型、Spring AI Tool Calling。
- **L3（深度，30 分中 ≥24）**：必须讲清安全串联、端到端流程、RAG vs MCP 边界（达标线②）。
- **L4（场景，20 分中 ≥16）**：能设计"基础保险知识库"架构，并纠正"MCP 误用"误区。
- **总分 ≥104（80%）** 达标；**≥117（90%）** 优秀。

> 两道「面试达标线」对应手册 s13 / s14：① Function Calling / MCP / 普通 API 三者差异与适用；② W2 整体产出如何串成"基础保险知识库"。
