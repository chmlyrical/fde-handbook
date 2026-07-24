# FDE 面试学习路径 v3.1

> 基于 ChatGPT 对 v3 的评估优化版 · 解决"内容过载和实现边界不清"  
> 核心方法：A/B/C 三级分类法 + Agent Harness 标记为参考模型 + Temporal 可选 + 框架选型 ADR + AI 数据工程  
> 用户画像：36 岁 / Java+大数据 / 医疗保险行业 / 每天可投入 4 小时 / 目标 AI 解决方案架构师

---

## 一、v3 → v3.1 关键调整说明

ChatGPT 对 v3 的评分：求职定位 9/10、技术覆盖 8.5/10、作品设计 8.5/10、高级岗匹配 8/10、12周可执行性 6.5/10、面试转化 8/10。

核心结论：v3 已明显成熟，问题从"技术遗漏"转成"内容过载和实现边界不清"。保留约 85% 内容，形成 v3.1。

### 八项关键修改

#### 1. A/B/C 三级分类法（解决过载的核心工具）

所有学习内容按深度要求分三级：

| 级别      | 要求            | 典型内容                                    |
| ------- | ------------- | --------------------------------------- |
| **A 级** | 必须亲自实现并能讲代码   | RAG、Tool Calling、LangGraph、Trace、基础模型网关 |
| **B 级** | 跑通 Demo 并能讲架构 | MCP、Temporal、vLLM、Spring AI MCP         |
| **C 级** | 理解原理并能做选型     | A2A、多 Agent、完整 LLMOps、模型集群扩缩容           |

**全文用 [A] / [B] / [C] 标记每个内容的深度要求。**

#### 2. Agent Harness 标记为参考能力模型（非行业标准）

- 七大组件拆分是合理的工程参考模型，但**不是行业统一标准**
- 面试表达改为："我根据企业 Agent 的运行需求，把 Harness 拆分为 Loop、Context、Tool Runtime、State、Policy、Workspace 和 Observability 七个能力域"
- **不重复开发框架已提供的能力**：OpenAI Agents SDK 已含 Agent Loop、工具执行、Handoff、Guardrails、Tracing 和可恢复运行状态；Spring AI 也在持续完善工具循环和可观测性
- 自研 Harness 侧重理解核心机制和企业扩展点

#### 3. Temporal 改为可选 + 优先 Java SDK

- **LangGraph 已有**状态持久化、Durable Execution、Interrupt、Human in the Loop 和失败恢复能力
- **作品②第一版只用**：LangGraph + PostgreSQL Checkpointer + 业务数据库 + 消息队列模拟
- Temporal 作为独立对比 Demo，**用 Java SDK**（与用户技术背景匹配）
- **状态归属明确**（避免 LangGraph 和 Temporal 同时控制同一状态）：
  - Agent 消息、工具结果、推理阶段 → LangGraph
  - 理赔单业务状态 → MySQL 或 PostgreSQL
  - 跨系统流程、等待、超时、补偿 → Temporal（可选）
  - 事件通知与系统解耦 → Kafka
- **引入 Temporal 的场景**：流程持续数小时/天、大量定时器和异步回调、涉及多个业务系统、需要可靠重试和补偿、Agent 只是业务流程中的一个节点

#### 4. MCP Transport 表述更新

- **旧表述**：stdio / SSE / HTTP
- **新表述**：stdio / Streamable HTTP（其中 Streamable HTTP 可以使用 SSE 进行服务端流式传输）
- 旧的独立 HTTP+SSE Transport 已在 2025 年 3 月被 Streamable HTTP 替代
- W2 MCP 学习内容补充：
  - MCP Session 生命周期
  - OAuth 2.1 授权基本流程
  - Tool Annotation（只读、破坏性、幂等）
  - 服务端能力协商
  - MCP Server 的来源信任和准入

#### 5. 增加框架选型方法论（ADR）

解决方案架构师的核心能力是"能解释为什么选"，不只是"会调用 API"。

- **W5 新增**：Agent 框架选型 ADR（Architecture Decision Record）
  - 比较 LangGraph / Spring AI / OpenAI Agents SDK
  - 维度：编排模型、状态管理、HITL、工具体系、MCP、多 Agent、可观测性、部署方式、技术语言、锁定风险、团队适配
- **W3 新增**：知识库平台选型 ADR
  - 比较 Dify / RAGFlow / ES 自研 / pgvector 自研 / 云厂商托管
  - 维度：解析能力、ACL、多租户、增量更新、混合检索、评测、API、运维成本、数据主权

#### 6. 增加 AI 数据工程（用户差异化优势）

用户有大数据背景，这部分应成为差异化优势。W3-W4 新增：

- Document ID 和 Chunk ID 设计
- 文件内容 Hash 与重复检测
- 数据源、文档、Chunk 的血缘
- 增量更新与全量重建
- 删除事件和失效标记
- Embedding 版本迁移
- 解析失败重试和死信队列
- 数据质量规则
- 索引构建状态
- 数据摄取可观测性
- 多知识库租户隔离
- 原始文件、解析结果和索引数据的一致性

**作品①亮点升级**：从普通 RAG 升级为"具备增量摄取、数据血缘、权限过滤、混合检索和评测闭环的保险知识库"。

#### 7. RAG 评测加确定性检索指标

- **确定性检索指标**：Hit Rate@K、Recall@K、Precision@K、MRR、nDCG@K
- RAGAS 四指标保留，但要和人工黄金答案、检索指标、引用准确性结合
- **测试集 9 类问题**：普通问题、关键词精确匹配、语义改写、跨条款、无答案、权限受限、易混淆条款、恶意注入、版本已失效文档

#### 8. W10 vLLM 目标修改

- **旧目标**："32B 模型需要多少 GPU"（无固定答案）
- **新目标**：在明确模型、量化、上下文、并发和 GPU 条件后，完成一次基准测试，并形成容量规划和成本测算文档
- 需要先明确：权重精度/量化格式、最大上下文长度、输入输出 Token 分布、并发请求数量、KV Cache 占用、Tensor Parallel 数量、GPU 型号和显存、TTFT/TPOT 服务目标、是否允许请求排队
- 没有 GPU 时：小模型实测 + 32B 公式估算和方案设计，不伪造运行数据

### 其他调整

- **W8 LLMOps 拆分**：
  - 必须实现：Prompt 版本号、模型与 Embedding 配置版本、评测集版本、一键离线回归脚本、Trace、Token 成本统计、错误 Case 保存
  - 只做设计：灰度发布、A/B 测试、自动回滚、在线漂移监控、企业级发布审批
- **W10-W11 新增部署交付工程**：Docker Compose、环境隔离、Secret 管理、Health Check、CI/CD、数据库迁移、日志指标 Trace 采集、API 限流、容器资源限制、K8s 基础部署架构、灰度和回滚方案
- **W4 投简历定位调整**：从"已具备面试解决方案架构师能力"改为"验证市场反馈、测试简历定位、获取真实面试问题"
- **面试话术调整**：删除"预计 X 周后能上手"，改为"该部分完成了原理研究和原型验证，尚未经历生产规模验证。我可以说明设计方案、当前边界和需要进一步验证的指标"

---

## 二、用户画像与方案前提

| 维度      | 内容                                                              |
| ------- | --------------------------------------------------------------- |
| 年龄      | 36 岁                                                            |
| 技术背景    | Java + 大数据（Hadoop / 数仓 / 数据建模 / SQL），做过架构相关（非大模型架构）             |
| 行业背景    | 医疗 / 保险 / 健康 相关（核心差异化优势）                                        |
| 大模型水平   | 会调 API、用过 ChatGPT 类工具；RAG / Agent / 本地部署均未做过                    |
| 求职方向    | 第一主线 AI 解决方案架构师/AI FDE/企业 GenAI 架构师；第二主线 AI 应用技术负责人；保底 数据与AI架构师 |
| 出差偏好    | 基本不想出差（主投远程/本地交付的 AI 应用岗）                                       |
| 时间预算    | **每天 4 小时，每周 28 小时**（在职但时间稳定）                                   |
| 12 周总时长 | 336 小时                                                          |

---

## 三、v3.1 新12周主线（含 A/B/C 标记）

|  周  | 核心内容                                                                                   | 作品进度   |     投简历    |
| :-: | -------------------------------------------------------------------------------------- | ------ | :--------: |
|  W1 | 大模型基础、模型调用、结构化输出、模型选型、**轻量模型网关 [A]**                                                   | —      |            |
|  W2 | Tool Calling [A]、MCP [B]、工具权限与参数校验 [A]                                                 | —      |            |
|  W3 | 文档解析 [A]、数据摄取 [A]、**AI 数据工程 [A]**、Chunk [A]、Embedding [A]、基础 RAG [A]、**知识库选型 ADR [C]** | 作品① 启动 |            |
|  W4 | 混合检索 [A]、Rerank [A]、ACL [A]、RAG 评测 [A]、作品一                                             | 作品① 完成 | **★ 市场验证** |
|  W5 | **Agent Loop [A]、ReAct [A]、Context Engine [A]、Tool Runtime [A]、Agent 框架选型 ADR [C]**    | 作品② 启动 |            |
|  W6 | LangGraph [A]、Checkpoint [A]、HITL [A]、中断恢复 [A]                                         | 作品② 推进 |            |
|  W7 | **Agent Harness 参考模型 [B]、幂等/重试 [B]、Temporal [B-可选]、业务 Workflow 边界 [C]**                | 作品② 推进 |            |
|  W8 | 评测四层 [A]、Trace [A]、OpenTelemetry [B]、**LLMOps 必须实现 [A] + 只做设计 [C]**、作品二                | 作品② 完成 |  **★ 第二批** |
|  W9 | Agent 安全 [A]、红队测试 [B]、OWASP LLM Top 10 [C]、权限审计 [A]                                    | —      |            |
| W10 | **模型网关平台版 [A]、推理服务 [B]、性能容量 [B]、部署交付工程 [B]**                                           | 作品③ 启动 |            |
| W11 | Spring AI [A]、企业系统集成 [A]、平台最小版本 [A]                                                    | 作品③ 推进 |            |
| W12 | 作品打磨、系统设计、Java 高频知识和模拟面试                                                               | 作品③ 完成 |  **★ 第三批** |

---

## 四、技术栈与框架选型

### 主框架组合

| 定位           | 框架                | 语言           | 用途                                   |  级别  |
| ------------ | ----------------- | ------------ | ------------------------------------ | :--: |
| **主攻**       | LangGraph         | Python       | Agent 状态机编排、长任务、HITL、中断恢复            |   A  |
| **企业集成**     | Spring AI         | Java         | 企业 Java 系统接入 AI、Tool Calling、RAG、MCP |   A  |
| **辅修**       | OpenAI Agents SDK | Python       | 快速理解标准 Agent Runtime 抽象              |   B  |
| **业务编排（可选）** | Temporal          | **Java SDK** | 跨小时跨天的持久业务工作流                        | B-可选 |

### 向量数据库

| 阶段       | 选型            | 理由                                                       |  级别 |
| -------- | ------------- | -------------------------------------------------------- | :-: |
| **主方案**  | Elasticsearch | 同时承担 BM25、向量检索、Metadata Filter、混合检索；适合药品名、ICD 编码、保单号精确匹配 |  A  |
| **对比实验** | pgvector      | 上手快、可练权限与向量统一管理，作为对比实验                                   |  B  |

### 模型网关演进

| 阶段      | 内容                                                                           |  周  |  级别 |
| ------- | ---------------------------------------------------------------------------- | :-: | :-: |
| W1 轻量版  | Model Adapter：多模型统一接口、Provider 适配、基础路由                                       |  W1 |  A  |
| W10 平台版 | AI Gateway：路由、失败重试、Fallback、限流、熔断、缓存、Token 预算、虚拟 API Key、用户和部门配额、成本归集、模型访问审计 | W10 |  A  |

---

## 五、三大作品定位（v3.1 调整）

| 作品      | 主题                     | 技术栈                                                                                            |   制作周  | 定位              |
| ------- | ---------------------- | ---------------------------------------------------------------------------------------------- | :----: | --------------- |
| **作品①** | 企业保险知识库（RAG + AI 数据工程） | Python + LangChain + ES（主） + BGE + bge-reranker + **增量摄取 + 数据血缘**                              |  W3-4  | 企业级可演示原型        |
| **作品②** | 特药理赔 Agent             | Python + LangGraph + **Agent Harness 参考实现** + Temporal（可选 Java SDK） + OCR/RAG/Rule Tool + HITL |  W5-8  | 企业级可演示原型        |
| **作品③** | 企业 Agent 平台最小版         | Java + Spring AI + 模型网关 + Tool Registry + MCP + Trace                                          | W10-12 | 核心模块 Demo + 架构图 |

> **作品表述**：全部用"企业级可演示原型 / Interview Grade Prototype"，不用"生产版/完整版"。  
> **作品③简化原则**：只实现 模型网关 + Prompt Registry + Tool Registry + MCP Registry + Trace + Token Cost。Knowledge Base、Evaluation Center、IAM、Application Marketplace 先做架构设计和接口定义。

---

## Week 1：大模型基础、模型调用、结构化输出、模型选型、轻量模型网关

**本周目标**：建立大模型完整基础知识体系，能独立调用主流模型 API，建立轻量模型网关概念。

### Day 1（4h）：大模型原理（宏观）[A]

- Transformer 架构、Self-Attention、Multi-Head Attention
- Token / Tokenizer / BPE / Tiktoken
- Embedding 的本质与维度
- 上下文窗口、KV Cache
- Prefill vs Decode 阶段

### Day 2（4h）：采样参数与输出控制 [A]

- Temperature、Top P、Top K、Stop Sequences
- Structured Output（JSON Mode、JSON Schema）
- Function Calling 概念入门
- 推理模型 vs 普通对话模型的差异
- 多模态模型的能力边界

### Day 3（4h）：模型选型与权衡 [A]

- 开源 vs 闭源 vs 本地部署的选择维度
- 准确率、延迟、吞吐、成本、上下文长度的权衡
- SFT、LoRA、量化、蒸馏的概念（不深入算法）
- 模型幻觉成因与缓解策略
- 主流模型对比：GPT-4o / Claude / Qwen / DeepSeek / Llama

### Day 4（4h）：API 调用实战 + 轻量模型网关 [A]

- OpenAI / 通义 / 智谱 / DeepSeek API 调用
- 流式输出（SSE）处理
- JSON 结构化输出与校验
- Token 统计与成本计算
- **轻量 Model Adapter 实现**：多模型统一接口、Provider 适配、基础路由、失败重试

### Day 5（4h）：Prompt 工程基础 [A]

- System Prompt / User Prompt / Assistant Prompt
- Zero Shot、Few Shot、Chain of Thought
- CoT 的工程使用边界
- Prompt 模板与变量管理
- Prompt Injection 攻击与防御

### Day 6（4h）：Prompt 工程进阶 + 本地部署体验 [A] + [B]

- 输出格式约束、JSON Schema 强约束 [A]
- Prompt 测试集概念 [A]
- Ollama 本地跑 Qwen3，理解量化与显存占用 [B]
- vLLM 概念了解 [C]

### Day 7（4h）：本周产出整合 + Java 恢复

- 整理本周代码到 GitHub
- 写博客/笔记：模型选型决策框架
- **Java 恢复**：Java 集合、并发基础复习
- Spring Boot 项目骨架搭建

**本周产出：**

1. 轻量模型网关 v1（多模型统一接口、Provider 适配、基础路由、失败重试）[A]
2. Prompt 模板管理系统 [A]
3. 模型选型决策文档 [A]
4. GitHub 仓库初始化 + Java 项目骨架

---

## Week 2：Tool Calling、MCP、工具权限与参数校验

**本周目标**：掌握 Function Calling 完整工程实践，理解 MCP 协议，能独立实现一个 MCP Server 和 MCP Client。

### Day 1（4h）：Function Calling 深入 [A]

- OpenAI / Claude / Qwen Function Calling 规范对比
- 工具描述（Tool Schema）写法
- 参数校验与错误处理
- 多工具并行调用
- 多轮工具调用循环

### Day 2（4h）：实战工具开发①：OCR Tool [A]

- 基于 PaddleOCR 或云 OCR 实现医疗单据 OCR
- 包装成 Function Calling 兼容的 Tool
- 错误处理与降级策略

### Day 3（4h）：实战工具开发②：数据库查询 Tool + 规则引擎 [A]

- DB Query Tool：参数化 SQL 执行，只读权限限制
- 规则引擎 Tool：基于 Drools 或简单规则脚本
- 工具权限与白名单设计
- 参数校验与沙箱执行

### Day 4（4h）：MCP 协议学习 [B]

- MCP Host / Client / Server 三方关系
- Tools / Resources / Prompts 三类能力
- **Transport：stdio / Streamable HTTP**（其中 Streamable HTTP 可以使用 SSE 进行服务端流式传输；旧 HTTP+SSE 已 2025-03 废弃）
- Tool Discovery、能力协商、进度跟踪、取消
- **MCP Session 生命周期**
- **OAuth 2.1 授权基本流程**
- **Tool Annotation**（只读、破坏性、幂等）
- **服务端能力协商**
- **MCP Server 的来源信任和准入**
- 权限与认证机制
- MCP 安全风险（Tool Poisoning、工具描述注入）

### Day 5（4h）：实现一个 MCP Server [B]

- 基于 Python MCP SDK 实现
- 暴露医疗相关 Tools：OCR、保单查询、规则校验
- 实现 Resources：保单模板、规则文档
- 实现 Prompts：理赔审核 Prompt 模板

### Day 6（4h）：实现一个 MCP Client + 工具权限 [B]

- MCP Client 调用自研 Server
- 工具白名单机制
- 参数校验与沙箱执行
- 错误处理、超时、重试
- 对比 MCP vs 直接 Function Calling 的差异

### Day 7（4h）：整合 + Java 恢复

- 整理 Tool + MCP 代码到 GitHub
- 画一张 Function Calling → MCP 演进图
- **Java 恢复**：Spring Boot 复习（REST API + 依赖注入 + 配置管理）

**本周产出：**

1. OCR Tool + DB Query Tool + Rule Engine Tool [A]
2. 一个可运行的 MCP Server（医疗场景）[B]
3. 一个 MCP Client + 工具权限白名单 [B]
4. Spring Boot 项目骨架完善

> **注**：Text2SQL 移到 W8 后作为独立选修 [C]（涉及语义层、Schema Linking、SQL 生成、只读权限、危险语句拦截、执行计划、结果校验和评测）。

---

## Week 3：文档解析、数据摄取、AI 数据工程、Chunk、Embedding、基础 RAG、知识库选型 ADR

**本周目标**：掌握 Naive RAG 完整流程，**加入 AI 数据工程**（用户差异化优势），用 ES 跑通文档问答 demo，启动作品①。

### Day 1（4h）：RAG 概念与流程 [A]

- Naive RAG 完整流程：摄取→解析→切分→Embedding→检索→生成
- RAG 四阶段：Naive → Advanced → Modular → Agentic
- LangChain RAG quickstart 跑通
- Dify 知识库体验（对比低代码方案）[B]

### Day 2（4h）：文档解析与清洗 [A]

- PyMuPDF / unstructured 解析 PDF
- Word / PPT / Excel 解析
- OCR 与版面分析（PaddleOCR）
- 文本清洗：去噪、去重、规范化

### Day 3（4h）：AI 数据工程（v3.1 新增，用户差异化优势）[A]

- **Document ID 和 Chunk ID 设计**
- **文件内容 Hash 与重复检测**
- **数据源、文档、Chunk 的血缘**
- **增量更新与全量重建**
- **删除事件和失效标记**
- **Embedding 版本迁移**
- **解析失败重试和死信队列**
- **数据质量规则**
- **索引构建状态**
- **数据摄取可观测性**
- **多知识库租户隔离**
- **原始文件、解析结果和索引数据的一致性**

### Day 4（4h）：Chunk 切分策略 + Embedding + 知识库选型 ADR [A] + [C]

- Chunk 策略：固定长度、按段落/标题、滑动窗口、Parent-Child、Sentence Window、语义切分 [A]
- Embedding 模型选型：BGE / GTE / OpenAI ada [A]
- Elasticsearch 安装与向量检索配置 [A]
- **知识库平台选型 ADR（v3.1 新增）[C]**：
  - 比较 Dify / RAGFlow / ES 自研 / pgvector 自研 / 云厂商托管
  - 维度：解析能力、ACL、多租户、增量更新、混合检索、评测、API、运维成本、数据主权
  - 产出：选型决策文档

### Day 5（4h）：检索与生成 [A]

- 向量检索（Top-K）
- BM25 关键词检索
- 引用溯源实现
- 无答案拒答策略
- Prompt 模板：问答 + 引用 + 拒答

### Day 6（4h）：作品①启动：保险知识库基础版 [A]

- 数据：5-10 份公开保险条款 PDF（重疾险/医疗险）
- 架构：摄取 pipeline（含 AI 数据工程） + ES（BM25+向量） + LangChain + 通义 API
- 功能：上传 PDF → chunk → embedding → 检索 → 问答 + 引用
- 放 GitHub，写 README

### Day 7（4h）：整合 + Java 恢复

- 作品①基础版跑通，README 完善
- 画 RAG 流程图（含数据工程环节）
- **Java 恢复**：Spring AI 入门（ChatClient、Model Adapter）

**本周产出：**

1. RAG 基础 demo（ES + LangChain + AI 数据工程）[A]
2. **知识库平台选型 ADR 文档** [C]
3. **作品①基础版**：保险知识库最小可用版（GitHub）[A]
4. Spring AI 入门 demo [B]

---

## Week 4：混合检索、Rerank、ACL、RAG 评测、作品一（市场验证节点）

**本周目标**：把 Naive RAG 升级到 Advanced RAG，引入混合检索、rerank、权限过滤、**确定性检索指标 + RAGAS**。作品①完成企业级可演示原型，第4周末投第一批简历（定位：市场验证）。

### Day 1（4h）：ES 混合检索深化 [A]

- 向量检索 + BM25 融合（RRF 算法）
- 医疗场景适配：药品名、ICD 编码、保单号精确匹配
- Metadata 过滤
- 多路召回融合

### Day 2（4h）：Rerank 与 Query 改写 [A]

- bge-reranker 模型使用
- Query Rewrite（HyDE、Query 扩展、Multi-Query）
- Query Decomposition（问题分解）
- Context Compression

### Day 3（4h）：权限控制与文档治理 [A]

- chunk 级权限标签（部门/角色）
- 检索时 Metadata filter（用户身份过滤）
- 文档版本管理与失效
- 增量索引策略
- 缓存失效机制

### Day 4（4h）：RAG 评测（确定性检索指标 + RAGAS）[A]

- **确定性检索指标（v3.1 新增）**：Hit Rate@K、Recall@K、Precision@K、MRR、nDCG@K
- RAGAS 四指标：Faithfulness / Answer Relevancy / Context Precision / Context Recall
- **结合人工黄金答案、检索指标、引用准确性**
- **测试集 9 类问题**：普通问题、关键词精确匹配、语义改写、跨条款、无答案、权限受限、易混淆条款、恶意注入、版本已失效文档
- 构造评测集（20-30 个医疗问答对）
- 跑评测，分析薄弱环节，针对性优化

### Day 5（4h）：作品①企业级完成 [A]

- 整合：ES 混合检索 + rerank + 权限 + AI 数据工程 + 评测
- 架构图绘制（draw.io 或 excalidraw）
- README 完整化：架构、难点、优化点、评测结果、**数据工程亮点**
- Streamlit/Gradio 前端界面

### Day 6（4h）：简历改造 + 投简历准备

- 简历突出：医疗/保险行业 + 大数据 + **AI 数据工程** + Java + RAG 作品
- 三个面试故事定稿（数据治理→RAG、Java架构→工程化、医疗行业→差异化）
- 整理目标公司清单（医疗AI / 保险科技 / 健康险）
- BOSS / 猎聘账号准备

### Day 7（4h）：开始投简历（市场验证）+ Java 恢复

- **★ 投第一批简历（定位：市场验证、测试简历定位、获取真实面试问题）**
- 主投：行业高度匹配的 AI 解决方案岗位、RAG/知识库应用岗位、医疗保险 AI 应用岗、部分 AI 项目负责人岗位
- **Java 恢复**：Spring AI Tool Calling
- 准备面试常见问题清单

**本周产出：**

1. 企业级 RAG 系统完整版（混合检索 + rerank + 权限 + AI 数据工程 + 评测）[A]
2. **作品①完成**：保险知识库企业级可演示原型（GitHub + README + 架构图 + 评测结果）[A]
3. 改造版简历 + 三个面试故事
4. **投第一批简历（市场验证）**

> **★ W4 末定位调整**：不是"已具备面试解决方案架构师能力"，而是"验证市场反馈、测试简历定位、获取真实面试问题"。只有一个 RAG 原型时，面对高级架构师深度面试仍然比较吃力。

> **面试话术**：遇到答不上的问题，不说"预计 X 周后能上手"，改为"该部分完成了原理研究和原型验证，尚未经历生产规模验证。我可以说明设计方案、当前边界和需要进一步验证的指标"。

---

## Week 5：Agent Loop、ReAct、Context Engine、Tool Runtime、Agent 框架选型 ADR

**本周目标**：理解 Agent 的核心抽象，掌握 ReAct 模式，**深入 Agent Harness 的 Loop Engine、Context Engine、Tool Runtime 三大组件**，**产出 Agent 框架选型 ADR**。启动作品②。

### Day 1（4h）：Agent 核心概念 [A]

- Agent vs Workflow vs Agentic Workflow 的区别
- Tool Calling 作为 Agent 的执行原语
- ReAct（Reasoning + Acting）模式
- Plan-and-Execute 模式
- Reflection、Self Correction

### Day 2（4h）：Agent Harness 参考能力模型 [B]

- **明确表述**：这是参考能力模型，非行业标准
- **七大能力域**（根据企业 Agent 运行需求拆分）：
  - Agent Loop Engine：模型调用、工具调用、结果回填、停止条件、最大步骤、执行预算、异常处理
  - Context Engine：组装 System Prompt、用户输入、会话历史、记忆、RAG 结果、工具结果、业务状态；上下文超长时裁剪、摘要、压缩
  - Tool Runtime：工具注册、Schema、参数校验、鉴权、超时、重试、幂等、隔离、沙箱、结果标准化
  - State 与 Checkpoint Engine：状态持久化、断点恢复、状态版本、人工修改、任务重放、失败恢复
  - Policy Engine：模型权限、工具权限、风险判断、人工审批、数据访问范围、执行预算
  - Artifact 与 Workspace：文件、报告、代码、图片、中间结果、业务对象
  - Trace 与 Evaluation Hooks：模型调用、工具调用、状态变化、成本、延迟、错误、评测结果
- **不重复开发框架已提供的能力**：OpenAI Agents SDK 已含 Loop/工具/Handoff/Guardrails/Tracing；自研侧重理解核心机制和企业扩展点

### Day 3（4h）：Agent Loop Engine 实战 [A]

- 实现 Agent Loop：模型调用 → 工具调用 → 结果回填 → 继续执行 → 停止条件
- 最大步骤数、最大 Token 数
- 异常处理与重试
- 执行预算（Token / 调用次数 / 时长）

### Day 4（4h）：Context Engine 实战 [A]

- 组装 System Prompt + 用户输入 + 会话历史 + RAG 结果 + 工具结果
- 上下文超长时的裁剪策略
- 摘要与压缩
- 记忆管理（短期/长期）

### Day 5（4h）：Tool Runtime 实战 + Agent 框架选型 ADR [A] + [C]

- 工具注册与 Schema 管理 [A]
- 参数校验与鉴权 [A]
- 超时、重试、幂等 [A]
- 沙箱执行与隔离 [A]
- 结果标准化 [A]
- **Agent 框架选型 ADR（v3.1 新增）[C]**：
  - 比较 LangGraph / Spring AI / OpenAI Agents SDK
  - 维度：编排模型、状态管理、HITL、工具体系、MCP、多 Agent、可观测性、部署方式、技术语言、锁定风险、团队适配
  - **明确区分两种模式**：Agents as Tools（主 Agent 保留控制权，子 Agent 返回结果）vs Handoff（控制权转移给新 Agent）
  - 产出：选型决策文档

### Day 6（4h）：作品②启动：特药理赔 Agent 需求设计 [A]

- 场景：用户上传理赔材料（医疗单据、处方、保单）
- 流程：OCR → 信息抽取 → 保单匹配 → 规则校验 → 知识库查询 → 人工审批 → 结论
- 设计 Agent 状态结构
- 识别所需工具：OCR、保单查询、规则引擎、知识库检索
- 用 Agent Harness 参考模型重新设计（Loop/Context/Tool/State/Policy）

### Day 7（4h）：OpenAI Agents SDK 学习 + Java 恢复 [B]

- OpenAI Agents SDK：Agent / Runner / Tool / Handoff / Guardrail / Session / Trace
- 对比 LangChain Agent 抽象
- **Java 恢复**：Spring AI ChatClient + Advisor

**本周产出：**

1. Agent Loop Engine + Context Engine + Tool Runtime 实现 [A]
2. **Agent 框架选型 ADR 文档** [C]
3. **作品②需求设计文档**（基于 Agent Harness 参考模型）[A]
4. OpenAI Agents SDK 实践 demo [B]

---

## Week 6：LangGraph、Checkpoint、HITL、中断恢复

**本周目标**：掌握 LangGraph 的核心抽象，能用 LangGraph 实现带中断恢复和 Human in the Loop 的复杂 Agent。

### Day 1（4h）：LangGraph 核心概念 [A]

- State、Node、Edge、Conditional Edge
- Checkpointer（状态持久化）
- Interrupt（中断机制）
- Command（恢复机制）
- Subgraph（子图）
- **LangGraph 已有 Durable Execution、Interrupt、HITL、失败恢复**（避免与 Temporal 重复建设）

### Day 2（4h）：LangGraph 实战①：状态图 + 条件分支 [A]

- 定义 State（含 messages、tool_results、current_step）
- 实现 Node：OCR、抽取、校验、检索、生成
- 实现 Conditional Edge（基于校验结果路由）
- Tool Node 集成

### Day 3（4h）：LangGraph 实战②：Checkpoint + 中断恢复 [A]

- 使用 MemorySaver / SqliteSaver / PostgreSQL Checkpointer 持久化
- 实现 Interrupt（如等待人工审批）
- 实现 Command（恢复执行）
- 长任务断点续跑

### Day 4（4h）：LangGraph 实战③：Human in the Loop [A]

- 在关键节点插入人工审批
- 审批通过/驳回的分支处理
- 审批超时降级
- 人工修改 Agent 状态

### Day 5（4h）：作品②推进：理赔 Agent 核心流程 [A]

- 用 LangGraph 搭建理赔 Agent 骨架
- 集成 W2 的工具：OCR、保单查询、规则引擎
- 集成 W4 的 RAG 知识库
- 实现状态图：OCR → 抽取 → 校验 → 检索 → 审批 → 结论

### Day 6（4h）：作品②推进：人工审批与中断恢复 [A]

- 理赔金额 > 5000 元触发人工审批
- 审批中断 → 状态持久化 → 异步恢复
- 审批驳回 → 修改 Agent 状态 → 重新执行
- Trace 记录全程

### Day 7（4h）：整合 + Java 恢复

- 作品②核心流程跑通
- LangGraph 流程图绘制
- **Java 恢复**：Spring AI RAG Advisor（与本周 RAG 集成呼应）

**本周产出：**

1. LangGraph 实战 demo（含 Checkpoint、Interrupt、HITL）[A]
2. **作品②核心流程跑通**：理赔 Agent 状态机版本 [A]
3. Spring AI RAG Advisor 集成 demo [B]

---

## Week 7：Agent Harness 参考模型、幂等、重试、长任务与业务 Workflow 边界

**本周目标**：深入 Agent Harness 完整组件（参考模型），**Temporal 作为可选对比 Demo**，明确 LangGraph 与 Workflow Engine 的边界。

### Day 1（4h）：Agent Harness State 与 Policy Engine [B]

- State 与 Checkpoint Engine：状态持久化、断点恢复、状态版本、人工修改、任务重放、失败恢复
- Policy Engine：模型权限、工具权限、风险判断、人工审批、数据访问范围、执行预算
- Artifact 与 Workspace：文件、报告、代码、图片、中间结果、业务对象管理
- **重点**：通过接口设计和架构图表达，不强制完整实现

### Day 2（4h）：生产级持久任务编排概念 [C]

- **LangGraph vs Workflow Engine 边界**：
  - LangGraph 管理 Agent 内部的状态和推理流程
  - Workflow Engine 管理跨小时、跨天、跨系统的业务流程
  - Kafka 或消息队列处理事件传递和系统解耦
  - 数据库保存最终业务状态和审计结果
- **状态归属明确**：
  - Agent 消息、工具结果、推理阶段 → LangGraph
  - 理赔单业务状态 → MySQL 或 PostgreSQL
  - 跨系统流程、等待、超时、补偿 → Temporal（可选）
  - 事件通知与系统解耦 → Kafka
- 幂等、重试与退避、补偿机制、任务超时、任务取消
- 消息队列、定时任务、异步回调、事件驱动
- 长任务恢复、多实例并发、状态一致性

### Day 3（4h）：Temporal 入门（可选，Java SDK）[B-可选]

- **Temporal 用 Java SDK**（与用户技术背景匹配）
- Temporal 安装与基础概念
- Workflow 与 Activity
- 实现一个简单的持久工作流
- 对比 LangGraph 的 Checkpoint 机制
- **如果不学 Temporal**：用 LangGraph + PostgreSQL Checkpointer + 业务数据库 + 消息队列模拟也能完成作品②

### Day 4（4h）：作品②业务编排设计 [A] + [C]

- 理赔流程的跨系统编排设计：
  - OCR（W2 工具）→ LangGraph Agent 内部流程
  - 人工审批（跨小时/跨天）→ LangGraph Interrupt 或 Temporal Workflow（可选）
  - 结果回写 → 数据库 + 审计
- 幂等设计：同一理赔单重复提交只处理一次
- 补偿机制：审批驳回后的回滚
- **状态归属图绘制**

### Day 5（4h）：多 Agent 协作（压缩到 8h，含 Day 5-6）[B] + [C]

- Router Agent（中央路由）[B]
- Supervisor Agent（中央调度）[B]
- **Agents as Tools vs Handoff 明确区分**：
  - Agents as Tools：主 Agent 保留控制权，子 Agent 返回结果
  - Handoff：控制权转移给新 Agent，由新 Agent 接管后续对话
- LangGraph Supervisor 模式实现 [B]
- 什么时候用多 Agent：多个独立领域、上下文隔离、并行专业任务、跨系统自治协作 [C]
- **什么时候不用多 Agent**：简单理赔流程拆成 OCR Agent/Rule Agent/Knowledge Agent/Review Agent 会增加延迟、Token 成本、错误传播、调试难度 [C]

### Day 6（4h）：A2A 协议（压缩到 4h）+ 作品②多 Agent（可选）[C]

- A2A vs MCP 的层级差异（MCP 工具接入，A2A Agent 间协作）[C]
- A2A 核心概念：Agent Card、Task、Message [C]
- 能力发现机制 [C]
- 跨框架、跨厂商 Agent 通信 [C]
- **作品②可选升级**：如果时间允许，把单 Agent 升级为多 Agent（OCR Agent + Knowledge Agent + Rule Agent + Review Agent + Supervisor）[B-可选]

### Day 7（4h）：整合 + Java 恢复

- 作品②业务 Workflow 版本跑通
- Agent Harness 参考能力模型完整图绘制
- **Java 恢复**：Spring AI MCP Client（与本周 MCP 呼应）

**本周产出：**

1. Agent Harness 参考能力模型实现（核心组件实现，其余接口设计）[B]
2. Temporal 持久工作流 demo（可选）[B-可选]
3. **作品②业务 Workflow 版本**：理赔 Agent + 业务编排 [A]
4. A2A 协议笔记 [C]

> **v3.1 关键变化**：Temporal 改为可选，作品②第一版只用 LangGraph + PostgreSQL Checkpointer + 业务数据库 + 消息队列模拟。Temporal 作为独立对比 Demo，用 Java SDK。

---

## Week 8：评测四层、Trace、OpenTelemetry、LLMOps（必须实现+只做设计）、作品二（投简历节点）

**本周目标**：掌握 Agent 系统的评测与可观测性建设，**LLMOps 拆成必须实现 + 只做设计**，完成作品②，第8周末投第二批简历。

### Day 1（4h）：Trace 与可观测性 [A] + [B]

- Trace / Span / Prompt 记录 [A]
- 模型调用记录、工具调用记录 [A]
- Agent 执行轨迹记录（结构化：计划、模型输出、工具调用、参数、状态迁移、决策依据）[A]
- **不依赖模型隐藏推理过程**
- OpenTelemetry 概念 [B]
- LangSmith / Langfuse 体验 [B]

### Day 2（4h）：Token 与成本看板 [A]

- Token 消耗统计（按 Agent / Tool / 用户）
- 成本计算与看板
- 延迟与错误率监控
- 实时告警机制

### Day 3（4h）：四层测试体系 [A]

1. **工具单元测试**：验证 OCR、数据库查询、规则引擎、RAG 工具的输入输出
2. **流程集成测试**：验证状态流转、条件分支、异常重试、人工审批
3. **模型离线评测**：验证场景识别、字段抽取、检索召回、答案正确率、工具选择
4. **线上业务指标**：验证任务完成率、人工接管率、错误率、平均成本、平均时延、业务处理效率

测试 Case 从 20-30 个扩充到 50-100 个，按错误类型分组：

- OCR 错误 / 抽取错误 / 检索错误 / 规则错误 / 工具选择错误 / 参数生成错误 / 幻觉 / 权限错误 / 流程错误

### Day 4（4h）：LLMOps 必须实现 [A]

- **Prompt 版本号**
- **模型与 Embedding 配置版本**
- **评测集版本**
- **一键离线回归脚本**
- **Trace**
- **Token 成本统计**
- **错误 Case 保存**

### Day 5（4h）：LLMOps 只做设计 [C] + 作品②完成

- **灰度发布**（架构设计 + 接口定义）[C]
- **A/B 测试**（架构设计 + 接口定义）[C]
- **自动回滚**（架构设计 + 接口定义）[C]
- **在线漂移监控**（架构设计 + 接口定义）[C]
- **企业级发布审批**（架构设计 + 接口定义）[C]
- **完整链路文档**：需求定义 → 数据集构建 → 原型开发 → 离线评测 → 安全测试 → 灰度发布 → 在线监控 → 错误回流 → 版本优化

### Day 6（4h）：作品②完成 + 简历更新

- 构造理赔场景测试集（50+ case，按错误类型分组）
- 跑四层测试体系
- Trace 看板（每个 case 的执行轨迹可视化）
- LLMOps 闭环：版本管理 + 回归测试 + Trace + 成本 + 错误回流
- README 完整化：架构图、流程图、评测结果、难点、**LLMOps 必须实现 vs 只做设计的边界**
- 简历加入作品②
- 更新面试故事（加入 Agent + Agent Harness + LLMOps 经验）
- **★ 投第二批简历**（Agent 工程师 / 多 Agent 系统 / AI 解决方案架构师）

### Day 7（4h）：面试复盘 + Java 恢复

- 回顾 W4 投出简历后的面试反馈
- 整理被问倒的问题，针对性补缺
- **Java 恢复**：Spring AI Observability（与本周可观测性呼应）

**本周产出：**

1. Trace 看板 + Token 成本看板 [A]
2. 四层测试体系（工具单元 / 流程集成 / 模型离线 / 线上业务）[A]
3. **LLMOps 必须实现**（版本管理 + 回归 + Trace + 成本 + 错误回流）[A]
4. **LLMOps 只做设计**（灰度/A/B/回滚/漂移/审批 架构图 + 接口定义）[C]
5. **作品②完成**：特药理赔 Agent 企业级可演示原型（GitHub + 评测 + Trace + LLMOps）[A]
6. **投第二批简历**

> **面试表达**：原型实现了版本、回归、Trace、成本和错误样本闭环；灰度、A/B 和漂移监控完成了架构设计及接口定义。

> **★ W8 末定位**：作品①②完成 + 具备面试 Agent/解决方案架构师岗位的能力（W4 是市场验证，W8 开始正式投递）

---

## Week 9：Agent 安全、红队测试、OWASP LLM Top 10、权限、审计和数据治理

**本周目标**：掌握 Agent 系统的安全风险与治理机制，能给作品①②加上安全防护层。

### Day 1（4h）：Prompt Injection 防护 [A]

- Direct Prompt Injection
- Indirect Prompt Injection（来自文档/工具返回）
- 防御策略：输入过滤、输出校验、指令隔离
- 工具返回内容隔离

### Day 2（4h）：供应链与 MCP 安全 [C]

- 第三方模型供应链风险
- 第三方 Embedding 和 Reranker 风险
- MCP Server 信任管理
- Tool Poisoning、工具描述注入
- 恶意文档注入、RAG 数据投毒
- 模型输出注入下游系统
- Secret 和 API Key 管理
- 插件和依赖扫描
- MCP Server 白名单、工具版本和签名
- 输出编码和转义
- **OWASP 2025 LLM Top 10**：敏感信息泄露、供应链、数据与模型投毒、不安全输出处理、Excessive Agency

### Day 3（4h）：数据越权与泄露防护 [A]

- 知识库越权（检索时过滤不严）
- 敏感信息泄露（PII / 医疗数据）
- 数据脱敏策略
- 租户隔离

### Day 4（4h）：工具滥用防护 + 审批与审计 [A]

- 任意 SQL 执行风险、任意代码执行风险
- 最小权限原则、工具白名单
- 参数校验与沙箱执行
- Human Approval 机制（高风险动作审批）
- Agent 身份与权限
- 审计日志设计
- 合规要求（医疗数据 HIPAA / 国内数据安全法）

### Day 5（4h）：执行预算 + 红队测试 [A] + [B]

- 执行预算（Token / 调用次数 / 时长）[A]
- 最大步骤数、最大 Token 数 [A]
- 超限降级策略 [A]
- 红队测试：主动攻击自己的 Agent 系统 [B]

### Day 6（4h）：作品①②安全加固 [A]

- 给保险知识库加权限过滤、脱敏
- 给理赔 Agent 加工具白名单、审批、审计
- 记录安全事件日志
- 红队测试 + 修复

### Day 7（4h）：整合 + Java 恢复

- 安全加固后的作品①②测试
- 安全架构图绘制
- **Java 恢复**：Spring Security（与本周安全主题呼应）

**本周产出：**

1. Agent 安全防护层（Prompt Injection 防护、OWASP LLM Top 10、MCP 安全、权限、审计）[A] + [C]
2. 作品①②安全加固版 [A]
3. 安全架构图 + 合规分析文档 [A]

---

## Week 10：模型网关平台版、推理服务、性能、容量、部署交付工程

**本周目标**：把 W1 的轻量模型网关升级为平台化 AI Gateway，掌握推理服务工程，**vLLM 改为基准测试 + 容量规划文档**，**新增部署交付工程**。启动作品③。

### Day 1（4h）：平台化 AI Gateway [A]

- 多模型统一接口
- Provider 适配（OpenAI / 通义 / Claude / 本地）
- 路由策略（按复杂度、按成本、按可用性）
- 主备模型与降级
- Token 统计与限流、熔断、缓存
- 虚拟 API Key、用户和部门配额
- 成本归集、模型访问审计
- 参考：LiteLLM

### Day 2（4h）：推理服务工程 [B]

- vLLM 生产部署
- Continuous Batching、KV Cache
- 模型量化（AWQ、GPTQ、INT4/INT8）
- OpenAI Compatible API

### Day 3（4h）：性能与容量评估（v3.1 修改目标）[B]

- **关键指标**：TTFT（首 Token 延迟）、TPOT（单 Token 输出时间）、端到端延迟、队列时间、Tokens Per Second
- **v3.1 新目标**：在明确模型、量化、上下文、并发和 GPU 条件后，完成一次基准测试，并形成容量规划和成本测算文档
- 需要先明确：权重精度/量化格式、最大上下文长度、输入输出 Token 分布、并发请求数量、KV Cache 占用、Tensor Parallel 数量、GPU 型号和显存、TTFT/TPOT 服务目标、是否允许请求排队
- **没有 GPU 时**：小模型实测 + 32B 公式估算和方案设计，不伪造运行数据

### Day 4（4h）：成本测算实战 [B]

- 不同模型的成本对比（按 token 计费 vs 私有部署）
- GPU 成本估算（A100/H100 租用 vs 自建）
- 不同场景的成本优化策略
- 容量规划文档

### Day 5（4h）：部署交付工程（v3.1 新增）[B]

- Docker 和 Docker Compose
- 开发、测试、生产环境隔离
- 配置和 Secret 管理
- Health Check 和 Readiness Check
- CI/CD 基础流程
- 数据库迁移
- 日志、指标和 Trace 采集
- API 限流
- 容器资源限制
- Kubernetes 基础部署架构
- 灰度和回滚方案
- **不深入 K8s 运维，但要能画出生产部署拓扑，说明每个组件如何扩容和容灾**

### Day 6（4h）：作品③启动：企业 Agent 平台骨架 [A]

- 基于 Spring AI 搭建 Java 后端
- 模型网关模块（W1 轻量版升级为平台版）
- Tool Registry + MCP Registry
- Prompt 管理
- 基础 Trace

### Day 7（4h）：架构图与文档 + Java 强化

- 完整 AI 中台架构图
- 组件职责说明
- 技术选型决策文档
- 与作品①②的集成关系图
- **Java 强化**：Spring AI 全模块串讲

**本周产出：**

1. 平台化 AI Gateway（路由、降级、限流、缓存、Token 预算、虚拟 API Key、成本归集、审计）[A]
2. 推理服务工程 demo（vLLM + 性能指标）[B]
3. **成本测算与容量规划文档**（基于基准测试或公式估算）[B]
4. **部署交付工程文档**（生产部署拓扑 + 扩容容灾方案）[B]
5. **作品③骨架**：企业 Agent 平台最小版 [A]

---

## Week 11：Spring AI、企业系统集成、平台最小版本

**本周目标**：把 Java 从"恢复"提升到"能面试"水平，Spring AI 实战整合，推进作品③核心模块落地。

### Day 1（4h）：Java 八股复习 [A]

- Java 集合（HashMap / ConcurrentHashMap 原理）
- Java 并发（线程池、锁、CAS、AQS）
- JVM（内存模型、GC、调优）

### Day 2（4h）：Spring Boot + 中间件 [A]

- Spring Boot 核心机制
- Redis 缓存与分布式锁
- MySQL 索引与优化
- Kafka 消息队列

### Day 3（4h）：Spring AI 深入 [A]

- ChatClient + Advisor 高级用法
- ToolCallback + Structured Output
- Chat Memory（持久化对话）
- VectorStore 抽象（接 ES）

### Day 4（4h）：Spring AI MCP 集成 [A] + [B]

- Spring AI 作为 MCP Client [A]
- Spring AI 实现 MCP Server [A]
- Observability 集成 [B]
- 把 W2 的 Python MCP Server 接入 Spring AI [B]

### Day 5（4h）：作品③核心模块落地 [A]

- 模型网关（Spring AI 实现）
- Tool Registry + MCP Registry
- Prompt 管理 + 版本控制
- Trace + Token 成本看板
- **简化原则**：Knowledge Base、Evaluation Center、IAM、Application Marketplace 先做架构设计和接口定义，不完整实现

### Day 6（4h）：作品③权限与审计 [A]

- Spring Security 集成
- RBAC 权限模型
- 审计日志
- 多租户隔离

### Day 7（4h）：整合 + 面试准备

- 作品③核心模块跑通
- Java 面试题刷题（重点：并发、JVM、Spring）
- Spring AI 面试题整理

**本周产出：**

1. Java 八股复习笔记 + 面试题集 [A]
2. Spring AI 深度实战 demo [A]
3. **作品③核心模块落地** [A]

---

## Week 12：作品打磨、系统设计、Java 高频知识和模拟面试（投第三批简历）

**本周目标**：三个作品全部打磨完成，GitHub 整理，模拟面试，投第三批简历（含架构师/技术负责人岗）。

### Day 1（4h）：作品①最终打磨

- README 终版（架构图、流程图、评测结果、难点、优化点、**AI 数据工程亮点**）
- 代码清理、注释完善
- 录制演示视频（3-5 分钟）
- Demo 在线部署（HuggingFace Space / Streamlit Cloud）

### Day 2（4h）：作品②最终打磨

- README 终版（Agent Harness 参考模型架构图、业务编排图、Trace 看板截图、评测结果、**LLMOps 必须实现 vs 只做设计的边界**）
- 理赔流程演示视频
- 在线 Demo 部署

### Day 3（4h）：作品③最终打磨

- README 终版（AI 中台架构图、模块说明、**部署交付拓扑**）
- 架构决策文档
- 部署文档

### Day 4（4h）：GitHub 整体整理

- 个人 GitHub 主页优化（README、置顶三个作品）
- 建立 Portfolio 仓库（导航到三个作品）
- 技术博客发布（1-2 篇深度文章）

### Day 5（4h）：简历终版 + 投第三批

- 简历加入三个作品
- 更新面试故事（含架构 + Agent Harness + LLMOps + 推理服务 + 部署交付经验）
- **★ 投第三批简历**（含 AI 解决方案架构师、企业 GenAI 架构师、技术负责人岗）

### Day 6（4h）：模拟面试

- RAG 方向 10 题模拟（自答 + 复盘）
- Agent 方向 10 题模拟
- **系统设计题模拟**：设计一个医疗 AI 平台（重点考察架构能力）
- Java 八股模拟

### Day 7（4h）：查漏补缺 + 反问准备

- 回顾所有面试反馈，补最后缺口
- 准备反问清单（团队、技术栈、数据基础、AI 落地阶段、LLMOps 现状）
- 面试当天 checklist

**本周产出：**

1. 三个作品最终版（GitHub + 在线 Demo + 演示视频）
2. 简历终版 + 面试故事全集
3. **投第三批简历**
4. 模拟面试复盘记录

> **★ W12 末定位**：三个作品完成 + 简历三批投完 + 具备面试 AI 解决方案架构师/企业 GenAI 架构师的能力

---

## 投简历节点与策略（v3.1 调整）

### 三个投简历节点（定位调整）

| 节点      |   时间  | 已有作品                             | 定位                       | 主投方向                                                    |
| ------- | :---: | -------------------------------- | ------------------------ | ------------------------------------------------------- |
| **第一批** |  W4 末 | 作品①（保险知识库）                       | **市场验证、测试简历定位、获取真实面试问题** | 行业高度匹配的 AI 解决方案岗位、RAG/知识库应用岗位、医疗保险 AI 应用岗、部分 AI 项目负责人岗位 |
| **第二批** |  W8 末 | 作品①②（含 Agent + Harness + LLMOps） | 正式投递                     | Agent 工程师、多 Agent 系统、AI 解决方案架构师、FDE                     |
| **第三批** | W12 末 | 作品①②③（含平台 + 推理服务 + 部署交付）         | 集中投递高级岗                  | AI 解决方案架构师、企业 GenAI 架构师、技术负责人                           |

### 主投公司方向

| 类别           | 代表公司                              |
| ------------ | --------------------------------- |
| 医疗 AI        | 医联、丁香园 AI、平安好医生 AI、零氪科技、推想科技、深透医疗 |
| 保险科技         | 众安科技、信美相互 AI、水滴 AI、暖哇科技、保险极客      |
| 健康险/寿险 AI 中台 | 平安、泰康、太平洋 AI 团队                   |
| 医疗大数据        | 医渡云、零氪、中电数据                       |
| 传统企业 AI 部门   | 各行业正在建 AI 团队的企业                   |
| AI 创业公司（应用岗） | 智谱、月之暗面、MiniMax、百川（非算法岗）          |

### 暂时减少投递

- 纯 Agent 开发工程师（高强度现场编码）
- 强算法工程师
- 模型训练工程师
- 推理引擎工程师
- 要求现场独立完成大量代码的平台核心开发岗位

---

## 风险与应对

**风险1：时间不足**

> 应对：每周日做一次进度检查，如果落后超过 2 天，压缩 [C] 级内容（A2A、多 Agent、完整 LLMOps、模型集群扩缩容、Temporal 深入）。

**风险2：面试早于学习完成**

> 应对：W4 末投第一批简历（定位市场验证），可能立即收到面试。遇到答不上的问题，**不说"预计 X 周后能上手"**，改为"该部分完成了原理研究和原型验证，尚未经历生产规模验证。我可以说明设计方案、当前边界和需要进一步验证的指标"。

**风险3：双语言（Python + Java）负担过重**

> 应对：Python 为主线（LangGraph），Java 为辅线（Spring AI，每周日恢复）。如果 Java 进度跟不上，作品③可以简化为"架构图 + 核心模块 demo"，不用完整实现。

**风险4：医疗数据获取困难**

> 应对：使用公开保险条款 PDF（重疾险/医疗险）、公开医疗文档、合成数据。不使用真实患者数据。

**风险5：Agent Harness 学习曲线陡**

> 应对：Agent Harness 是参考能力模型，非行业标准。**重点实现 Loop/Context/Tool Runtime/Trace 四个核心组件 [A]，State/Policy/Workspace 通过接口设计和架构图表达 [B]**。不重复开发框架已提供的能力。

**风险6：Temporal 学习曲线陡（v3.1 调整为可选）**

> 应对：**Temporal 改为可选**。作品②第一版只用 LangGraph + PostgreSQL Checkpointer + 业务数据库 + 消息队列模拟。Temporal 作为独立对比 Demo，用 Java SDK。如果不学 Temporal，作品②也能完成。

**风险7：LLMOps 实现复杂（v3.1 拆分）**

> 应对：LLMOps 拆成"必须实现"（版本管理 + 回归 + Trace + 成本 + 错误回流）[A] +"只做设计"（灰度/A/B/回滚/漂移/审批）[C]。面试时如实表达边界。

**风险8：vLLM 没有 GPU 实测条件**

> 应对：小模型实测 + 32B 公式估算和方案设计，不伪造运行数据。

---

## A/B/C 三级分类速查表

| 级别                    | 要求                     | 内容清单                                                                                                                                                       |
| --------------------- | ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **[A] 必须亲自实现并能讲代码**   | 亲自写代码 + 能讲清原理和细节       | RAG 全流程、AI 数据工程、Tool Calling、LangGraph、Agent Loop、Context Engine、Tool Runtime、Trace、基础模型网关、平台化 AI Gateway、Spring AI 核心集成、四层测试体系、LLMOps 必须实现部分、安全加固、Java 八股 |
| **[B] 跑通 Demo 并能讲架构** | 跑通官方 Demo + 能讲架构图和选型理由 | MCP Server/Client、Temporal（可选）、vLLM 部署、OpenAI Agents SDK、Spring AI MCP、OpenTelemetry、LangSmith/Langfuse、Agent Harness 非核心组件、红队测试、部署交付工程、知识库平台对比体验          |
| **[C] 理解原理并能做选型**     | 读文档 + 能讲清概念和选型维度       | A2A、多 Agent 深入、完整 LLMOps（灰度/A/B/回滚/漂移）、模型集群扩缩容、OWASP LLM Top 10、知识库选型 ADR、Agent 框架选型 ADR、业务 Workflow 边界                                                    |

---

## 面试叙事线（v3.1 形成的完整故事）

> 从企业数据和知识库建设出发，完成 RAG、Agent、Harness、评测、安全和平台化，再通过 Java 与现有企业系统集成，最终具备医疗保险 AI 方案设计和落地能力。

**三个面试故事升级版：**

1. **数据治理 → RAG + AI 数据工程**："我做了 X 年数仓，知道企业数据质量是 AI 落地最大的坑。我做的 RAG 不是简单扔文档进向量库，而是有增量摄取、数据血缘、权限过滤、混合检索和评测闭环的保险知识库——这是我跟纯应用工程师的差别。"
2. **Java 架构 → AI 工程化 + 平台化**："我做过 Java 后端架构，知道怎么把新能力稳定接进现有系统。大模型 demo 好做，但上生产要处理模型网关、工具注册、Trace、成本管控、部署交付——这些都是工程问题，不是算法问题。"
3. **医疗行业 → 差异化定位**："我有医疗/保险行业经验，懂业务流程、合规要求、数据特点。医疗 AI 恰恰是最需要懂业务+懂数据+懂合规的赛道，纯 AI 工程师玩不转，这是我的主场。"

---

## v3 vs v3.1 关键差异总结

| 维度            | v3                   | v3.1                                                  |
| ------------- | -------------------- | ----------------------------------------------------- |
| 内容深度控制        | 全部"完整实现"             | **A/B/C 三级分类**                                        |
| Agent Harness | "完整实现七大组件"           | **参考能力模型，核心组件实现，其余接口设计**                              |
| Temporal      | 必学，Go/Python SDK     | **可选，Java SDK，作品②第一版不强制接入**                           |
| MCP Transport | stdio/SSE/HTTP       | **stdio/Streamable HTTP（旧 HTTP+SSE 已废弃）**             |
| 框架选型方法论       | 无                    | **W3 知识库选型 ADR + W5 Agent 框架选型 ADR**                  |
| AI 数据工程       | 无                    | **W3-W4 新增，用户差异化优势**                                  |
| RAG 评测        | RAGAS + LLM as Judge | **+ 确定性检索指标（Hit Rate@K/Recall@K/MRR/nDCG@K）+ 9 类测试集** |
| 部署交付          | 无                    | **W10-W11 新增**                                        |
| vLLM 目标       | "32B 需要多少 GPU"       | **基准测试 + 容量规划文档（条件明确后）**                              |
| LLMOps        | 全部实现                 | **必须实现 [A] + 只做设计 [C]**                               |
| W4 投递定位       | "已具备面试解决方案架构师能力"     | **"市场验证、测试简历定位、获取真实面试问题"**                            |
| 面试话术          | "预计 X 周后能上手"         | **"完成了原理研究和原型验证，尚未经历生产规模验证"**                         |

---

*文档版本：v3.1 · 2026-07-21*  
*基于 ChatGPT 对 v3 的评估优化*  
*核心方法：A/B/C 三级分类法 + 参考模型标注 + 可选模块 + 选型方法论 + 差异化优势强化*  
*用户画像：36 岁 / Java+大数据 / 医疗保险行业 / 每天可投入 4 小时 / 目标 AI 解决方案架构师*
