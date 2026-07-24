# FDE 学习路径 v4 · 8周面试冲刺版

> **用户画像**：36 岁，Java + 大数据 + 医疗保险行业经验
> **求职目标**：企业 GenAI 解决方案架构师、高级 AI 应用工程师、AI 应用技术负责人、医疗保险 AI 负责人、本地或低出差 AI Deployment Engineer
> **时间投入**：每天 4 小时，每周约 28 小时，共 8 周
> **核心要求**：第 4 周结束时具备真实面试能力，第 8 周完成高级岗位强化
> **技术原则**：删除所有 OpenAI、GPT、OpenAI Agents SDK 相关内容，改用 Qwen / DeepSeek / Claude

> **资源使用原则**：视频按需看对应章节（别从头刷）· 官方文档优先（MCP/Spring AI/vLLM 迭代快，文档最准）· B 站 1.5-2x 倍速 · 看完一节立刻动手写代码

---

## 一、总体调整原则

### 1. 第 4 周必须达到可面试状态

第 4 周结束时，至少完成：

1. 一个可运行的企业保险知识库 RAG 原型
2. 一个可运行的特药理赔 Agent 核心流程
3. 一份简历终版
4. 两个完整项目故事
5. 两张架构图
6. 一套 RAG 与 Agent 高频面试题
7. 一轮模拟面试
8. 第一批正式投递

第 4 周之后继续补充平台化、安全、评测、推理服务和 Java 工程化，用于冲刺更高薪岗位。

### 2. 技术主线收敛

Python · LangGraph · LangChain · Spring AI · Elasticsearch · PostgreSQL · Redis · MCP · LiteLLM · vLLM · Qwen · DeepSeek · Claude · Docker · Docker Compose · Langfuse

### 3. 深度分级

| 级别 | 要求 |
|---|---|
| A | 面试核心能力，必须掌握；涉及工程实现必须亲自编码 |
| B | 跑通最小 Demo，能解释架构、限制和适用场景 |
| C | 能解释原理、完成选型并说明取舍，无需完整实现 |

### 4. 每周必须保留四类证据

① 可运行代码　② 测试或评测报告　③ ADR 或架构决策记录　④ 一次失败案例及修复过程

---

## 二、8 周总览

| 周 | 核心主题 | 关键产出 | 面试状态 |
|---|---|---|---|
| W1 | 大模型应用基础、模型调用、结构化输出、轻量模型网关 | 多模型调用服务、Prompt 模板、模型选型文档 | 建立基础 |
| W2 | Tool Calling、MCP、Document AI、基础 RAG | OCR Tool、MCP Server/Client、基础知识库 | 可讲工具与 MCP |
| W3 | 企业 RAG、AI 数据工程、混合检索、评测 | 企业保险知识库原型 | 可面试 RAG 岗 |
| W4 | LangGraph Agent、HITL、Trace、简历与模拟面试 | 特药理赔 Agent 核心版、简历、架构图 | **★ 正式开始面试** |
| W5 | Agent Harness、Text2SQL、业务状态与可靠性 | Harness 企业扩展、三路数据路由 | 提升 Agent 深度 |
| W6 | 评测、LLMOps、安全、Memory | Agent 评测、安全加固、版本与回归 | 冲刺架构岗 |
| W7 | 模型网关平台化、Spring AI、部署交付 | 企业 Agent 平台核心模块 | 冲刺负责人岗 |
| W8 | vLLM、容量成本、系统设计、作品集与高级面试 | 三个作品整合、FDE 交付包、终轮模拟面试 | **★ 集中投递高薪** |

---

## 三、第 1-4 周：面试就绪阶段（详细日级任务）

### Week 1：大模型基础、模型调用、结构化输出、轻量模型网关

**本周目标**：建立大模型应用开发基础，能够独立调用多个非 OpenAI 模型，理解结构化输出、模型选型、成本、上下文和基础网关设计。

#### Day 1：大模型核心基础 [A] · 4h
- Transformer 总体架构、Self-Attention 和 Multi-Head Attention
- Token、Tokenizer、Embedding
- 上下文窗口、KV Cache、Prefill 与 Decode
- 推理模型与普通对话模型的差异

**面试要求**：能解释一次模型请求从 API 到返回 Token 的完整过程；能说明上下文长度、延迟和成本的关系。

**资源：**
- [B站 李宏毅生成式AI导论2025（第10-11讲 Transformer）](https://www.bilibili.com/video/BV1mXpuz7E9v/)
- [B站 李宏毅机器学习2025（第1/3/4讲）](https://www.bilibili.com/video/BV1aiADewEBC/)
- [人大《大语言模型》教材](https://llmbook-zh.github.io/)

#### Day 2：采样参数与结构化输出 [A] · 4h
- Temperature、Top P、Top K、Stop Sequences
- JSON Schema、Structured Output、输出校验和失败重试
- 模型幻觉与拒答

**实战**：使用 Qwen/DeepSeek/Claude API 返回结构化 JSON；用 Pydantic 或 JSON Schema 校验输出；对格式错误重试或降级。

**资源：**
- [B站 李宏毅 第2讲：今日生成式AI厉害在哪](https://www.bilibili.com/video/BV1mXpuz7E9v/)
- [通义千问 API 文档](https://help.aliyun.com/zh/model-studio/developer-reference/use-qwen-by-calling-api)
- [DeepSeek API 文档](https://api-docs.deepseek.com/)
- [Claude 结构化输出文档](https://docs.anthropic.com/claude/docs/build-with-claude/structured-output)

#### Day 3：模型选型与技术边界 [A][C] · 4h
- 决策树：何时优化 Prompt / 用 RAG / 用规则引擎 / 微调 / 用小模型 / 本地模型 / 闭源模型
- 选型维度：准确率、延迟、Token 成本、上下文长度、数据安全、部署复杂度、并发吞吐、多模态

**资源：**
- [B站 李宏毅 第7讲：DeepSeek-R1 深度思考](https://www.bilibili.com/video/BV1aiADewEBC/)
- [智谱 API 文档](https://open.bigmodel.cn/dev/api)

#### Day 4：多模型统一接入 [A] · 4h
- 实现轻量 Model Adapter：统一请求结构、Provider Adapter、模型路由
- 超时、重试、Fallback、Token 统计、成本记录、Trace ID、流式输出

**建议模型**：Qwen、DeepSeek、Claude、本地 Ollama 模型

**资源：**
- [通义千问 API](https://help.aliyun.com/zh/model-studio/developer-reference/use-qwen-by-calling-api)
- [DeepSeek API](https://api-docs.deepseek.com/)
- [Claude API](https://docs.anthropic.com/en/api/getting-started)
- [LiteLLM 文档（统一接口参考）](https://docs.litellm.ai/)

#### Day 5：Prompt 工程 [A] · 4h
- System Prompt、Zero-shot、Few-shot、Prompt 模板、Prompt 版本、输出约束
- Prompt Injection 基础、Prompt 测试集

**实战**：结合 YOYO 项目 —— 场景识别 Prompt、Few-shot 样本选择方法、JSON 输出约束、错误场景分类、98% 正确率的评测口径说明。

**资源：**
- [B站 李宏毅 第5-6讲：训练不了AI你可以训练你自己](https://www.bilibili.com/video/BV1mXpuz7E9v/)
- [Anthropic Prompt Engineering 指南](https://docs.anthropic.com/claude/docs/prompt-engineering)

#### Day 6：本地模型体验 [B] · 4h
- Ollama 启动 Qwen 或 DeepSeek 蒸馏模型
- 理解量化、测试不同上下文长度
- 比较本地模型和 API 模型的延迟、成本和效果，记录测试环境和结果

**资源：**
- [Ollama 官网](https://ollama.com/)
- [Ollama Qwen3 模型](https://ollama.com/library/qwen3)
- [Ollama DeepSeek-R1](https://ollama.com/library/deepseek-r1)
- [vLLM 文档（概念了解）](https://docs.vllm.ai/)

#### Day 7：整合与面试准备 · 4h
- 轻量模型网关 v1
- Prompt 模板管理代码
- 模型选型决策树
- YOYO 项目技术复盘初稿
- Java 恢复 2h：Spring Boot 项目骨架、REST API、依赖注入

**资源：**
- [Spring Boot 官网](https://spring.io/projects/spring-boot)
- [B站 Spring AI 全套（第1-17集 入门）](https://www.bilibili.com/video/BV1GfyGBqEm6/)

**📦 本周产出**：轻量模型网关 v1 · Prompt 模板管理 · 模型选型决策树 · YOYO 复盘初稿 · Java 骨架恢复

---

### Week 2：Tool Calling、MCP、Document AI、基础 RAG

**本周目标**：掌握工具调用、MCP 和文档处理，跑通一个医疗保险知识库基础版。

#### Day 1：Tool Calling [A] · 4h
- Tool Schema、参数校验、工具注册
- 多轮工具调用、多工具路由、工具超时、工具失败重试
- 工具返回结果标准化

**资源：**
- [阿里云百炼 Qwen Function Calling 文档（含Python/Node示例）](https://help.aliyun.com/zh/model-studio/qwen-function-calling)
- [DeepSeek Function Calling 指南](https://api-docs.deepseek.com/zh-cn/guides/function_calling)
- [Claude Tool Use 文档](https://docs.anthropic.com/en/docs/build-with-claude/tool-use)

#### Day 2：医疗 OCR Tool 与 Document AI [A][B] · 4h
- 只跑通一类医疗材料或发票：OCR 文本识别、坐标返回、字段结构化抽取
- OCR 置信度、字段置信度、低置信度人工复核
- 页面原图与字段坐标关联

**评测**：字符准确率、字段 Precision/Recall/F1、人工修改率。跨页字段、复杂表格和手写体只做设计。

**资源：**
- [PaddleOCR 官方文档](https://paddlepaddle.github.io/paddleocr/)
- [PaddleOCR GitHub](https://github.com/PaddlePaddle/PaddleOCR)
- [LangChain 多模态文档](https://python.langchain.com/docs/how_to/multimodal_prompts/)

#### Day 3：数据库 Tool 与规则 Tool [A] · 4h
- 数据库 Tool：参数化 SQL、只读账号、表和字段白名单、查询超时、返回数量限制、审计日志
- 规则 Tool：简单规则脚本或 Drools、规则版本、命中依据、金额计算、风险等级判断

**资源：**
- [Drools 官网（规则引擎）](https://www.drools.org/)
- [PostgreSQL 文档](https://www.postgresql.org/docs/current/)
- [LangChain Tools 文档](https://python.langchain.com/docs/how_to/tools/)

#### Day 4：MCP 核心 [B] · 4h
- 固定验证版本：Protocol Version `2025-11-25`、Local=stdio、Remote=Streamable HTTP
- 初始化时执行协议版本和能力协商
- 掌握 Host/Client/Server、Tools/Resources/Prompts、Session 生命周期、Tool Discovery
- Tool Annotation、OAuth 在 HTTP Transport 中的适用边界、MCP Server 来源信任
- Tool Annotation 不可作为真实鉴权依据

**资源：**
- [MCP Transport 规范（2025-11-25）](https://modelcontextprotocol.io/specification/2025-11-25/basic/transports)
- [MCP 授权规范](https://modelcontextprotocol.io/specification/2025-06-18/basic/authorization)
- [MCP Schema（含 Tool Annotation）](https://modelcontextprotocol.io/specification/2025-06-18/schema)
- [MCP 官网](https://modelcontextprotocol.io/)

#### Day 5：MCP Server [B] · 4h
- 实现一个医疗场景 MCP Server：保单查询 Tool、OCR Tool、规则校验 Tool
- 保单文档 Resource、理赔审核 Prompt

**资源：**
- [MCP Python SDK GitHub](https://github.com/modelcontextprotocol/python-sdk)
- [MCP 官方示例](https://modelcontextprotocol.io/examples)
- [B站 Spring AI 第49-56集 MCP 全套](https://www.bilibili.com/video/BV1GfyGBqEm6/)

#### Day 6：基础 RAG [A] · 4h
- PDF 解析、文本清洗、Chunk、Embedding
- Elasticsearch 向量检索、BM25、引用溯源、无答案拒答

**资源：**
- [B站 LangGraph构建智能AI应用（含RAG）](https://www.bilibili.com/video/BV1zKx4zWEZP/)
- [LangChain RAG Quickstart](https://python.langchain.com/docs/tutorials/rag/)
- [Elasticsearch kNN 向量检索文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/knn-search.html)

#### Day 7：整合与面试准备 · 4h
- OCR Tool、Database Tool、Rule Tool
- MCP Server 和 Client
- 基础保险知识库
- Function Calling、MCP、普通 API 的差异说明
- Java 恢复 2h：Spring AI ChatClient 和 Tool Calling

**资源：**
- [B站 Spring AI 第36-44集 Tools+权限](https://www.bilibili.com/video/BV1GfyGBqEm6/)
- [Spring AI Tool Calling 文档](https://docs.spring.io/spring-ai/reference/api/tools.html)

**📦 本周产出**：OCR/DB/Rule 三个 Tool · MCP Server+Client（固定版本）· 基础保险知识库 · 工具差异说明文档

---

### Week 3：企业 RAG、AI 数据工程、混合检索、评测

**本周目标**：完成第一个正式面试作品 —— 企业保险知识库可运行原型。

#### Day 1：AI 数据工程 [A] · 4h
- 亲自实现：Document ID、Chunk ID、文件 Hash、摄取任务状态、文档版本、Embedding 版本
- 开始积累 10 个 Golden Cases

**资源：**
- [LangChain Document 元数据设计](https://python.langchain.com/docs/how_to/document_loader_pdf/)
- [PyMuPDF 文档](https://pymupdf.readthedocs.io/)
- [unstructured 文档](https://unstructured-io.github.io/unstructured/)
- _结合你的 ETL/数据治理经验设计血缘_

#### Day 2：混合检索与 Rerank [A] · 4h
- Elasticsearch BM25 + 向量检索 + RRF 融合
- BGE Reranker、Metadata Filter
- 药品名、ICD 编码、保单号精确匹配

**资源：**
- [ES 向量检索文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/knn-search.html)
- [bge-reranker 模型](https://huggingface.co/BAAI/bge-reranker-large)
- [B站 2025最全RAG教程（含Rerank）](https://www.bilibili.com/video/BV1D37GzLEe5/)

#### Day 3：权限和文档治理 [A] · 4h
- Chunk 级 ACL、用户/角色/部门过滤
- 文档失效、删除和索引失效、增量更新
- 失败重试、死信记录、原文件/解析结果/索引一致性检查

**评测**：评测集增加到 30 个 Cases。

**资源：**
- [ES Metadata Filter（ACL 过滤）](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-terms-query.html)
- [LangChain 自查询检索器](https://python.langchain.com/docs/how_to/self_query/)

#### Day 4：Query 优化 [A] · 4h
- Query Rewrite、Multi-Query、Query Decomposition、Context Compression
- 无答案拒答、引用准确性

**资源：**
- [LangChain Query 改写](https://python.langchain.com/docs/how_to/query_transformer/)
- [LangChain RAG 进阶教程](https://python.langchain.com/docs/tutorials/rag/)

#### Day 5：RAG 评测 [A] · 4h
- 测试集扩充到 50 个 Cases：20 人工 Golden + 30 模型辅助生成人工抽检
- 每个 Case 含：`case_id / question / question_type / expected_document_ids / expected_chunk_ids / answer_key_points / should_refuse / allowed_roles / document_version`

**指标**：Hit Rate@K、Recall@K、Precision@K、MRR、nDCG@K、Faithfulness、Answer Relevancy、引用准确率、拒答准确率。

**资源：**
- [B站 RAG评估资料（LangSmith+RAGAS）](https://www.bilibili.com/video/BV1aZ421W7DB/)
- [B站 2025最全RAG教程（含RAGAS）](https://www.bilibili.com/video/BV1D37GzLEe5/)
- [RAGAS 官方文档](https://docs.ragas.io/)
- [RAGAS GitHub](https://github.com/explodinggradients/ragas)

#### Day 6：作品一打磨 [A] · 4h
- README、架构图、数据流图、选型 ADR、NFR 基线表、评测报告
- 一次失败案例及修复、3 分钟演示

**资源：**
- [Dify 文档（对比低代码知识库）](https://docs.dify.ai/)
- [RAGFlow GitHub（选型对比）](https://github.com/infiniflow/ragflow)

#### Day 7：第一轮模拟面试 · 4h
- RAG 完整流程、Chunk 策略如何选、为什么用 ES
- BM25 和向量检索差异、为什么需要 Rerank
- 如何实现 ACL、如何处理文档更新、如何评估检索
- 如何处理无答案、如何防止知识库越权

**本周完成简历 v1，并开始少量市场验证投递。**

**资源：**
- [B站 Spring AI 第57-77集 RAG全套（Java视角复习）](https://www.bilibili.com/video/BV1GfyGBqEm6/)
- _参考《FDE面试备战手册》20 道模拟题_

**📦 本周产出（作品一）**：企业保险知识库可运行原型 · 架构图+数据流图 · 选型 ADR · NFR 基线 · 50 Cases 评测报告 · 3 分钟演示 · 简历 v1

---

### Week 4：LangGraph Agent、HITL、Trace、正式面试准备

**本周目标**：完成第二个可面试作品的核心版本。第 4 周结束后正式开始面试。

#### Day 1：Agent 核心与 Loop [A] · 4h
- Agent、Workflow、Agentic Workflow 概念区分
- ReAct、Tool Calling Loop、停止条件、最大步骤、Token 预算
- 错误处理、工具结果回填

**实战**：亲自实现一个简单 Agent Loop。

**资源：**
- [B站 李宏毅 第2讲：一堂课搞懂 AI Agent 原理](https://www.bilibili.com/video/BV1aiADewEBC/)
- [B站 LangGraph构建智能AI应用（ReAct+Tool Calling）](https://www.bilibili.com/video/BV1zKx4zWEZP/)
- [LangGraph 中文官方文档](https://langgraph.com.cn/)

#### Day 2：LangGraph [A] · 4h
- State、Node、Edge、Conditional Edge、Tool Node
- Checkpointer、Interrupt、Command

**资源：**
- [B站 LangGraph构建智能AI应用（StateGraph/条件边/Tool Node）](https://www.bilibili.com/video/BV1zKx4zWEZP/)
- [LangGraph 中文官方教程](https://langgraph.com.cn/tutorials/get-started/1-build-basic-chatbot/index.html)
- [LangGraph GitHub](https://github.com/langchain-ai/langgraph)

#### Day 3：特药理赔 Agent 主流程 [A] · 4h

**流程**：
```
上传理赔材料 → OCR → 字段抽取 → 材料完整性检查 → 保单和知识库检索
→ 规则引擎校验 → 风险等级判断 → 人工审批 → 结论生成 → 结果回写
```

**资源**：复用 W2 的 OCR/DB/Rule Tool + W3 的保险知识库 + W4 Day2 的 LangGraph _重点是编排，不是重写工具_

#### Day 4：HITL 与中断恢复 [A] · 4h
- PostgreSQL Checkpointer、人工审批触发
- 审批通过和驳回、状态修改、中断恢复、异常重试
- 幂等键、最大执行时间

**资源：**
- [B站 LangGraph（Checkpoint/Interrupt/HITL 章节）](https://www.bilibili.com/video/BV1zKx4zWEZP/)
- [LangGraph Interrupts 官方文档](https://docs.langchain.com/oss/python/langgraph/interrupts)

#### Day 5：Trace 与成本 [A] · 4h
- 固定 Trace 字段：`trace_id / user_id_hash / tenant_id / model_provider / model_name / prompt_version / knowledge_version / agent_version / tool_name / input_tokens / output_tokens / latency_ms / cost / status / error_type`
- 自定义 Span：`llm.call / rag.retrieve / rerank.call / agent.step / tool.call / human.approval / eval.case`

**资源：**
- [Langfuse 官网（Trace 平台）](https://langfuse.com/)
- [OpenTelemetry AI Agent 可观测性博客](https://opentelemetry.io/blog/2025/ai-agent-observability/)
- [LangChain 流式与回调](https://python.langchain.com/docs/how_to/streaming/)

#### Day 6：作品二核心版打磨 [A] · 4h
- Agent 流程图、状态归属图、30 个 Agent 测试 Cases
- 工具调用成功率、任务完成率、人工审批触发准确率
- 高风险漏判样本统计、单任务成本、错误恢复测试
- README、3 分钟演示

**资源：**
- [RAGAS（Agent 评测）](https://docs.ragas.io/)
- [Langfuse（Trace 可视化）](https://langfuse.com/)

#### Day 7：正式面试准备 · 4h
- 简历终版 v1、三分钟自我介绍
- 三个项目故事：YOYO / 保险知识库 / 特药理赔 Agent
- 两张架构图
- 30 道大模型与 RAG 问题 + 30 道 Agent 问题 + 10 道系统设计题
- 一轮完整模拟面试 + 正式开始投递

**资源**：_参考《FDE面试备战手册.html》20 道模拟题 + 投递关键词；用通义/DeepSeek 做模拟面试官_

**📦 本周产出（作品二核心版）**：特药理赔 Agent · Agent 流程图+状态归属图 · 30 Cases 测试 · 量化评测 · 简历终版 · 项目故事+架构图 · 面试题集 · 模拟面试 · 正式投递

---

### ★ 第 4 周面试就绪标准

达到以下全部标准才算完成第 4 周：

1. 能独立演示 RAG
2. 能独立演示 LangGraph Agent
3. 能解释 Agent 和 Workflow 边界
4. 能解释规则、RAG、Agent、人工的职责划分
5. 能解释 MCP 和 Tool Calling
6. 能解释 Agent 状态、HITL、Checkpoint 和恢复
7. 能展示量化评测
8. 能说明哪些是亲自实现，哪些只做了设计
9. 能讲清项目失败案例和修复过程
10. 能回答至少 60% 的目标岗位高频技术问题

---

## 四、第 5-8 周：高薪岗位强化阶段

### Week 5：Agent Harness、可靠性、Text2SQL、三路数据路由

**本周目标**：把 Agent 原型升级为具备企业扩展能力的架构作品。

**必须完成 [A]**
- Agent Loop 企业扩展、Context 裁剪与摘要、Tool Runtime
- 工具鉴权、执行预算、幂等、超时、重试与退避、补偿
- 业务状态归属、审计

**Text2SQL 最小 Demo [B]**
```
用户问题 → 数据路由判断 → Schema Linking → 生成只读 SQL
→ SQL 安全校验 → 执行计划和超时 → 执行 → 结果解释
```

**三路数据路由 [A]**：根据问题动态选择 RAG / SQL / 业务 API

**默认不实现**：Temporal、多 Agent、A2A 默认不实现，只理解边界

**资源：**
- [B站 李宏毅 Agent 原理](https://www.bilibili.com/video/BV1aiADewEBC/)
- [B站 LangGraph（多智能体/工具）](https://www.bilibili.com/video/BV1zKx4zWEZP/)
- [Awesome-Text2SQL](https://github.com/eosphoros-ai/awesome-text2sql)
- [Temporal Java SDK（仅了解）](https://docs.temporal.io/develop/java)

---

### Week 6：评测、LLMOps、安全、Agent Memory

**本周目标**：补齐高级岗位最看重的评测、治理和安全能力。

**Agent 评测 [A]**
- RAG Eval 50 Cases · Agent Eval 30 Cases
- OCR Tool / Rule Tool / Database Tool 各 10-20 Cases

**LLMOps 必须实现 [A]**
- Prompt 版本、模型配置版本、Embedding 版本、评测集版本
- 一键离线回归、Trace、Token 成本、错误 Case 保存

**只做设计 [C]**
- 灰度发布、A/B 测试、自动回滚、漂移监控、发布审批

**安全 [A][B][C]**
- Prompt Injection / Indirect Prompt Injection / MCP Server 信任 / Tool Poisoning
- RAG 数据投毒 / SQL 和代码执行风险 / 最小权限 / Tool 白名单
- 数据脱敏 / 租户隔离 / Human Approval / 安全审计
- OWASP LLM Top 10 / Agentic Application 风险

**Agent Memory 生命周期 [B]**
- Working Memory / Conversation Summary / Episodic Memory / 用户级长期记忆
- 保留时间、删除和更正、Memory Poisoning、租户隔离

**资源：**
- [RAGAS（Agent/Tool 评测）](https://docs.ragas.io/)
- [OWASP LLM Top 10](https://genai.owasp.org/llm-top-10/)
- [OWASP Agentic Security](https://genai.owasp.org/initiatives/agentic-security-initiative/)
- [LangChain Memory 文档](https://python.langchain.com/docs/how_to/chatbots_memory/)
- [Langfuse（版本与回归）](https://langfuse.com/)

---

### Week 7：Spring AI、模型网关、企业平台与部署交付

**本周目标**：形成 Java 与企业 AI 平台能力，冲刺高级架构师和技术负责人岗位。

**Spring AI [A]**
- ChatClient、Advisor、Structured Output、Tool Calling、Chat Memory
- Elasticsearch VectorStore、MCP Client、MCP Server、Observability

**AI Gateway [A][B][C]**
- 自研核心：Model Adapter / Provider 路由 / 超时重试 / Fallback / Token 成本 / Trace Hook / 简单限流
- LiteLLM 集成体验：虚拟 Key / 预算 / 配额 / 多租户 / 项目级成本 / 管理界面
- 完成 Build vs Buy ADR

**单一调用链**
- 企业模式：业务应用 → Spring AI → LiteLLM → 模型服务
- 学习模式：业务应用 → 自研 Model Adapter → 模型服务
- 避免两层路由、两层重试和成本重复统计

**部署交付 [B]**
- Docker / Docker Compose / 环境隔离 / Secret 管理 / Health Check / Readiness Check
- CI/CD / 数据库迁移 / 日志指标 Trace / API 限流 / 容器资源限制
- Kubernetes 基础拓扑 / 灰度和回滚方案

**资源：**
- [B站 Spring AI 全套84集（ChatClient/Tools/MCP/RAG/Agent）](https://www.bilibili.com/video/BV1GfyGBqEm6/)
- [B站 Spring AI 零基础19集](https://www.bilibili.com/video/BV1X27GznEsf/)
- [Spring AI Tool Calling 文档](https://docs.spring.io/spring-ai/reference/api/tools.html)
- [Spring AI MCP 文档](https://docs.spring.io/spring-ai/reference/api/mcp/mcp-overview.html)
- [LiteLLM 文档](https://docs.litellm.ai/)

---

### Week 8：vLLM、容量成本、系统设计、作品整合与高级面试

**本周目标**：完成高级岗位的最后补强，形成完整作品集和交付方案。

**vLLM 服务化实验 [B]**
- 启动兼容 API、使用非 OpenAI 模型、指定量化方式
- 设置输入输出长度、并发和请求速率
- 测试 TTFT、TPOT、ITL、吞吐和端到端延迟，输出测试环境和结论
- 无 GPU 时：小模型实测 + 32B 模型公式估算，明确假设条件，不伪造性能数据

**容量与成本 [B]**
- API 模型与私有部署成本对比、GPU 显存估算、KV Cache 估算
- 并发和队列、模型副本数、Token 成本、成本优化方案

**总 FDE Solution Delivery Pack（15-18 页）**
1. 特药理赔现有流程
2. 业务痛点和目标
3. AI 适用性
4. 数据准备度
5. 规则、RAG、Agent、人工边界
6. POC 范围
7. 保险知识库
8. 特药理赔 Agent
9. 企业 Agent 平台
10. 数据和知识架构
11. 安全与权限
12. 评测与 LLMOps
13. 部署拓扑
14. NFR 和实测结果
15. POC 到生产差距
16. 成本、风险和上线计划

**高级面试冲刺**
- 设计企业 RAG 平台 / 理赔 Agent / 企业 AI 中台 / 模型网关
- 从 POC 到生产如何推进 / 如何评估大模型项目 / 如何控制成本
- 如何处理安全与合规 / 如何划分规则 RAG Agent 人工 / 如何带团队建设企业 AI 能力

**资源：**
- [vLLM 官方文档](https://docs.vllm.ai/)
- [vLLM Benchmark CLI（TTFT/TPOT/ITL）](https://docs.vllm.ai/en/stable/benchmarking/cli/)
- [vLLM 兼容 API 部署](https://docs.vllm.ai/en/stable/models/serving.html)
- [Ollama Qwen3（无GPU小模型实测）](https://ollama.com/library/qwen3)
- _系统设计题参考《FDE面试备战手册》_

---

## 五、三个作品集

**作品一 · W3 完成：企业保险知识库**
- 增量摄取、文档和 Chunk 版本、混合检索、Rerank、ACL、引用、评测、数据工程和血缘设计

**作品二 · W4 核心版：特药理赔 Agent**
- LangGraph、HITL、Checkpoint、Workflow 与 Agent 边界、OCR/RAG/规则/人工职责、幂等/重试/恢复、Trace/成本/评测、从 POC 到生产差距

**作品三 · W7 完成：企业 Agent 平台最小版**
- Spring AI、模型网关、MCP、Tool Registry、Trace、部署拓扑、Build vs Buy ADR

---

## 六、第 4 周开始的投递策略

**第一优先**
1. 企业 GenAI 解决方案架构师
2. 高级 AI 应用工程师
3. AI 应用技术负责人
4. 医疗保险 AI 负责人
5. AI 解决方案负责人

**第二优先**
1. 本地或远程 AI Deployment Engineer
2. 低出差比例的 FDE
3. 高级 RAG 工程师
4. 高级 Agent 应用工程师

**暂缓**
- Agent Runtime 核心开发
- 纯多 Agent 平台开发
- 强现场编码岗位
- 模型训练岗位
- 推理引擎开发
- 长期驻场 FDE
- 强算法岗位

---

## 七、最终面试叙事

**定位**
> 我具备 Java、大数据、医疗保险行业和复杂项目管理经验，目前重点转向企业级大模型应用、RAG、Agent 和 AI 平台落地。我能够完成从业务流程分析、数据准备度评估、技术选型、原型开发、效果评测到生产化方案设计的完整工作。

### 1. YOYO 场景识别
- 用户自然语言表达复杂，规则会产生规则爆炸
- 模型负责语义理解，规则负责输出约束和安全控制
- Prompt 和 Few-shot 如何优化、测试集如何构建
- 98% 的样本规模、测试口径和错误分类

### 2. 企业保险知识库
- 增量摄取、文档和 Chunk 版本、混合检索、Rerank
- ACL、引用、评测、数据工程和血缘设计

### 3. 特药理赔 Agent
- Workflow 与 Agent 的边界、OCR/RAG/规则引擎/人工的职责
- LangGraph 状态、HITL、Checkpoint、幂等/重试/恢复
- Trace、成本和评测、从 POC 到生产的差距

---

## 八、执行红线

> ⚠ 不可逾越的 10 条红线

1. 第 4 周前不学习复杂多 Agent
2. 第 4 周前不学习 A2A
3. Temporal 默认不学习
4. 第 4 周前不开发完整 AI 平台
5. 第 4 周前不深入 vLLM
6. 第 4 周前 Java 只恢复 Spring Boot 和 Spring AI 必需能力
7. 每周必须产生可运行代码和测试结果
8. 不得只看教程不写代码
9. 不得使用无法解释的 AI 生成代码
10. 项目中所有指标必须记录测试环境、样本量和实测值

---

## 附录：核心资源速查

### B 站视频
| 资源 | BV 号 / 链接 | 用途 |
|---|---|---|
| 李宏毅 生成式AI导论2025 | [BV1mXpuz7E9v](https://www.bilibili.com/video/BV1mXpuz7E9v/) | W1 大模型基础 |
| 李宏毅 机器学习2025（含Agent） | [BV1aiADewEBC](https://www.bilibili.com/video/BV1aiADewEBC/) | W1/W4 Agent 原理 |
| LangGraph构建智能AI应用 | [BV1zKx4zWEZP](https://www.bilibili.com/video/BV1zKx4zWEZP/) | W2-W4 RAG/Agent |
| Spring AI 全套84集 | [BV1GfyGBqEm6](https://www.bilibili.com/video/BV1GfyGBqEm6/) | W2/W3/W7 Java |
| Spring AI 零基础19集 | [BV1X27GznEsf](https://www.bilibili.com/video/BV1X27GznEsf/) | W7 快速入门 |
| RAG评估资料 | [BV1aZ421W7DB](https://www.bilibili.com/video/BV1aZ421W7DB/) | W3 评测 |
| 2025最全RAG教程（含RAGAS） | [BV1D37GzLEe5](https://www.bilibili.com/video/BV1D37GzLEe5/) | W3 RAG |

### 官方文档
| 文档 | 链接 |
|---|---|
| 通义千问 API | https://help.aliyun.com/zh/model-studio/developer-reference/use-qwen-by-calling-api |
| DeepSeek API | https://api-docs.deepseek.com/ |
| Claude API | https://docs.anthropic.com/en/api/getting-started |
| LangChain | https://python.langchain.com/docs/ |
| LangGraph 中文 | https://langgraph.com.cn/ |
| Spring AI | https://docs.spring.io/spring-ai/reference/ |
| MCP 协议 | https://modelcontextprotocol.io/ |
| vLLM | https://docs.vllm.ai/ |
| RAGAS | https://docs.ragas.io/ |
| LiteLLM | https://docs.litellm.ai/ |
| Langfuse | https://langfuse.com/ |
| OWASP LLM | https://genai.owasp.org/llm-top-10/ |
| Elasticsearch kNN | https://www.elastic.co/guide/en/elasticsearch/reference/current/knn-search.html |
| Ollama | https://ollama.com/ |

---

*FDE 学习路径 v4 · 8 周面试冲刺版 · 第 4 周正式面试，第 8 周冲刺高薪岗位 · 全程非 OpenAI 模型*
