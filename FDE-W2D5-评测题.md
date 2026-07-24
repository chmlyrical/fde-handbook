# FDE W2D5 评测题 · MCP Server

> 配套《FDE-W2D5-MCP Server-学习手册.html》使用。共 13 题，分 L1 基础 / L2 进阶 / L3 深度 / L4 场景四档。
> 候选人背景：36 岁，Java + 大数据 + 医疗保险。场景统一围绕医疗保险 / 特药理赔 / 保险知识库。
> 评分建议：每题满分 10 分；参考答案折叠在 `<details>` 中，先自答再对照。

---

## L1 基础（概念识记，每题 10 分）

### Q1. 一个 MCP Server 通常由哪些能力组成？用医保场景举例。
- **考察点**：Tools/Resources/Prompts 三类能力、场景映射。
- **评分维度**：①三类能力齐全；②医保例子贴切且分类正确；③是否点出 Server 是"能力提供方"。
<details>
<summary>参考答案</summary>

MCP Server 暴露三类能力：
- **Tools**：模型触发的动作（保单查询、OCR、规则校验）；
- **Resources**：只读数据/上下文（保单原文、特药目录）；
- **Prompts**：用户选的提示词模板（理赔审核 SOP）。

Server 本质是把内部业务系统（保单库、OCR 引擎、规则引擎）包成标准 MCP 接口，Client 无需懂内部实现即可用。
</details>

### Q2. 用 FastMCP 写出最小 MCP Server 骨架，并说明 transport 怎么选。
- **考察点**：FastMCP 用法、@mcp.tool、transport 选型。
- **评分维度**：①骨架正确（建实例+装饰器+run）；②transport 名对照 2025-11-25（stdio/streamable-http）；③选型依据（Local vs Remote）。
<details>
<summary>参考答案</summary>

```python
from mcp.server.fastmcp import FastMCP
mcp = FastMCP("insurance-claim-server")

@mcp.tool()
def query_policy(policy_no: str) -> dict:
    """按保单号查询保单信息。"""
    return {"policy_no": policy_no, "status": "active"}

if __name__ == "__main__":
    mcp.run(transport="stdio")  # 或 "streamable-http"
```

transport 选型：Local 用 `stdio`（无端口、安全）；Remote 跨网络用 `streamable-http`（需鉴权）。注意固定验证版 2025-11-25 用 Streamable HTTP，非已弃用 sse。
</details>

### Q3. 定义一个 MCP Tool 有哪三步？各自作用是什么？
- **考察点**：Tool 三段式（schema/实现/注册）。
- **评分维度**：①三步完整；②每步作用清晰；③是否点出"注册后出现在 tools/list"。
<details>
<summary>参考答案</summary>

1. **schema（输入契约）**：由类型注解+docstring 生成 inputSchema（参数名/类型/必填/描述），模型靠它选参填参；
2. **实现（业务逻辑）**：函数体调内部系统，返回结构化结果；
3. **注册（暴露）**：@mcp.tool() 装饰即注册，握手后出现在 tools/list，Client 才能发现并调用。

模型靠 description 决定"调不调"，靠 schema 决定"怎么填参"。
</details>

### Q4. 什么是 Resource？用 @mcp.resource 写一个名为"保单文档"的示例。
- **考察点**：Resource 定义、URI、只读上下文语义。
- **评分维度**：①Resource 是只读数据/上下文；②示例含 URI 与返回值；③是否说明由 Host 读入而非模型调用。
<details>
<summary>参考答案</summary>

Resource 是只读数据/上下文，由应用（Host）通过 `resources/read` 按 URI 读入模型上下文，模型不"调用"它。

```python
@mcp.resource("policy://{policy_no}/document")
def policy_document(policy_no: str) -> str:
    """返回某保单的原始条款文本（只读上下文）。"""
    return policy_store.load_text(policy_no)
```

URI 作句柄（如 `policy://PA2024-123456/document`），Host 读取后塞进上下文让模型"看原文"。
</details>

---

## L2 进阶（理解应用，每题 10 分）

### Q5. 写出"保单查询 Tool"的完整示例，并说明返回与错误处理要点。
- **考察点**：Tool 实战编码、结构化返回、错误路径。
- **评分维度**：①示例含参数/返回/查询；②错误处理结构化（如 found 标志）；③是否点出description 由 docstring 生成。
<details>
<summary>参考答案</summary>

```python
@mcp.tool()
def query_policy(policy_no: str, fields: list[str] | None = None) -> dict:
    """
    按保单号查询保单信息。
    :param policy_no: 保单号，如 'PA2024-123456'
    :param fields: 可选，指定返回字段
    :return: 含 status/limit/insured 的字典
    """
    rec = policy_db.get(policy_no)
    if not rec:
        return {"found": False, "policy_no": policy_no}   # 结构化错误，不抛裸异常
    return {"found": True, **rec}
```

要点：返回稳定可解析（dict）；错误走结构化返回（found=False）而非未捕获异常；docstring 即 description，写清格式示例提升调用准确率。
</details>

### Q6. OCR Tool 应该注意什么？它该标 readOnlyHint 吗？
- **考察点**：OCR Tool 性质、引用溯源、hint 语义。
- **评分维度**：①OCR 是只读识别→readOnlyHint=true；②引用溯源意义；③误差/超时处理。
<details>
<summary>参考答案</summary>

OCR 本身只识别不写业务库，标注 `readOnlyHint=true`（仅提示）。注意：
- **引用溯源**：保留原文与识别结果对应，供理赔结论回溯，避免模型凭空编诊断；
- **误差**：OCR 有错，结果要带置信度/原文，下游可质疑而非当事实；
- **超时/分片**：大图/PDF 设超时，避免卡死 Server；
- **hint 不是安全**：真写临时文件仍受 Server 文件系统权限约束（D4 s12）。
</details>

### Q7. inputSchema 设计有哪些要点？为什么用 Literal 锁枚举？
- **考察点**：schema 质量、枚举约束、调用准确率。
- **评分维度**：①列出描述/类型/必填可选/枚举等要点；②说明 Literal 防非法值；③长文本走 Resource。
<details>
<summary>参考答案</summary>

要点：
- 描述写清（含格式示例）→ 模型选参填参准；
- 类型精确（str/int/bool/Pydantic）；
- 必填（无默认）vs 可选（有默认）分明；
- 非法值用 `Literal` 枚举锁死（如地区限 ["北京","上海","全国"]），防模型传错；
- 超长自由文本走 Resource，Tool 参数应精简。

schema 是"模型与 Tool 的契约"，质量直接决定调用准确率，生产当接口契约评审。
</details>

### Q8. 什么是 Prompt 模板？它和 Tool 的触发方式有何不同？
- **考察点**：Prompt 定义、user-controlled、与 Tool 触发区别。
- **评分维度**：①Prompt 是用户选的模板（带参）；②user-controlled vs model-controlled；③可固化 SOP。
<details>
<summary>参考答案</summary>

Prompt 用 @mcp.prompt() 声明，是**用户**在 Host 里点选并填参的提示词模板（user-controlled），非模型推理中自动调用。作用是把"理赔审核 SOP"等专家经验固化成可复用范式，保证审核口径一致（合规需要），降低前端拼 prompt 成本。

与 Tool 区别：Tool 由模型在推理中自主决定调用（model-controlled，是动作）；Prompt 由用户主动选择（是模板）。模板里要内置"不确定就拒答"约束。
</details>

---

## L3 深度（原理与安全，每题 10 分）

### Q9. 详细讲清 Resource 与 Tool 的分工边界（达标线②），并给判定标准。
- **考察点**：Tool vs Resource 三维区分、判定标准。
- **评分维度**：①触发方/副作用/本质三维；②给出明确判定标准；③医保例子归类正确。
<details>
<summary>参考答案</summary>

| 维度 | Tool | Resource |
|---|---|---|
| 本质 | 动作（执行函数） | 数据（只读上下文） |
| 触发方 | 模型（自主决定） | 应用/Host（读入上下文） |
| 副作用 | 可有 | 无 |
| 医保例 | 查保单/OCR/校验 | 保单原文/特药目录 |

判定标准：**要执行动作/产生副作用 → Tool；只是把知识放进上下文 → Resource**。

混淆后果：把 Resource 当 Tool 会丢失动作语义与鉴权边界；反之模型无法触发。医保落点：查询/OCR/校验=Tool；保单原文/目录=Resource；审核 SOP=Prompt。
</details>

### Q10. 规则校验 Tool 为什么要求"低随机 + 可解释 reason + 引用"？
- **考察点**：确定性逻辑、可解释性、引用溯源、规则外置。
- **评分维度**：①低随机(T=0)保证确定；②reason 可解释供拒付说明；③引用溯源+规则外置。
<details>
<summary>参考答案</summary>

- **低随机**：规则校验是确定性问题，T=0 保证同样输入同样输出，可复现、可审计；
- **可解释 reason**：拒付/赔付都要能讲清依据（对应哪条特药目录/适应症），向客户与监管解释；
- **引用溯源**：reason 要能回溯具体条款，配合 Resource 原文；
- **规则外置**：规则放 DB/配置，Tool 只"查+算"，改规则不发版；
- **区分"不符合"与"信息不足"**：避免把"查不到"当"不报销"导致错拒。
</details>

### Q11. MCP Server 的可观测与错误处理要做到哪几点？医保为何特别重要？
- **考察点**：结构化错误、超时降级、日志审计、合规。
- **评分维度**：①三件套齐全；②fail visible 不静默；③医保合规留痕。
<details>
<summary>参考答案</summary>

三件套：
1. **结构化错误**：返回 `{"ok":false,"error":...,"code":...}` 而非抛裸异常；
2. **超时与降级**：内部查询/OCR 设超时，失败返回降级或显式错误；
3. **日志与 Trace**：记录入参/出参/耗时/错误。

医保特殊：涉及钱与客户隐私，每次 Tool 调用要**可回溯、可审计**（谁、查了哪个保单），异常要 **fail visible**（明确报错）而非静默返回错数据导致错赔；审计日志需脱敏。
</details>

---

## L4 场景（综合实战，每题 10 分）

### Q12. 场景题：用 Spring AI（Java）为现有医保 Spring 微服务暴露 MCP 能力，写出"保单查询"Tool 并说明落地优势。
- **考察点**：Java/Spring AI 落地、@Tool、复用现有服务。
- **评分维度**：①@Tool 示例正确；②说明复用现有 Java 资产；③保留原有权限/审计。
<details>
<summary>参考答案</summary>

```java
@Tool(description = "按保单号查询保单状态与额度")
public Map<String,Object> queryPolicy(@ToolParam("保单号") String policyNo) {
    return policyService.query(policyNo);   // 复用现有 Java 保单服务
}
```

优势：
- 直接复用医保现有 Spring 微服务（保单库、规则引擎），把"已有方法"用 @Tool 包一层即变 MCP 能力，几乎零重写；
- Python SDK 适原型，Java 适生产主力栈；
- 复用时要**保留原有权限/审计**，别因包了 MCP 绕过内控。

B站 Spring AI 第49-56集（BV1GfyGBqEm6）讲透 Java 侧全套。
</details>

### Q13. 场景题：面试官让你现场把"特药理赔审核"拆成 MCP 能力，你会怎么拆？哪些用 Tool、哪些用 Resource、哪些用 Prompt？
- **考察点**：综合能力拆分、三维边界判断、医保落地。
- **评分维度**：①拆分合理（查询/OCR/校验/文档/模板）；②Tool/Resource/Prompt 归类正确；③说明为何这样分。
<details>
<summary>参考答案</summary>

拆分与归类：
- **Tool（模型触发、动作）**：
  - 保单查询 Tool（查状态/额度/被保人）
  - 病历 OCR Tool（图片→结构化文本，readOnlyHint=true）
  - 特药规则校验 Tool（诊断+药品→是否报销+理由，低随机、带引用）
- **Resource（只读上下文）**：
  - 保单文档 Resource（policy://{no}/document 原文）
  - 特药目录 Resource（适应症/限制条文）
- **Prompt（用户选模板）**：
  - 理赔审核话术模板（固化 SOP，内置"不确定就拒答"）

理由：凡是"执行动作/产生副作用或需模型主动决策"→Tool；"静态知识进上下文"→Resource；"专家经验复用"→Prompt。这样拆分调用准确率高、权限边界清、易评测。
</details>

---

## 评分汇总表

| 题号 | 档位 | 考察主题 | 满分 | 自评 | 考官评 |
|---|---|---|---|---|---|
| Q1 | L1 | Server 能力组成 | 10 | | |
| Q2 | L1 | FastMCP 骨架+transport | 10 | | |
| Q3 | L1 | Tool 三段式 | 10 | | |
| Q4 | L1 | Resource 定义 | 10 | | |
| Q5 | L2 | 保单查询 Tool 实战 | 10 | | |
| Q6 | L2 | OCR Tool 要点 | 10 | | |
| Q7 | L2 | inputSchema 设计 | 10 | | |
| Q8 | L2 | Prompt 模板 | 10 | | |
| Q9 | L3 | Resource vs Tool 分工 | 10 | | |
| Q10 | L3 | 规则校验 Tool 设计 | 10 | | |
| Q11 | L3 | 可观测与错误处理 | 10 | | |
| Q12 | L4 | Spring AI 落地 | 10 | | |
| Q13 | L4 | 能力拆分场景 | 10 | | |
| **合计** | | | **130** | | |

### 达标线
- **L1+L2（基础进阶，80 分）**：必拿满。能写出 Tool 三段式、Resource/Prompt 示例、schema 设计、OCR 要点。
- **L3（深度，30 分中 ≥24）**：必须讲清 Resource vs Tool 分工（达标线②）、规则校验设计、可观测三件套。
- **L4（场景，20 分中 ≥16）**：能用 Spring AI 落地并现场拆分医保能力。
- **总分 ≥104（80%）** 达标；**≥117（90%）** 优秀。

> 两道「面试达标线」对应手册 s13 / s14：① 怎么定义一个 MCP Tool；② Resource 和 Tool 的分工。
