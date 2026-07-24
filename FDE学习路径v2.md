# FDE 面试学习路径 v2

> 12周系统化方案 · 基于用户画像定制 · 每天可投入 4 小时（每周 28 小时）· 可直接评估版本

---

## 一、用户画像与方案前提

| 维度 | 内容 |
|---|---|
| 年龄 | 36 岁 |
| 技术背景 | Java + 大数据（Hadoop / 数仓 / 数据建模 / SQL），做过架构相关（非大模型架构） |
| 行业背景 | 医疗 / 保险 / 健康 相关（核心差异化优势） |
| 大模型水平 | 会调 API、用过 ChatGPT 类工具；RAG / Agent / 本地部署均未做过 |
| 求职方向 | FDE / AI 应用工程师 / RAG 工程师 / Text2SQL 工程师（目标中小厂 + 医疗 AI / 保险科技公司） |
| 出差偏好 | 基本不想出差（不投纯驻场 FDE，主投远程/本地交付的 AI 应用岗） |
| 时间预算 | **每天 4 小时，每周 28 小时**（在职但时间稳定） |
| 12 周总时长 | 336 小时（充足，可执行系统化方案） |

---

## 二、方案设计原则与核心策略

**核心策略：** 大模型应用架构与 Agent 工程为核心，RAG 和企业知识库为基础，平台化、评测和治理形成高级岗位竞争力，Java 并行恢复不集中堆后。

### 五条设计原则

1. **学用一体**：每周学习内容必须当周产出可运行代码或文档，不做"学完才做"。
2. **作品贯穿全程**：三大作品按学习节奏分散到 W3-W10 完成，不堆在最后两周。
3. **Java 并行恢复**：每周抽固定时间（周日上午）恢复 Java，不集中到 W12；Spring AI 作为 Java × AI 交叉点，从 W5 开始引入。
4. **投简历节点明确**：W4 末投第一批（作品①完成），W8 末投第二批（作品②完成），W12 末投第三批（作品③完成）。
5. **医疗行业贯穿**：所有 demo 与作品使用医疗/保险数据与场景，形成差异化壁垒。

### 学习投入分配比例

| 模块 | 占比 | 小时数 | 对应周 |
|---|:---:|:---:|---|
| Agent 与 Agent Runtime | 25% | 84h | W5-7 |
| RAG 与企业知识库 | 25% | 84h | W3-4 |
| 模型应用基础与 Tool Calling | 15% | 50h | W1-2 |
| 评测、可观测性与安全 | 15% | 50h | W8-9 |
| AI 平台架构 | 10% | 34h | W10 |
| Java 恢复（并行） | 10% | 34h | W1-12 每周日 |

---

## 三、12周总览

| 周 | 主线主题 | 核心产出 | 并行 Java | 作品进度 | 投简历 |
|:---:|---|---|---|---|:---:|
| W1 | 大模型基础与模型调用 | API 调用程序 + 流式输出 + Prompt 模板 | Java 集合并发复习 | — | |
| W2 | Tool Calling 与 MCP | OCR Tool + MCP Server/Client | Spring Boot 复习 | — | |
| W3 | RAG 基础 | 文档摄取 + pgvector + 检索 demo | Spring AI 入门 | 作品① 启动 | |
| W4 | 企业级 RAG | ES 混合检索 + rerank + 权限 | Spring AI Tool Calling | 作品① 完成 | **★ 投第一批** |
| W5 | Agent 基础 | ReAct + Tool Loop + Trace | Spring AI ChatClient | 作品② 启动 | |
| W6 | LangGraph 状态机 | 状态图 + Checkpoint + HITL | Spring AI RAG Advisor | 作品② 推进 | |
| W7 | 多 Agent 与 A2A | Router + Supervisor + Handoff | Spring AI MCP Client | 作品② 推进 | |
| W8 | 评测与可观测性 | 测试集 + Trace 看板 + RAGAS | Spring AI Observability | 作品② 完成 | **★ 投第二批** |
| W9 | Agent 安全与治理 | 白名单 + 审批 + 审计 + 执行预算 | Spring Security | — | |
| W10 | 大模型平台架构 | AI 中台架构图 + 核心模块 demo | Spring AI 整合 | 作品③ 启动 | |
| W11 | Java 强化 + Spring AI 实战 | 作品③ 核心模块落地 | Java 重点投入 | 作品③ 推进 | |
| W12 | 面试冲刺 + 作品打磨 | 三个作品 GitHub 整理 + 模拟面试 | Java 八股冲刺 | 作品③ 完成 | **★ 投第三批** |

---

## 四、技术栈与框架选型

### 主框架组合（一主一辅一企业集成）

| 定位 | 框架 | 语言 | 用途 |
|---|---|---|---|
| **主攻** | LangGraph | Python | Agent 状态机编排、长任务、HITL、中断恢复 |
| **企业集成** | Spring AI | Java | 企业 Java 系统接入 AI、Tool Calling、RAG、MCP |
| **辅修** | OpenAI Agents SDK | Python | 快速理解标准 Agent Runtime 抽象 |

### 向量数据库学习顺序

| 阶段 | 选型 | 理由 | 使用周 |
|---|---|---|:---:|
| 第一阶段 | PostgreSQL + pgvector | 上手快、可练权限与向量统一管理 | W3 |
| 第二阶段 | Elasticsearch 混合检索 | 用户有 ES 经验，医疗编码/药品名需 BM25+向量 | W4 |
| 第三阶段 | Milvus | 大规模平台架构面试加分 | W10 |

### 知识库平台选型

| 方案 | 使用场景 | 使用周 |
|---|---|:---:|
| Dify | POC、快速验证、低代码理解 | W3（对比体验） |
| RAGFlow | 复杂文档解析研究（扫描件、表格） | W4（研究源码） |
| 自研 | 面试作品、企业生产系统 | W3-4 作品① |

---

## 五、三大作品定位与产出节奏

| 作品 | 主题 | 技术栈 | 制作周 | 验证能力 | 优先级 |
|---|---|---|:---:|---|:---:|
| **作品①** | 企业保险知识库（RAG） | Python + LangChain + pgvector + ES + BGE + bge-reranker | W3-4 | RAG、知识库、数据架构 | 必做 |
| **作品②** | 特药理赔 Agent（LangGraph） | Python + LangGraph + OCR Tool + RAG Tool + MySQL Tool + 规则引擎 + HITL | W5-8 | Agent、FDE 落地、长任务编排 | 必做 |
| **作品③** | 企业 Agent 平台最小版（Spring AI） | Java + Spring AI + 模型网关 + Tool Registry + MCP + Trace | W10-12 | 平台架构、Java 工程化、AI 中台 | 选做 |

---

## Week 1：大模型基础与模型调用

**本周目标**：建立大模型完整基础知识体系，能独立调用主流模型 API，理解 token、上下文窗口、采样参数的本质，能写生产级 Prompt 模板。

### Day 1（4h）：大模型原理（宏观）
- Transformer 架构、Self-Attention、Multi-Head Attention
- Token / Tokenizer / BPE / Tiktoken
- Embedding 的本质与维度
- 上下文窗口、KV Cache
- Prefill vs Decode 阶段
- 资源：李宏毅大模型课程 B 站（选看 Transformer 那几节）

### Day 2（4h）：采样参数与输出控制
- Temperature、Top P、Top K、Stop Sequences
- Structured Output（JSON Mode、JSON Schema）
- Function Calling 概念入门
- 推理模型（o1/o3/DeepSeek-R1）vs 普通对话模型的差异
- 多模态模型（视觉理解）的能力边界

### Day 3（4h）：模型选型与权衡
- 开源 vs 闭源 vs 本地部署的选择维度
- 准确率、延迟、吞吐、成本、上下文长度的权衡
- SFT、LoRA、量化、蒸馏的概念（不深入算法）
- 模型幻觉成因与缓解策略
- 主流模型对比：GPT-4o / Claude / Qwen / DeepSeek / Llama

### Day 4（4h）：API 调用实战（Python）
- OpenAI / 通义 / 智谱 / DeepSeek API 调用
- 流式输出（SSE）处理
- JSON 结构化输出与校验
- Token 统计与成本计算
- 多模型切换封装

### Day 5（4h）：Prompt 工程基础
- System Prompt / User Prompt / Assistant Prompt
- Zero Shot、Few Shot、Chain of Thought
- CoT 的工程使用边界（什么时候用、什么时候不用）
- Prompt 模板与变量管理
- Prompt Injection 攻击与防御

### Day 6（4h）：Prompt 工程进阶 + 本地部署体验
- 输出格式约束、JSON Schema 强约束
- Prompt 测试集概念
- Prompt A/B 测试概念
- Ollama 本地跑 Qwen3，理解量化与显存占用
- vLLM 概念了解（不深入）

### Day 7（4h）：本周产出整合 + Java 恢复
- 整理本周代码到 GitHub
- 写一篇博客/笔记：模型选型决策框架
- **Java 恢复（每周日上午固定）**：Java 集合、并发基础复习
- Spring Boot 项目骨架搭建

**本周产出：**
1. 多模型 API 调用封装库（Python，支持流式/JSON/Token统计/多模型切换）
2. Prompt 模板管理系统（含测试集概念）
3. 模型选型决策文档（博客/笔记）
4. GitHub 仓库初始化 + Java 项目骨架

---

## Week 2：Tool Calling 与 MCP

**本周目标**：掌握 Function Calling 完整工程实践，理解 MCP 协议（Host/Client/Server/Tools/Resources/Prompts），能独立实现一个 MCP Server 和 MCP Client。

### Day 1（4h）：Function Calling 深入
- OpenAI / Claude / Qwen Function Calling 规范对比
- 工具描述（Tool Schema）写法
- 参数校验与错误处理
- 多工具并行调用
- 多轮工具调用循环

### Day 2（4h）：实战工具开发①：OCR Tool
- 基于 PaddleOCR 或云 OCR 实现医疗单据 OCR
- 包装成 Function Calling 兼容的 Tool
- 参数：图片路径/URL；返回：结构化字段
- 错误处理与降级策略

### Day 3（4h）：实战工具开发②：数据库查询 Tool
- Text2SQL Tool：自然语言 → SQL → 执行 → 返回结果
- 只读权限限制、参数化执行
- 规则引擎 Tool：基于 Drools 或简单规则脚本
- 工具权限与白名单设计

### Day 4（4h）：MCP 协议学习
- MCP Host / Client / Server 三方关系
- Tools / Resources / Prompts 三类能力
- Transport（stdio / SSE / HTTP）
- Tool Discovery、能力协商、进度跟踪、取消
- 权限与认证机制
- MCP 安全风险（Tool Poisoning 等）

### Day 5（4h）：实现一个 MCP Server
- 基于 Python MCP SDK 实现
- 暴露医疗相关 Tools：OCR、保单查询、规则校验
- 实现 Resources：保单模板、规则文档
- 实现 Prompts：理赔审核 Prompt 模板

### Day 6（4h）：实现一个 MCP Client + 工具权限
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
1. OCR Tool + DB Query Tool + Rule Engine Tool（Function Calling 兼容）
2. 一个可运行的 MCP Server（医疗场景）
3. 一个 MCP Client + 工具权限白名单
4. Spring Boot 项目骨架完善

---

## Week 3：RAG 基础

**本周目标**：掌握 Naive RAG 完整流程，能用 pgvector 跑通一个文档问答 demo，并启动作品①保险知识库的搭建。

### Day 1（4h）：RAG 概念与流程
- Naive RAG 完整流程：摄取→解析→切分→Embedding→检索→生成
- RAG 四阶段：Naive → Advanced → Modular → Agentic
- LangChain RAG quickstart 跑通
- Dify 知识库体验（对比低代码方案）

### Day 2（4h）：文档解析与清洗
- PyMuPDF / unstructured 解析 PDF
- Word / PPT / Excel 解析
- OCR 与版面分析（PaddleOCR）
- 表格结构识别（Table Transformer）
- 文本清洗：去噪、去重、规范化

### Day 3（4h）：Chunk 切分策略
- 固定长度切分
- 按段落/标题切分
- 滑动窗口
- Parent-Child Chunk
- Sentence Window
- 语义切分（基于 Embedding 相似度）

### Day 4（4h）：Embedding 与 pgvector
- Embedding 模型选型：BGE / GTE / OpenAI ada
- PostgreSQL + pgvector 安装与使用
- HNSW vs IVFFlat 索引对比
- 向量入库、相似度查询
- Metadata 存储与过滤

### Day 5（4h）：检索与生成
- 向量检索（Top-K）
- BM25 关键词检索（pg_trgm 或 ES）
- 引用溯源实现
- 无答案拒答策略
- Prompt 模板：问答 + 引用 + 拒答

### Day 6（4h）：作品①启动：保险知识库基础版
- 数据：5-10 份公开保险条款 PDF（重疾险/医疗险）
- 架构：摄取 pipeline + pgvector + LangChain + 通义 API
- 功能：上传 PDF → chunk → embedding → 检索 → 问答 + 引用
- 放 GitHub，写 README

### Day 7（4h）：整合 + Java 恢复
- 作品①基础版跑通，README 完善
- 画 RAG 流程图
- **Java 恢复**：Spring AI 入门（ChatClient、Model Adapter）

**本周产出：**
1. RAG 基础 demo（pgvector + LangChain）
2. **作品①基础版**：保险知识库最小可用版（GitHub）
3. RAG 流程图笔记
4. Spring AI 入门 demo

---

## Week 4：企业级 RAG（投简历节点）

**本周目标**：把 Naive RAG 升级到 Advanced RAG，引入混合检索、rerank、权限过滤、文档版本管理、RAG 评测。作品①完成生产级版本，第4周末投第一批简历。

### Day 1（4h）：Elasticsearch 混合检索
- ES BM25 检索搭建
- 向量检索 + BM25 融合（RRF 算法）
- 医疗场景适配：药品名、ICD 编码、保单号精确匹配
- Metadata 过滤

### Day 2（4h）：Rerank 与 Query 改写
- bge-reranker 模型使用
- Query Rewrite（HyDE、Query 扩展、Multi-Query）
- Query Decomposition（问题分解）
- 多路召回融合
- Context Compression

### Day 3（4h）：权限控制与文档治理
- chunk 级权限标签（部门/角色）
- 检索时 Metadata filter（用户身份过滤）
- 文档版本管理与失效
- 增量索引策略
- 缓存失效机制

### Day 4（4h）：RAG 评测
- RAGAS 四指标：Faithfulness / Answer Relevancy / Context Precision / Context Recall
- 构造评测集（20-30 个医疗问答对）
- 跑 RAGAS 自动评测
- 分析薄弱环节，针对性优化

### Day 5（4h）：作品①企业级完成
- 整合：pgvector + ES 混合检索 + rerank + 权限 + 评测
- 架构图绘制（draw.io 或 excalidraw）
- README 完整化：架构、难点、优化点、评测结果
- Streamlit/Gradio 前端界面

### Day 6（4h）：简历改造 + 投简历准备
- 简历突出：医疗/保险行业 + 大数据 + Java + RAG 作品
- 三个面试故事定稿（数据治理→RAG、Java架构→工程化、SQL建模→Text2SQL）
- 整理目标公司清单（医疗AI / 保险科技 / 健康险）
- BOSS / 猎聘账号准备

### Day 7（4h）：开始投简历 + Java 恢复
- **★ 投第一批简历**（医疗AI、保险科技公司为主）
- **Java 恢复**：Spring AI Tool Calling（与本周 Tool 主题呼应）
- 准备面试常见问题清单

**本周产出：**
1. 企业级 RAG 系统完整版（混合检索 + rerank + 权限 + 评测）
2. **作品①完成**：保险知识库生产版（GitHub + README + 架构图 + 评测结果）
3. 改造版简历 + 三个面试故事
4. **投第一批简历**

> **★ W4 末里程碑：作品①完成 + 简历投出 + 具备面试 RAG 岗位的能力**

---

## Week 5：Agent 基础

**本周目标**：理解 Agent 的核心抽象，掌握 ReAct、Plan-and-Execute、Reflection 等执行模式，能实现一个带 Trace 和状态管理的 Agent Loop。启动作品②特药理赔 Agent。

### Day 1（4h）：Agent 核心概念
- Agent vs Workflow vs Agentic Workflow 的区别
- Tool Calling 作为 Agent 的执行原语
- ReAct（Reasoning + Acting）模式
- Plan-and-Execute 模式
- Reflection、Self Correction

### Day 2（4h）：Agent 执行模式深入
- Router 模式（动态路由到子 Agent）
- Supervisor 模式（中央调度）
- 状态机模式
- DAG 工作流
- 什么场景用固定工作流，什么场景允许模型动态决策

### Day 3（4h）：Agent 状态与控制
- Agent State（保存在哪里、怎么持久化）
- Agent Memory（短期/长期）
- 避免死循环：最大步骤数、最大 Token 数
- 中断恢复机制
- Human in the Loop（审批、确认）

### Day 4（4h）：Agent 实战①：ReAct Loop
- 用 LangChain 实现一个 ReAct Agent
- 接入 W2 的 OCR Tool + DB Query Tool + Rule Engine Tool
- 实现 Trace 记录（每步思考、工具调用、结果）
- 错误处理与重试

### Day 5（4h）：Agent 实战②：Plan-and-Execute
- 实现 Plan-and-Execute 模式
- 动态任务拆解
- 工具选择策略
- 并行工具调用

### Day 6（4h）：作品②启动：特药理赔 Agent 需求设计
- 场景：用户上传理赔材料（医疗单据、处方、保单）
- 流程：OCR → 信息抽取 → 保单匹配 → 规则校验 → 知识库查询 → 人工审批 → 结论
- 设计 Agent 状态结构
- 识别所需工具：OCR、保单查询、规则引擎、知识库检索

### Day 7（4h）：OpenAI Agents SDK 学习 + Java 恢复
- OpenAI Agents SDK：Agent / Runner / Tool / Handoff / Guardrail / Session / Trace
- 对比 LangChain Agent 抽象
- **Java 恢复**：Spring AI ChatClient + Advisor

**本周产出：**
1. ReAct Agent + Plan-and-Execute Agent（带 Trace）
2. **作品②需求设计文档** + Agent 状态结构设计
3. OpenAI Agents SDK 实践 demo

---

## Week 6：LangGraph 状态机编排

**本周目标**：掌握 LangGraph 的核心抽象（State/Node/Edge/Checkpointer/Interrupt），能用 LangGraph 实现带中断恢复和 Human in the Loop 的复杂 Agent。

### Day 1（4h）：LangGraph 核心概念
- State、Node、Edge、Conditional Edge
- Checkpointer（状态持久化）
- Interrupt（中断机制）
- Command（恢复机制）
- Subgraph（子图）

### Day 2（4h）：LangGraph 实战①：状态图 + 条件分支
- 定义 State（含 messages、tool_results、current_step）
- 实现 Node：OCR、抽取、校验、检索、生成
- 实现 Conditional Edge（基于校验结果路由）
- Tool Node 集成

### Day 3（4h）：LangGraph 实战②：Checkpoint + 中断恢复
- 使用 MemorySaver / SqliteSaver 持久化
- 实现 Interrupt（如等待人工审批）
- 实现 Command（恢复执行）
- 长任务断点续跑

### Day 4（4h）：LangGraph 实战③：Human in the Loop
- 在关键节点插入人工审批
- 审批通过/驳回的分支处理
- 审批超时降级
- 人工修改 Agent 状态

### Day 5（4h）：作品②推进：理赔 Agent 核心流程
- 用 LangGraph 搭建理赔 Agent 骨架
- 集成 W2 的工具：OCR、保单查询、规则引擎
- 集成 W4 的 RAG 知识库
- 实现状态图：OCR → 抽取 → 校验 → 检索 → 审批 → 结论

### Day 6（4h）：作品②推进：人工审批与中断恢复
- 理赔金额 > 5000 元触发人工审批
- 审批中断 → 状态持久化 → 异步恢复
- 审批驳回 → 修改 Agent 状态 → 重新执行
- Trace 记录全程

### Day 7（4h）：整合 + Java 恢复
- 作品②核心流程跑通
- LangGraph 流程图绘制
- **Java 恢复**：Spring AI RAG Advisor（与本周 RAG 集成呼应）

**本周产出：**
1. LangGraph 实战 demo（含 Checkpoint、Interrupt、HITL）
2. **作品②核心流程跑通**：理赔 Agent 状态机版本
3. Spring AI RAG Advisor 集成 demo

---

## Week 7：多 Agent 协作与 A2A

**本周目标**：掌握多 Agent 协作模式（Router/Supervisor/Handoff），理解 A2A 协议，把作品②升级为多 Agent 架构。

### Day 1（4h）：多 Agent 协作模式
- Router Agent（中央路由）
- Supervisor Agent（中央调度）
- Agent Handoff（任务委托）
- Agent Registry / Tool Registry
- 多 Agent 通信机制

### Day 2（4h）：LangGraph 多 Agent 实现
- Supervisor 模式实现
- 子 Agent 定义：OCR Agent、Knowledge Agent、Rule Agent、Review Agent
- Handoff 机制（子 Agent 以工具形式暴露给 Supervisor）
- 并行节点执行

### Day 3（4h）：A2A 协议学习
- A2A vs MCP 的层级差异（MCP 工具接入，A2A Agent 间协作）
- A2A 核心概念：Agent Card、Task、Message
- 能力发现机制
- 长任务协作
- 跨框架、跨厂商 Agent 通信

### Day 4（4h）：作品②升级：多 Agent 架构
- 把单 Agent 理赔流程升级为多 Agent
- OCR Agent：负责材料识别
- Knowledge Agent：负责保单/规则检索
- Rule Agent：负责规则校验
- Review Agent：负责结论生成与解释
- Supervisor 协调

### Day 5（4h）：Agent Runtime 概念
- Agent Runtime 是什么（执行环境、调度、隔离）
- Agent Harness（测试框架）
- 沙箱执行
- 资源限制（CPU/内存/Token/步数）
- 对比 OpenAI Agents SDK 的 Runner 抽象

### Day 6（4h）：作品②完善：Trace 与可观测性初步
- 记录每个 Agent 的执行轨迹
- Token 消耗统计
- 工具调用成功率
- 异常处理与降级

### Day 7（4h）：整合 + Java 恢复
- 作品②多 Agent 版本跑通
- 多 Agent 协作流程图绘制
- **Java 恢复**：Spring AI MCP Client（与本周 MCP 呼应）

**本周产出：**
1. 多 Agent 协作 demo（Router + Supervisor + Handoff）
2. **作品②多 Agent 版本跑通**
3. A2A 协议笔记
4. Spring AI MCP Client demo

---

## Week 8：评测与可观测性（投简历节点）

**本周目标**：掌握 Agent 系统的评测与可观测性建设，完成作品②的评测与 Trace 看板，第8周末投第二批简历。

### Day 1（4h）：Trace 与可观测性
- Trace / Span / Prompt 记录
- 模型调用记录、工具调用记录
- Agent 执行轨迹记录
- OpenTelemetry 概念
- LangSmith / Langfuse 体验

### Day 2（4h）：Token 与成本看板
- Token 消耗统计（按 Agent / Tool / 用户）
- 成本计算与看板
- 延迟与错误率监控
- 实时告警机制

### Day 3（4h）：Agent 评测
- 工具选择准确率
- 参数生成准确率
- 工具调用成功率
- 任务完成率
- 轨迹评测（LLM as Judge）
- 人工评测、对抗测试

### Day 4（4h）：RAG 评测深化
- RAGAS 跑作品①评测集
- 对比不同优化策略的指标变化
- 回归测试机制
- 测试集版本管理

### Day 5（4h）：作品②完成：评测 + Trace 看板
- 构造理赔场景测试集（20-30 个 case）
- 跑 Agent 评测（任务完成率、工具调用成功率）
- Trace 看板（每个 case 的执行轨迹可视化）
- README 完整化：架构图、流程图、评测结果、难点

### Day 6（4h）：简历更新 + 投第二批简历
- 简历加入作品②
- 更新面试故事（加入 Agent 经验）
- 扩展目标公司清单（含 Agent 方向岗位）
- **★ 投第二批简历**（Agent 工程师 / 多 Agent 系统岗）

### Day 7（4h）：面试复盘 + Java 恢复
- 回顾 W4 投出简历后的面试反馈
- 整理被问倒的问题，针对性补缺
- **Java 恢复**：Spring AI Observability（与本周可观测性呼应）

**本周产出：**
1. Trace 看板 + Token 成本看板
2. Agent 评测体系（含测试集、LLM as Judge）
3. **作品②完成**：特药理赔 Agent 完整版（GitHub + 评测 + Trace）
4. **投第二批简历**

> **★ W8 末里程碑：作品①②完成 + 具备面试 Agent 工程师岗位的能力**

---

## Week 9：Agent 安全与治理

**本周目标**：掌握 Agent 系统的安全风险与治理机制，能给作品①②加上安全防护层。这是高级岗位（技术负责人/架构师）的加分项。

### Day 1（4h）：Prompt Injection 防护
- Direct Prompt Injection
- Indirect Prompt Injection（来自文档/工具返回）
- 防御策略：输入过滤、输出校验、指令隔离
- 工具返回内容隔离

### Day 2（4h）：数据越权与泄露防护
- 知识库越权（检索时过滤不严）
- 敏感信息泄露（PII / 医疗数据）
- 数据脱敏策略
- 租户隔离

### Day 3（4h）：工具滥用防护
- 任意 SQL 执行风险
- 任意代码执行风险
- 最小权限原则
- 工具白名单
- 参数校验与沙箱执行

### Day 4（4h）：审批与审计
- Human Approval 机制（高风险动作审批）
- Agent 身份与权限
- 审计日志设计
- 合规要求（医疗数据 HIPAA / 国内数据安全法）

### Day 5（4h）：执行预算与限制
- 执行预算（Token / 调用次数 / 时长）
- 最大步骤数
- 最大 Token 数
- 超限降级策略

### Day 6（4h）：作品①②安全加固
- 给保险知识库加权限过滤、脱敏
- 给理赔 Agent 加工具白名单、审批、审计
- 记录安全事件日志

### Day 7（4h）：整合 + Java 恢复
- 安全加固后的作品①②测试
- 安全架构图绘制
- **Java 恢复**：Spring Security（与本周安全主题呼应）

**本周产出：**
1. Agent 安全防护层（Prompt Injection 防护、权限、审计）
2. 作品①②安全加固版
3. 安全架构图 + 合规分析文档

---

## Week 10：大模型平台架构

**本周目标**：设计一个小型 AI 中台架构，理解模型网关、Registry、Runtime、可观测性等组件。启动作品③企业 Agent 平台最小版。

### Day 1（4h）：AI 中台架构设计
- AI Gateway（模型路由、降级、限流、缓存）
- Model Registry / Prompt Registry / Agent Registry
- Tool Registry / MCP Registry
- Knowledge Base / Vector Store
- Workflow Engine

### Day 2（4h）：Runtime 与可观测性
- Agent Runtime 设计
- Memory Service / State Store
- Evaluation Platform
- Observability Platform
- Guardrails

### Day 3（4h）：治理与成本
- Cost Center（成本归集与分摊）
- IAM（身份与访问管理）
- Audit Center（审计中心）
- Human Approval Center（人工审批中心）
- Application Marketplace

### Day 4（4h）：模型网关实现
- 多模型统一接口
- Provider 适配（OpenAI / 通义 / Claude / 本地）
- 路由策略（按复杂度、按成本、按可用性）
- 主备模型与降级
- Token 统计与限流

### Day 5（4h）：作品③启动：企业 Agent 平台骨架
- 基于 Spring AI 搭建 Java 后端
- 模型网关模块
- Tool Registry + MCP Registry
- Prompt 管理
- 基础 Trace

### Day 6（4h）：架构图与文档
- 完整 AI 中台架构图
- 组件职责说明
- 技术选型决策文档
- 与作品①②的集成关系图

### Day 7（4h）：整合 + Java 强化
- 作品③骨架跑通
- AI 中台架构图定稿
- **Java 强化**：Spring AI 全模块串讲

**本周产出：**
1. AI 中台完整架构图 + 决策文档
2. 模型网关实现（多模型路由、降级、限流）
3. **作品③骨架**：企业 Agent 平台最小版

---

## Week 11：Java 强化 + Spring AI 实战

**本周目标**：把 Java 从"恢复"提升到"能面试"水平，Spring AI 实战整合，推进作品③核心模块落地。

### Day 1（4h）：Java 八股复习
- Java 集合（HashMap / ConcurrentHashMap 原理）
- Java 并发（线程池、锁、CAS、AQS）
- JVM（内存模型、GC、调优）

### Day 2（4h）：Spring Boot + 中间件
- Spring Boot 核心机制
- Redis 缓存与分布式锁
- MySQL 索引与优化
- Kafka 消息队列

### Day 3（4h）：Spring AI 深入
- ChatClient + Advisor 高级用法
- ToolCallback + Structured Output
- Chat Memory（持久化对话）
- VectorStore 抽象（接 pgvector / ES）

### Day 4（4h）：Spring AI MCP 集成
- Spring AI 作为 MCP Client
- Spring AI 实现 MCP Server
- Observability 集成
- 把 W2 的 Python MCP Server 接入 Spring AI

### Day 5（4h）：作品③核心模块落地
- 模型网关（Spring AI 实现）
- Tool Registry + MCP Registry
- Prompt 管理 + 版本控制
- Trace + Token 成本看板

### Day 6（4h）：作品③权限与审计
- Spring Security 集成
- RBAC 权限模型
- 审计日志
- 多租户隔离

### Day 7（4h）：整合 + 面试准备
- 作品③核心模块跑通
- Java 面试题刷题（重点：并发、JVM、Spring）
- Spring AI 面试题整理

**本周产出：**
1. Java 八股复习笔记 + 面试题集
2. Spring AI 深度实战 demo
3. **作品③核心模块落地**

---

## Week 12：面试冲刺 + 作品打磨

**本周目标**：三个作品全部打磨完成，GitHub 整理，模拟面试，投第三批简历（含架构师/技术负责人岗）。

### Day 1（4h）：作品①最终打磨
- README 终版（架构图、流程图、评测结果、难点、优化点）
- 代码清理、注释完善
- 录制演示视频（3-5 分钟）
- Demo 在线部署（HuggingFace Space / Streamlit Cloud）

### Day 2（4h）：作品②最终打磨
- README 终版（多 Agent 架构图、Trace 看板截图、评测结果）
- 理赔流程演示视频
- 在线 Demo 部署

### Day 3（4h）：作品③最终打磨
- README 终版（AI 中台架构图、模块说明）
- 架构决策文档
- 部署文档

### Day 4（4h）：GitHub 整体整理
- 个人 GitHub 主页优化（README、置顶三个作品）
- 建立 Portfolio 仓库（导航到三个作品）
- 技术博客发布（1-2 篇深度文章）

### Day 5（4h）：简历终版 + 投第三批
- 简历加入三个作品
- 更新面试故事（含架构经验）
- **★ 投第三批简历**（含技术负责人/架构师岗）

### Day 6（4h）：模拟面试
- RAG 方向 10 题模拟（自答 + 复盘）
- Agent 方向 10 题模拟
- 系统设计题模拟（设计一个医疗 AI 平台）
- Java 八股模拟

### Day 7（4h）：查漏补缺 + 反问准备
- 回顾所有面试反馈，补最后缺口
- 准备反问清单（团队、技术栈、数据基础、AI 落地阶段）
- 面试当天 checklist

**本周产出：**
1. 三个作品最终版（GitHub + 在线 Demo + 演示视频）
2. 简历终版 + 面试故事全集
3. **投第三批简历**（含架构师/技术负责人岗）
4. 模拟面试复盘记录

> **★ W12 末里程碑：三个作品完成 + 简历三批投完 + 具备面试高级岗位的能力**

---

## 投简历节点与策略

### 三个投简历节点

| 节点 | 时间 | 已有作品 | 主投方向 |
|---|:---:|---|---|
| **第一批** | W4 末 | 作品①（保险知识库） | RAG 工程师、知识库、医疗 AI、保险科技 |
| **第二批** | W8 末 | 作品①②（含 Agent） | Agent 工程师、多 Agent 系统、AI 应用 |
| **第三批** | W12 末 | 作品①②③（含平台） | AI 解决方案架构师、技术负责人、平台岗 |

### 主投公司方向

| 类别 | 代表公司 |
|---|---|
| 医疗 AI | 医联、丁香园 AI、平安好医生 AI、零氪科技、推想科技、深透医疗 |
| 保险科技 | 众安科技、信美相互 AI、水滴 AI、暖哇科技、保险极客 |
| 健康险/寿险 AI 中台 | 平安、泰康、太平洋 AI 团队 |
| 医疗大数据 | 医渡云、零氪、中电数据 |
| AI 创业公司（通用） | 智谱、月之暗面、MiniMax、百川（应用岗非算法） |

---

## 风险与应对

**风险1：时间不足**
> 应对：每周日做一次进度检查，如果落后超过 2 天，压缩选做项（作品③、A2A 深入、Milvus）。

**风险2：面试早于学习完成**
> 应对：W4 末投第一批简历后，可能立即收到面试。遇到答不上的问题，诚实说"这部分还在学，原理是…，预计 X 周后能上手"，回去立刻补。

**风险3：双语言（Python + Java）负担过重**
> 应对：Python 为主线（LangGraph），Java 为辅线（Spring AI，每周日恢复）。如果 Java 进度跟不上，作品③可以简化为"架构图 + 核心模块 demo"，不用完整实现。

**风险4：医疗数据获取困难**
> 应对：使用公开保险条款 PDF（重疾险/医疗险）、公开医疗文档、合成数据。不使用真实患者数据。

---

## 给评审的提问清单

请评审重点评估以下几个问题：

1. **时间分配合理性**：每天 4 小时、12 周 336 小时，按当前模块分配是否合理？有没有哪个模块被低估或高估？
2. **技术栈选型**：Python（LangGraph）+ Java（Spring AI）双语言并行，对在职学习者是否过于激进？是否应该聚焦单语言？
3. **作品顺序与深度**：作品①（RAG）→ 作品②（Agent）→ 作品③（平台）的顺序是否合理？深度是否足够支撑面试？
4. **投简历节点**：W4/W8/W12 三个节点是否过早或过晚？W4 末只有作品①是否够投？
5. **模块覆盖完整性**：相比你之前给出的 10 大模块体系，本方案是否有遗漏？特别是 Agent Runtime、A2A、模型网关的深度是否足够？
6. **医疗行业结合度**：三个作品都用医疗/保险场景，是否过于聚焦反而限制了通用性？
7. **Java 恢复节奏**：每周日上午恢复 Java，从 W5 开始引入 Spring AI，节奏是否合理？
8. **风险应对**：四个风险点（时间、面试早于学习、双语言、数据）的应对策略是否可行？

---

*文档版本：v2.0 · 2026-07-21*
*用户画像：36 岁 / Java+大数据 / 医疗保险行业 / 每天可投入 4 小时 / 目标中小厂+医疗 AI*
