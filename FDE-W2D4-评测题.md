# FDE W2D4 评测题 · MCP 核心

> 配套《FDE-W2D4-MCP核心-学习手册.html》使用。共 13 题，分 L1 基础 / L2 进阶 / L3 深度 / L4 场景四档。
> 候选人背景：36 岁，Java + 大数据 + 医疗保险。场景统一围绕医疗保险 / 特药理赔 / 保险知识库。
> 评分建议：每题按"考察点"给分，满分 10 分；参考答案折叠在 `<details>` 中，先自答再对照。

---

## L1 基础（概念识记，每题 10 分）

### Q1. 一句话说明 MCP 解决什么问题？它和 Function Calling 的定位差异是什么？
- **考察点**：对 MCP 价值与边界的准确理解（N×M → N+M 解耦、标准协议 vs 单点能力）。
- **评分维度**：①是否点出"标准协议/生态"而非"更强能力"；②是否讲清与 Function Calling 的层次差异；③是否用"解耦集成"表达。
<details>
<summary>参考答案</summary>

MCP（Model Context Protocol）是 LLM 接入工具/数据的**标准协议**，核心价值是把"工具/数据提供方"和"模型/应用方"解耦：工具按 MCP Server 标准实现一次，任何兼容 Client 都能用，将 N 个模型 × M 个工具的 N×M 集成降到 N+M。

与 Function Calling 的差异：Function Calling 是"模型调用函数的**能力**"（单点、各家私有）；MCP 是"工具接入的**协议/生态**"，管传输（stdio/HTTP）、动态发现（tools/list）与三类原语（Tools/Resources/Prompts）。二者不矛盾——MCP 之上仍可承载 Function Calling 式的调用。类比：Function Calling 是"会拧螺丝"，MCP 是"统一的螺丝刀接口标准"。
</details>

### Q2. 画出 Host / Client / Server 三层模型，并说明各自职责与对应关系。
- **考察点**：三层角色定义、1:1 连接约束、Host 权限归属。
- **评分维度**：①三个角色职责是否准确；②是否点出 Client 与 Server 的 1:1；③是否说明 Host 负责信任/授权。
<details>
<summary>参考答案</summary>

- **Host**：承载 LLM 交互的宿主应用（含 UI、会话、用户授权与信任决策）。如自研"理赔审核 Agent"。
- **Client**：Host 内部为**每一个** Server 建立的 1:1 连接通道，负责协议转发。
- **Server**：暴露 Tools/Resources/Prompts 能力的服务方。

关系：`Host（1）⇄ 多个 Client（每 Server 一个）⇄ 多个 Server`。一个 Client 只连一个 Server；Server 只跟 Client 通信，不直接认识 Host；信任/授权决策在 Host 层。

医保例子：理赔应用(Host) 内有"保单 MCP Client""OCR MCP Client"，分别连 保单 Server 与 OCR Server。
</details>

### Q3. 初始化握手（initialize）做哪几件事？顺序是什么？
- **考察点**：握手三步、版本协商、能力协商、先握手后调用。
- **评分维度**：①三步顺序正确；②是否说明交换了"版本+能力"；③是否点出"未握手不能调用工具"。
<details>
<summary>参考答案</summary>

顺序：
1. Client → `initialize{ protocolVersion, capabilities, clientInfo }`
2. Server → 返回 `{ protocolVersion, capabilities, serverInfo }`（协商出的版本与双方能力）
3. Client → `notifications/initialized` 通知，握手完成

要点：版本是**协商**出的双方支持的最高版本（固定验证版 `2025-11-25`）；能力是双方各自声明支持的原语（tools/resources/prompts 等）。**任何工具调用都必须发生在握手完成之后**，否则是协议错误。
</details>

### Q4. MCP 有哪两种标准 Transport？各自适用 Local 还是 Remote？
- **考察点**：stdio 与 Streamable HTTP 的区分与选型。
- **评分维度**：①两种 Transport 名称准确；②是否对应 Local/Remote；③是否点出 HTTP 需鉴权、stdio 无端口。
<details>
<summary>参考答案</summary>

- **stdio**：本地子进程，经 stdin/stdout 传 JSON-RPC，**无网络端口**，适用 **Local**（同机工具，如本地 OCR、本地文件读取）。安全隔离好、启动快。
- **Streamable HTTP**：基于 HTTP，服务端可用 SSE 流式推送，适用 **Remote**（跨网络独立部署的 Server）。需补 TLS、认证、Session 管理。

选型：能本地就 stdio；必须跨网络才上 Streamable HTTP 并加鉴权。注意固定验证版 2025-11-25 规范推荐的是 Streamable HTTP（非旧版 SSE Transport）。
</details>

---

## L2 进阶（理解应用，每题 10 分）

### Q5. 解释 Tools / Resources / Prompts 三类原语的区别，并各举一个医保例子。
- **考察点**：三类原语语义、控制方（model/application/user）、有/无副作用。
- **评分维度**：①三者定义准确；②是否点出控制方差异；③医保例子贴切且区分正确。
<details>
<summary>参考答案</summary>

| 原语 | 控制方 | 是什么 | 副作用 | 医保例子 |
|---|---|---|---|---|
| Tools | 模型 | 可执行函数 | 通常有 | 保单查询 Tool、OCR Tool、规则校验 Tool |
| Resources | 应用 | 只读数据/文档 | 无 | 保单 PDF、特药目录表 |
| Prompts | 用户 | 提示词模板 | 无 | "理赔审核"模板 |

核心区别：Tool 是"动作"（模型自主决定调用）；Resource 是"上下文"（应用注入）；Prompt 是"模板"（用户选择）。一句话：Tool 做、Resource 给、Prompt 套。
</details>

### Q6. 什么是 Tool Discovery？它给工程带来什么好处？
- **考察点**：tools/list 机制、模型据 schema 调用、解耦红利。
- **评分维度**：①发现流程正确；②是否说明模型靠 description 选 Tool；③是否点出"加工具零改造"。
<details>
<summary>参考答案</summary>

握手后，Client 调 `tools/list` 拉取 Server 暴露的全部 Tool（含 name、description、inputSchema）；模型据描述与 schema 决定调用哪个，再用 `tools/call{name, arguments}` 执行。

好处：Server 新增/修改 Tool，只要符合 schema，前端 Agent 下次 list 自动发现，**无需改 Host 代码**——这是 MCP 解耦 N×M 的关键红利。

工程注意：① Tool 的 `description` 质量直接决定调用准确率；② `tools/list` 有成本，生产应缓存并按 Server 版本失效；③ 相似 Tool 多易选错，需评测。
</details>

### Q7. 固定验证的协议版本号是多少？版本协商取什么值？
- **考察点**：具体版本号记忆、协商规则（交集最大值）。
- **评分维度**：①版本号准确（2025-11-25）；②协商规则正确（非客户端单方面）；③是否提到对齐版本实现。
<details>
<summary>参考答案</summary>

固定验证版本为 **`2025-11-25`**，覆盖 Transport、三类原语、Tool Annotation、Session 生命周期与 `Mcp-Session-Id` 头。

版本协商：取**双方都支持的最高版本**（Client 报自己支持的最高、Server 回 ≤ 该值且自身支持的最高），是协商出的交集最大值，不是 Client 单方面指定。若 Server 版本过低不支持，握手失败（应 fail-fast）。实现时把版本钉在配置里并校验 Server 返回值，避免跨版本套用字段。
</details>

### Q8. HTTP Transport 下如何保持"同一会话"的连贯性？
- **考察点**：Mcp-Session-Id、有状态交互、生命周期。
- **评分维度**：①点出 Mcp-Session-Id；②是否说明它在初始化时下发、后续请求携带；③是否提到超时回收与监控。
<details>
<summary>参考答案</summary>

Streamable HTTP 下，Server 在初始化响应中下发 `Mcp-Session-Id`，后续每个请求都必须带这个头，Server 据此关联同一会话的状态（已加载资源、上下文）。stdio 无此概念（进程级会话）。

工程上：会话要设空闲超时与上限，异常断开（子进程被杀/连接中断）要能安全释放，生产对会话数做监控防止悬挂会话拖垮 Server。
</details>

---

## L3 深度（原理与安全，每题 10 分）

### Q9. 详细论证：为什么 Tool Annotation 不能作为真实鉴权依据？正确做法是什么？
- **考察点**：Annotation 是 hint、可被谎报、安全边界归属。
- **评分维度**：①是否点出 Annotation 是 Server 自报的提示；②是否说明可被谎报/遗漏；③正确做法（Host/Client 权限 + Server 真实访问控制）。
<details>
<summary>参考答案</summary>

Tool Annotation（readOnlyHint / destructiveHint / idempotentHint / openWorldHint）在规范里明确定义为 **hint（提示）**，不是强制约束：
- 它由 Server **自报**，恶意或有缺陷的 Server 可以谎报（标 readOnly 实际写库）或漏填；
- 因此不能用于**安全边界决策**（能否调用、要不要鉴权、是否允许写）。

正确做法：鉴权落在 **Host/Client 的权限策略 + Server 自身的真实访问控制**（如当前用户角色、保单状态、RBAC）。Annotation 仅用于 **UX 提示**（如 destructive 工具弹确认框）。原则："默认拒绝 + 显式授权"，绝不默认信任 Annotation。

医保例子：是否允许调用"提交理赔结论"Tool，由 Host 权限配置决定，即便 Server 标成 readOnly 也要独立鉴权。
</details>

### Q10. OAuth 在 MCP 里的适用边界是什么？它和"Tool 级业务授权"是什么关系？
- **考察点**：OAuth 管端点准入（门禁）、不替代细粒度授权、stdio 无需。
- **评分维度**：①是否点出 OAuth 适用 Remote/HTTP；②是否区分"传输授权"与"业务授权"；③是否说明 stdio 一般无需。
<details>
<summary>参考答案</summary>

OAuth 适用于 **Remote / Streamable HTTP** 的 MCP Server，保护的是**传输层端点准入**（"谁能连 Server"），属门禁级。

边界：
- OAuth **不替代** Tool 级 / 数据级细粒度业务授权（"能否调某 Tool、能否查某客户保单"）—那是 Host/Server 内部权限策略的事；
- **stdio（Local）一般无需 OAuth**—无网络面，靠进程/文件系统权限隔离即可；硬套是过度设计。

医保例子：中心化"特药目录 MCP 服务"用 HTTP+OAuth，只有持有效 token 的理赔系统能连；但"能否查某客户保单"仍由 Server 按用户身份判定。
</details>

### Q11. MCP Server 来源不可信会带来什么风险？工程上怎么做来源信任管控？
- **考察点**：信任模型、白名单/最小权限/沙箱/能力审查。
- **评分维度**：①是否说明"模型不能自动连陌生 Server"；②是否列出至少 3 项工程做法；③是否提到连接后能力审查。
<details>
<summary>参考答案</summary>

风险：恶意/有缺陷的 Server 可执行危险动作、读敏感数据、谎报能力（配合 Q9 的 Annotation 谎报形成组合风险）。让模型"发现就自动连"是危险设计。

工程做法：
1. **来源白名单**：只连预核准 Server（域名/包签名校验）；
2. **最小权限**：Server 进程只给必要文件系统/网络/DB 权限；
3. **首次人工确认**：展示将暴露的能力再授权；
4. **沙箱隔离**：不可信 Server 放容器，限制资源触达；
5. **连接后能力审查**：拦截 tools/list 暴露的高危 Tool。

医保：接第三方"医保政策爬虫 MCP Server"前加白名单 + 沙箱，限制只能访问公开政策站点，不能碰内网保单库。
</details>

---

## L4 场景（综合实战，每题 10 分）

### Q12. 场景题：你要为"特药理赔审核"设计 MCP 架构。请说明会拆几个 Server、各用什么 Transport、如何做安全。
- **考察点**：综合运用三层模型/原语/Transport/信任，医保场景落地。
- **评分维度**：①Server 拆分合理（保单/OCR/规则）；②Transport 选型有依据；③安全（鉴权/信任/Annotation 定位）到位。
<details>
<summary>参考答案</summary>

**拆分**（按能力解耦）：
- 保单查询 MCP Server（查保单状态/额度）— 内部数据，用 **stdio** 或内网 HTTP；
- OCR MCP Server（识别病历/发票）— 本地算力，用 **stdio**；
- 特药规则校验 MCP Server（跑医保目录/适应症规则）— 可共享，用 **Streamable HTTP**（远程，配 OAuth 门禁）；
- （可选）保单文档 Resource Server 提供原始 PDF 上下文。

**原语分工**：保单查询/OCR/规则校验做成 **Tools**（模型控制）；保单原文、特药目录做成 **Resources**（应用注入）；"标准理赔审核话术"做成 **Prompt 模板**（用户选）。

**安全**：
- 远程规则 Server 用 OAuth 管端点准入，Tool 级权限由 Host 按用户角色/保单状态判定（不看 Annotation）；
- 所有 Server 走来源白名单 + 最小权限；第三方 Server 沙箱运行；
- Tool Annotation 仅用于 UX（destructive 的"提交结论"Tool 弹确认），不用于鉴权。
</details>

### Q13. 场景题：面试官追问"你说 MCP 比 Function Calling 安全，那如果我用一个标了 readOnlyHint 的第三方 Tool 去查客户隐私，是不是就安全了？"
- **考察点**：识破"Annotation=安全"陷阱、回到真实权限模型、沟通表达。
- **评分维度**：①直接指出陷阱（Annotation 不可信）；②给出正确安全模型；③结合医保隐私合规表达。
<details>
<summary>参考答案</summary>

**直接否定**：不是。readOnlyHint 只是 Server 自报的提示，可被谎报或漏填——标了 readOnly 的 Tool 完全可能偷偷写库或外传隐私。MCP 并不比 Function Calling"天生更安全"，它只是把传输/发现标准化，安全边界仍要自己建。

正确做法：
1. **不靠 Annotation 做安全决策**，隐私访问的鉴权由 Host/Server 真实权限系统判定（用户角色、最小授权、审计日志）；
2. **来源信任**：该第三方 Server 必须在白名单内、沙箱运行、能力审查通过；
3. **传输层**：若为 Remote，OAuth 管门禁，但门禁 ≠ 业务授权；
4. **合规**：客户隐私查询需显式授权 + 全程审计，默认拒绝。

一句话：Annotation 是 UX 提示不是安全保证，"标了 readOnly"不等于"安全"。
</details>

---

## 评分汇总表

| 题号 | 档位 | 考察主题 | 满分 | 自评 | 考官评 |
|---|---|---|---|---|---|
| Q1 | L1 | MCP 价值与定位 | 10 | | |
| Q2 | L1 | 三层模型 | 10 | | |
| Q3 | L1 | 初始化握手 | 10 | | |
| Q4 | L1 | 两种 Transport | 10 | | |
| Q5 | L2 | 三类原语 | 10 | | |
| Q6 | L2 | Tool Discovery | 10 | | |
| Q7 | L2 | 协议版本 | 10 | | |
| Q8 | L2 | Session 与 Mcp-Session-Id | 10 | | |
| Q9 | L3 | Annotation 不鉴权 | 10 | | |
| Q10 | L3 | OAuth 边界 | 10 | | |
| Q11 | L3 | Server 来源信任 | 10 | | |
| Q12 | L4 | 架构场景 | 10 | | |
| Q13 | L4 | 安全追问场景 | 10 | | |
| **合计** | | | **130** | | |

### 达标线
- **L1+L2（基础进阶，80 分）**：必须拿满。能讲清 Host/Client/Server 模型、初始化握手、三种原语、Transport 选型、Tool Discovery、版本号。
- **L3（深度，30 分中 ≥24）**：必须能论证「Tool Annotation 不能当鉴权」「OAuth 边界」「Server 来源信任」——这是面试区分度所在。
- **L4（场景，20 分中 ≥16）**：能结合医保场景落地架构，并识破"Annotation=安全"陷阱。
- **总分 ≥104（80%）** 为达标；**≥117（90%）** 为优秀。

> 两道「面试达标线」对应手册 s13 / s14：① Host/Client/Server + 初始化握手；② MCP vs Function Calling + Annotation 为何不鉴权。
