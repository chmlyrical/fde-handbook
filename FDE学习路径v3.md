# FDE 面试学习路径 v3

> 12周系统化方案 · 基于 ChatGPT 专业评估大改版 · 吸收四大补充 + 三大压缩 + 求职主线调整
> 用户画像：36 岁 / Java+大数据 / 医疗保险行业 / 每天可投入 4 小时 / 目标 AI 解决方案架构师 / AI FDE

---

## 一、v2 → v3 关键调整说明

ChatGPT 对 v2 的评分：方向 8.5/10、技术覆盖 7.5/10、可执行性 6.5/10、求职匹配 7/10、作品 8/10、高级岗支撑力 6.5/10。

核心结论：方向正确，但工程底座有遗漏，需要大改。

### 四大必须补充（提升高级岗支撑力）

| # | 模块 | 核心内容 | 投入 |
|---|---|---|---|
| ① | **Agent Harness 与 Loop Engine** | Agent Loop Engine / Context Engine / Tool Runtime / State 与 Checkpoint Engine / Policy Engine / Artifact 与 Workspace / Trace 与 Evaluation Hooks | W5-W7 核心，12-16h |
| ② | **生产级持久任务编排** | Temporal 或同类 Durable Workflow Engine；幂等、重试与退避、补偿机制、任务超时、取消、消息队列、异步回调、事件驱动、长任务恢复、多实例并发、状态一致性 | W7 |
| ③ | **LLMOps 与 AI 软件生命周期** | Prompt/模型/Embedding/Reranker/知识库/评测集版本管理；离线回归、上线前评测门禁、灰度、A/B 测试、在线反馈采集、错误回流、回滚、成本告警、效果漂移监控 | W8 |
| ④ | **模型部署、性能、容量和 SRE** | vLLM、Continuous Batching、KV Cache、TTFT、TPOT、Tokens/s、GPU 显存估算、模型副本数、扩缩容、监控；要能回答"32B 模型需要多少 GPU、支持多少并发、怎么测算延迟和成本" | W10 |

### 三大必须压缩（避免过度设计）

| # | 模块 | v2 | v3 调整 |
|---|---|---|---|
| ① | 多 Agent | W7 整周 | 压缩到 8 小时 |
| ② | A2A 协议 | W7 整周 | 压缩到 4 小时概念+Demo |
| ③ | 多向量库并行 | pgvector + ES 双索引 | ES 为主方案，pgvector 作对比实验 |

### 求职主线调整

| 主线 | v2 | v3 |
|---|---|---|
| **第一主线** | AI 应用技术负责人 | **AI 解决方案架构师、AI FDE、企业 GenAI 架构师**（发挥行业+数据+架构优势） |
| **第二主线** | — | AI 应用技术负责人（传统企业AI部门、医疗保险科技公司） |
| **保底** | — | 数据与AI架构师、大数据与AI平台架构师 |
| **暂时减少** | — | 纯 Agent 开发、强算法、模型训练、推理引擎 |

### 其他关键调整

- **模型网关提前**：W1 建立轻量 Model Adapter 概念，W10 升级为平台化 AI Gateway
- **知识库选型**：ES 为主方案（适合医疗编码/药品名），pgvector 作对比实验，避免双写一致性问题
- **Text2SQL 移后**：从 W2 移到 W8 后作为独立选修（涉及语义层、Schema Linking 等复杂内容）
- **评测四层测试体系**：工具单元测试 / 流程集成测试 / 模型离线评测 / 线上业务指标
- **安全补充**：OWASP LLM Top 10、MCP 安全（Tool Poisoning、工具描述注入、供应链风险）
- **Java 小时数重算**：每周日 4h × 12 周 = 48h，W11 重点投入，实际约 60h
- **作品表述调整**：从"生产版/完整版"改为"企业级可演示原型 / Interview Grade Prototype"
- **区分两种模式**：Agents as Tools（主 Agent 保留控制权）vs Handoff（控制权转移）

---

## 二、用户画像与方案前提

| 维度 | 内容 |
|---|---|
| 年龄 | 36 岁 |
| 技术背景 | Java + 大数据（Hadoop / 数仓 / 数据建模 / SQL），做过架构相关（非大模型架构） |
| 行业背景 | 医疗 / 保险 / 健康 相关（核心差异化优势） |
| 大模型水平 | 会调 API、用过 ChatGPT 类工具；RAG / Agent / 本地部署均未做过 |
| 求职方向 | 第一主线 AI 解决方案架构师/AI FDE/企业 GenAI 架构师；第二主线 AI 应用技术负责人；保底 数据与AI架构师 |
| 出差偏好 | 基本不想出差（主投远程/本地交付的 AI 应用岗） |
| 时间预算 | **每天 4 小时，每周 28 小时**（在职但时间稳定） |
| 12 周总时长 | 336 小时 |

---

## 三、v3 新12周主线（采用 ChatGPT 建议）

| 周 | 核心内容 | 作品进度 | 投简历 |
|:---:|---|---|:---:|
| W1 | 大模型基础、模型调用、结构化输出、模型选型、**轻量模型网关** | — | |
| W2 | Tool Calling、MCP、工具权限与参数校验 | — | |
| W3 | 文档解析、数据摄取、Chunk、Embedding、基础 RAG | 作品① 启动 | |
| W4 | 混合检索、Rerank、ACL、RAG 评测、作品一 | 作品① 完成 | **★ 第一批** |
| W5 | **Agent Loop、ReAct、Context Engine、Tool Runtime** | 作品② 启动 | |
| W6 | LangGraph、Checkpoint、HITL、中断恢复 | 作品② 推进 | |
| W7 | **Agent Harness、幂等、重试、长任务与业务 Workflow（Temporal）** | 作品② 推进 | |
| W8 | 评测、Trace、OpenTelemetry、**LLMOps**、作品二 | 作品② 完成 | **★ 第二批** |
| W9 | Agent 安全、红队测试、权限、审计和数据治理 | — | |
| W10 | **模型网关、推理服务、性能、容量和成本** | 作品③ 启动 | |
| W11 | Spring AI、企业系统集成、平台最小版本 | 作品③ 推进 | |
| W12 | 作品打磨、系统设计、Java 高频知识和模拟面试 | 作品③ 完成 | **★ 第三批** |

---

## 四、技术栈与框架选型

### 主框架组合

| 定位 | 框架 | 语言 | 用途 |
|---|---|---|---|
| **主攻** | LangGraph | Python | Agent 状态机编排、长任务、HITL、中断恢复 |
| **企业集成** | Spring AI | Java | 企业 Java 系统接入 AI、Tool Calling、RAG、MCP |
| **辅修** | OpenAI Agents SDK | Python | 快速理解标准 Agent Runtime 抽象 |
| **业务编排** | Temporal | Go/Python | 跨小时跨天的持久业务工作流（理赔长流程） |

### 向量数据库（v3 调整为单主方案）

| 阶段 | 选型 | 理由 |
|---|---|---|
| **主方案** | Elasticsearch | 同时承担 BM25、向量检索、Metadata Filter、混合检索；适合药品名、ICD 编码、保单号精确匹配 |
| **对比实验** | pgvector | 上手快、可练权限与向量统一管理，作为对比实验 |

> **v2 问题**：同时用 pgvector 和 ES 会引入双写一致性、数据更新同步、删除同步、版本同步、权限字段同步、故障排查复杂度。v3 选一个主方案。

### 模型网关演进

| 阶段 | 内容 | 周 |
|---|---|:---:|
| W1 轻量版 | Model Adapter：多模型统一接口、Provider 适配、基础路由 | W1 |
| W10 平台版 | AI Gateway：路由、失败重试、Fallback、限流、熔断、缓存、Token 预算、虚拟 API Key、用户和部门配额、成本归集、模型访问审计 | W10 |

参考：LiteLLM 已把统一接口、路由、重试、Fallback、虚拟密钥和成本统计作为核心能力。

---

## 五、三大作品定位（v3 调整）

| 作品 | 主题 | 技术栈 | 制作周 | 定位 |
|---|---|---|:---:|---|
| **作品①** | 企业保险知识库（RAG） | Python + LangChain + ES（主） + pgvector（对比） + BGE + bge-reranker | W3-4 | 企业级可演示原型 |
| **作品②** | 特药理赔 Agent | Python + LangGraph + Agent Harness + Temporal（业务编排） + OCR/RAG/Rule Tool + HITL | W5-8 | 企业级可演示原型 |
| **作品③** | 企业 Agent 平台最小版 | Java + Spring AI + 模型网关 + Tool Registry + MCP + Trace | W10-12 | 核心模块 Demo + 架构图 |

> **v3 表述调整**：从"生产版/完整版"改为"企业级可演示原型 / Interview Grade Prototype"，更务实。
> **作品③简化**：只实现 模型网关 + Prompt Registry + Tool Registry + MCP Registry + Trace + Token Cost。Knowledge Base、Evaluation Center、IAM、Application Marketplace 先做架构设计和接口定义。

---

## Week 1：大模型基础、模型调用、结构化输出、模型选型、轻量模型网关

**本周目标**：建立大模型完整基础知识体系，能独立调用主流模型 API，理解 token、上下文窗口、采样参数，**建立轻量模型网关概念**。

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

### Day 4（4h）：API 调用实战 + 轻量模型网关
- OpenAI / 通义 / 智谱 / DeepSeek API 调用
- 流式输出（SSE）处理
- JSON 结构化输出与校验
- Token 统计与成本计算
- **轻量 Model Adapter 实现**：多模型统一接口、Provider 适配、基础路由、失败重试

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
- **Java 恢复**：Java 集合、并发基础复习
- Spring Boot 项目骨架搭建

**本周产出：**
1. 轻量模型网关 v1（多模型统一接口、Provider 适配、基础路由、失败重试）
2. Prompt 模板管理系统（含测试集概念）
3. 模型选型决策文档（博客/笔记）
4. GitHub 仓库初始化 + Java 项目骨架

---

## Week 2：Tool Calling、MCP、工具权限与参数校验

**本周目标**：掌握 Function Calling 完整工程实践，理解 MCP 协议，能独立实现一个 MCP Server 和 MCP Client，建立工具权限与参数校验机制。

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

### Day 3（4h）：实战工具开发②：数据库查询 Tool + 规则引擎
- DB Query Tool：参数化 SQL 执行，只读权限限制
- 规则引擎 Tool：基于 Drools 或简单规则脚本
- 工具权限与白名单设计
- 参数校验与沙箱执行

### Day 4（4h）：MCP 协议学习
- MCP Host / Client / Server 三方关系
- Tools / Resources / Prompts 三类能力
- Transport（stdio / SSE / HTTP）
- Tool Discovery、能力协商、进度跟踪、取消
- 权限与认证机制
- MCP 安全风险（Tool Poisoning、工具描述注入）

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

> **注**：Text2SQL 已从 W2 移到 W8 后作为独立选修（涉及语义层、Schema Linking、SQL 生成、只读权限、危险语句拦截、执行计划、结果校验和评测，内容复杂）。

---

## Week 3：文档解析、数据摄取、Chunk、Embedding、基础 RAG

**本周目标**：掌握 Naive RAG 完整流程，能用 ES 跑通一个文档问答 demo，启动作品①保险知识库。

### Day 1（4h）：RAG 概念与流程
- Naive RAG 完整流程：摄取→解析→切分→Embedding→检索→生成
- RAG 四阶段：Naive → Advanced → Modular → Agentic
- LangChain RAG quickstart 跑通
- Dify 知识库体验（对比低代码方案）

### Day 2（4h）：文档解析与清洗
- PyMuPDF / unstructured 解析 PDF
- Word / PPT / Excel 解析
- OCR 与版面分析（PaddleOCR）
- 文本清洗：去噪、去重、规范化
- 表格结构识别（选学，不深入 Table Transformer）

### Day 3（4h）：Chunk 切分策略
- 固定长度切分
- 按段落/标题切分
- 滑动窗口
- Parent-Child Chunk
- Sentence Window
- 语义切分（基于 Embedding 相似度）

### Day 4（4h）：Embedding 与 ES 向量检索
- Embedding 模型选型：BGE / GTE / OpenAI ada
- Elasticsearch 安装与向量检索配置
- BM25 关键词检索
- Metadata 存储与过滤
- pgvector 对比实验（作为参考，不作为主方案）

### Day 5（4h）：检索与生成
- 向量检索（Top-K）
- BM25 关键词检索
- 引用溯源实现
- 无答案拒答策略
- Prompt 模板：问答 + 引用 + 拒答

### Day 6（4h）：作品①启动：保险知识库基础版
- 数据：5-10 份公开保险条款 PDF（重疾险/医疗险）
- 架构：摄取 pipeline + ES（BM25+向量） + LangChain + 通义 API
- 功能：上传 PDF → chunk → embedding → 检索 → 问答 + 引用
- 放 GitHub，写 README

### Day 7（4h）：整合 + Java 恢复
- 作品①基础版跑通，README 完善
- 画 RAG 流程图
- **Java 恢复**：Spring AI 入门（ChatClient、Model Adapter）

**本周产出：**
1. RAG 基础 demo（ES + LangChain）
2. **作品①基础版**：保险知识库最小可用版（GitHub）
3. RAG 流程图笔记
4. Spring AI 入门 demo

---

## Week 4：混合检索、Rerank、ACL、RAG 评测、作品一（投简历节点）

**本周目标**：把 Naive RAG 升级到 Advanced RAG，引入混合检索、rerank、权限过滤、RAG 评测。作品①完成企业级可演示原型，第4周末投第一批简历。

### Day 1（4h）：ES 混合检索深化
- 向量检索 + BM25 融合（RRF 算法）
- 医疗场景适配：药品名、ICD 编码、保单号精确匹配
- Metadata 过滤
- 多路召回融合

### Day 2（4h）：Rerank 与 Query 改写
- bge-reranker 模型使用
- Query Rewrite（HyDE、Query 扩展、Multi-Query）
- Query Decomposition（问题分解）
- Context Compression

### Day 3（4h）：权限控制与文档治理
- chunk 级权限标签（部门/角色）
- 检索时 Metadata filter（用户身份过滤）
- 文档版本管理与失效
- 增量索引策略
- 缓存失效机制

### Day 4（4h）：RAG 评测（第一层+第三层测试）
- RAGAS 四指标：Faithfulness / Answer Relevancy / Context Precision / Context Recall
- 构造评测集（20-30 个医疗问答对）
- 跑 RAGAS 自动评测
- 分析薄弱环节，针对性优化

### Day 5（4h）：作品①企业级完成
- 整合：ES 混合检索 + rerank + 权限 + 评测
- 架构图绘制（draw.io 或 excalidraw）
- README 完整化：架构、难点、优化点、评测结果
- Streamlit/Gradio 前端界面

### Day 6（4h）：简历改造 + 投简历准备
- 简历突出：医疗/保险行业 + 大数据 + Java + RAG 作品
- 三个面试故事定稿（数据治理→RAG、Java架构→工程化、医疗行业→差异化）
- 整理目标公司清单（医疗AI / 保险科技 / 健康险）
- BOSS / 猎聘账号准备

### Day 7（4h）：开始投简历 + Java 恢复
- **★ 投第一批简历**（医疗AI、保险科技公司、AI 解决方案架构师岗）
- **Java 恢复**：Spring AI Tool Calling
- 准备面试常见问题清单

**本周产出：**
1. 企业级 RAG 系统完整版（混合检索 + rerank + 权限 + 评测）
2. **作品①完成**：保险知识库企业级可演示原型（GitHub + README + 架构图 + 评测结果）
3. 改造版简历 + 三个面试故事
4. **投第一批简历**

> **★ W4 末里程碑：作品①完成 + 简历投出 + 具备面试 RAG/解决方案架构师岗位的能力**

---

## Week 5：Agent Loop、ReAct、Context Engine、Tool Runtime

**本周目标**：理解 Agent 的核心抽象，掌握 ReAct、Plan-and-Execute 模式，**深入 Agent Harness 的 Loop Engine、Context Engine、Tool Runtime 三大组件**。启动作品②特药理赔 Agent。

### Day 1（4h）：Agent 核心概念
- Agent vs Workflow vs Agentic Workflow 的区别
- Tool Calling 作为 Agent 的执行原语
- ReAct（Reasoning + Acting）模式
- Plan-and-Execute 模式
- Reflection、Self Correction

### Day 2（4h）：Agent Harness 概念（v3 新增）
- **Agent Harness 完整定义**（不只是测试框架）：
  - Agent Loop Engine：模型调用、工具调用、结果回填、停止条件、最大步骤、执行预算、异常处理
  - Context Engine：组装 System Prompt、用户输入、会话历史、记忆、RAG 结果、工具结果、业务状态；上下文超长时裁剪、摘要、压缩
  - Tool Runtime：工具注册、Schema、参数校验、鉴权、超时、重试、幂等、隔离、沙箱、结果标准化
  - State 与 Checkpoint Engine：状态持久化、断点恢复、状态版本、人工修改、任务重放、失败恢复
  - Policy Engine：模型权限、工具权限、风险判断、人工审批、数据访问范围、执行预算
  - Artifact 与 Workspace：文件、报告、代码、图片、中间结果、业务对象
  - Trace 与 Evaluation Hooks：模型调用、工具调用、状态变化、成本、延迟、错误、评测结果

### Day 3（4h）：Agent Loop Engine 实战
- 实现 Agent Loop：模型调用 → 工具调用 → 结果回填 → 继续执行 → 停止条件
- 最大步骤数、最大 Token 数
- 异常处理与重试
- 执行预算（Token / 调用次数 / 时长）

### Day 4（4h）：Context Engine 实战
- 组装 System Prompt + 用户输入 + 会话历史 + RAG 结果 + 工具结果
- 上下文超长时的裁剪策略
- 摘要与压缩
- 记忆管理（短期/长期）

### Day 5（4h）：Tool Runtime 实战
- 工具注册与 Schema 管理
- 参数校验与鉴权
- 超时、重试、幂等
- 沙箱执行与隔离
- 结果标准化

### Day 6（4h）：作品②启动：特药理赔 Agent 需求设计
- 场景：用户上传理赔材料（医疗单据、处方、保单）
- 流程：OCR → 信息抽取 → 保单匹配 → 规则校验 → 知识库查询 → 人工审批 → 结论
- 设计 Agent 状态结构
- 识别所需工具：OCR、保单查询、规则引擎、知识库检索
- 用 Agent Harness 视角重新设计（Loop/Context/Tool/State/Policy）

### Day 7（4h）：OpenAI Agents SDK 学习 + Java 恢复
- OpenAI Agents SDK：Agent / Runner / Tool / Handoff / Guardrail / Session / Trace
- **区分两种模式**：Agents as Tools（主 Agent 保留控制权）vs Handoff（控制权转移）
- 对比 LangChain Agent 抽象
- **Java 恢复**：Spring AI ChatClient + Advisor

**本周产出：**
1. Agent Loop Engine + Context Engine + Tool Runtime 实现
2. **作品②需求设计文档**（基于 Agent Harness 视角）
3. OpenAI Agents SDK 实践 demo

---

## Week 6：LangGraph、Checkpoint、HITL、中断恢复

**本周目标**：掌握 LangGraph 的核心抽象，能用 LangGraph 实现带中断恢复和 Human in the Loop 的复杂 Agent。

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

## Week 7：Agent Harness、幂等、重试、长任务与业务 Workflow（v3 大改）

**本周目标**：深入 Agent Harness 完整组件，引入 Temporal 等持久业务编排，理解 LangGraph 与 Workflow Engine 的边界。

### Day 1（4h）：Agent Harness State 与 Policy Engine（v3 新增）
- State 与 Checkpoint Engine：状态持久化、断点恢复、状态版本、人工修改、任务重放、失败恢复
- Policy Engine：模型权限、工具权限、风险判断、人工审批、数据访问范围、执行预算
- Artifact 与 Workspace：文件、报告、代码、图片、中间结果、业务对象管理

### Day 2（4h）：生产级持久任务编排概念（v3 新增）
- **LangGraph vs Workflow Engine 边界**：
  - LangGraph 管理 Agent 内部的状态和推理流程
  - Workflow Engine 管理跨小时、跨天、跨系统的业务流程
  - Kafka 或消息队列处理事件传递和系统解耦
  - 数据库保存最终业务状态和审计结果
- **Temporal 核心概念**：持久、可恢复、基于事件历史的重放
- 幂等、重试与退避、补偿机制、任务超时、任务取消
- 消息队列、定时任务、异步回调、事件驱动
- 长任务恢复、多实例并发、状态一致性

### Day 3（4h）：Temporal 实战入门
- Temporal 安装与基础概念
- Workflow 与 Activity
- 实现一个简单的持久工作流
- 对比 LangGraph 的 Checkpoint 机制

### Day 4（4h）：作品②升级：引入业务 Workflow
- 理赔流程的跨系统编排：
  - OCR（W2 工具）→ LangGraph Agent 内部流程
  - 人工审批（跨小时/跨天）→ Temporal Workflow
  - 结果回写 → 数据库 + 审计
- 幂等设计：同一理赔单重复提交只处理一次
- 补偿机制：审批驳回后的回滚

### Day 5（4h）：多 Agent 协作（压缩到 8h，含 Day 5-6）
- Router Agent（中央路由）
- Supervisor Agent（中央调度）
- **Agents as Tools vs Handoff 明确区分**：
  - Agents as Tools：主 Agent 保留控制权，子 Agent 返回结果
  - Handoff：控制权转移给新 Agent，由新 Agent 接管后续对话
- LangGraph Supervisor 模式实现
- 什么时候用多 Agent：多个独立领域、上下文隔离、并行专业任务、跨系统自治协作
- **什么时候不用多 Agent**：简单理赔流程拆成 OCR Agent/Rule Agent/Knowledge Agent/Review Agent 会增加延迟、Token 成本、错误传播、调试难度

### Day 6（4h）：A2A 协议（压缩到 4h）+ 作品②多 Agent（可选）
- A2A vs MCP 的层级差异（MCP 工具接入，A2A Agent 间协作）
- A2A 核心概念：Agent Card、Task、Message
- 能力发现机制
- 跨框架、跨厂商 Agent 通信
- **作品②可选升级**：如果时间允许，把单 Agent 升级为多 Agent（OCR Agent + Knowledge Agent + Rule Agent + Review Agent + Supervisor）

### Day 7（4h）：整合 + Java 恢复
- 作品②业务 Workflow 版本跑通
- Agent Harness 完整组件图绘制
- **Java 恢复**：Spring AI MCP Client（与本周 MCP 呼应）

**本周产出：**
1. Agent Harness 完整实现（7 大组件）
2. Temporal 持久工作流 demo
3. **作品②业务 Workflow 版本**：理赔 Agent + Temporal 业务编排
4. A2A 协议笔记

> **v3 关键变化**：W7 从"多 Agent 与 A2A 整周"改为"Agent Harness + Temporal 业务编排"，多 Agent 压缩到 8h，A2A 压缩到 4h。

---

## Week 8：评测、Trace、OpenTelemetry、LLMOps、作品二（投简历节点）

**本周目标**：掌握 Agent 系统的评测与可观测性建设，**建立完整 LLMOps 闭环**，完成作品②的评测与 Trace 看板，第8周末投第二批简历。

### Day 1（4h）：Trace 与可观测性
- Trace / Span / Prompt 记录
- 模型调用记录、工具调用记录
- Agent 执行轨迹记录（结构化：计划、模型输出、工具调用、参数、状态迁移、决策依据）
- **不依赖模型隐藏推理过程**
- OpenTelemetry 概念
- LangSmith / Langfuse 体验

### Day 2（4h）：Token 与成本看板
- Token 消耗统计（按 Agent / Tool / 用户）
- 成本计算与看板
- 延迟与错误率监控
- 实时告警机制

### Day 3（4h）：四层测试体系（v3 新增）
1. **工具单元测试**：验证 OCR、数据库查询、规则引擎、RAG 工具的输入输出
2. **流程集成测试**：验证状态流转、条件分支、异常重试、人工审批
3. **模型离线评测**：验证场景识别、字段抽取、检索召回、答案正确率、工具选择
4. **线上业务指标**：验证任务完成率、人工接管率、错误率、平均成本、平均时延、业务处理效率

测试 Case 从 20-30 个扩充到 50-100 个，按错误类型分组：
- OCR 错误 / 抽取错误 / 检索错误 / 规则错误 / 工具选择错误 / 参数生成错误 / 幻觉 / 权限错误 / 流程错误

### Day 4（4h）：LLMOps 完整闭环（v3 新增）
- **版本管理**：Prompt / 模型 / Embedding / Reranker / 知识库 / 评测集
- **离线回归测试**：每次变更跑评测集
- **上线前评测门禁**：指标不达标禁止上线
- **灰度发布**：按比例放量
- **A/B 测试**：新旧版本对比
- **在线反馈采集**：用户反馈、人工接管记录
- **错误样本回流**：失败 case 自动入库
- **Prompt 和模型回滚**：快速回退
- **成本异常告警**：Token 消耗异常
- **模型效果漂移监控**：定期跑评测集对比基线
- **完整链路**：需求定义 → 数据集构建 → 原型开发 → 离线评测 → 安全测试 → 灰度发布 → 在线监控 → 错误回流 → 版本优化

### Day 5（4h）：作品②完成：评测 + Trace 看板 + LLMOps
- 构造理赔场景测试集（50+ case，按错误类型分组）
- 跑四层测试体系
- Trace 看板（每个 case 的执行轨迹可视化）
- LLMOps 闭环：版本管理 + 回归测试 + 灰度 + 监控
- README 完整化：架构图、流程图、评测结果、难点

### Day 6（4h）：简历更新 + 投第二批简历
- 简历加入作品②
- 更新面试故事（加入 Agent + Agent Harness + LLMOps 经验）
- 扩展目标公司清单（含 Agent 方向岗位）
- **★ 投第二批简历**（Agent 工程师 / 多 Agent 系统 / AI 解决方案架构师）

### Day 7（4h）：面试复盘 + Java 恢复
- 回顾 W4 投出简历后的面试反馈
- 整理被问倒的问题，针对性补缺
- **Java 恢复**：Spring AI Observability（与本周可观测性呼应）

**本周产出：**
1. Trace 看板 + Token 成本看板
2. 四层测试体系（工具单元 / 流程集成 / 模型离线 / 线上业务）
3. LLMOps 闭环（版本管理 + 回归 + 灰度 + 监控 + 回流）
4. **作品②完成**：特药理赔 Agent 企业级可演示原型（GitHub + 评测 + Trace + LLMOps）
5. **投第二批简历**

> **★ W8 末里程碑：作品①②完成 + 具备面试 Agent/解决方案架构师岗位的能力**

---

## Week 9：Agent 安全、红队测试、权限、审计和数据治理

**本周目标**：掌握 Agent 系统的安全风险与治理机制（v3 补充 OWASP LLM Top 10 和 MCP 安全），能给作品①②加上安全防护层。

### Day 1（4h）：Prompt Injection 防护
- Direct Prompt Injection
- Indirect Prompt Injection（来自文档/工具返回）
- 防御策略：输入过滤、输出校验、指令隔离
- 工具返回内容隔离

### Day 2（4h）：供应链与 MCP 安全（v3 新增）
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

### Day 3（4h）：数据越权与泄露防护
- 知识库越权（检索时过滤不严）
- 敏感信息泄露（PII / 医疗数据）
- 数据脱敏策略
- 租户隔离

### Day 4（4h）：工具滥用防护 + 审批与审计
- 任意 SQL 执行风险、任意代码执行风险
- 最小权限原则、工具白名单
- 参数校验与沙箱执行
- Human Approval 机制（高风险动作审批）
- Agent 身份与权限
- 审计日志设计
- 合规要求（医疗数据 HIPAA / 国内数据安全法）

### Day 5（4h）：执行预算 + 红队测试
- 执行预算（Token / 调用次数 / 时长）
- 最大步骤数、最大 Token 数
- 超限降级策略
- 红队测试：主动攻击自己的 Agent 系统

### Day 6（4h）：作品①②安全加固
- 给保险知识库加权限过滤、脱敏
- 给理赔 Agent 加工具白名单、审批、审计
- 记录安全事件日志
- 红队测试 + 修复

### Day 7（4h）：整合 + Java 恢复
- 安全加固后的作品①②测试
- 安全架构图绘制
- **Java 恢复**：Spring Security（与本周安全主题呼应）

**本周产出：**
1. Agent 安全防护层（Prompt Injection 防护、OWASP LLM Top 10、MCP 安全、权限、审计）
2. 作品①②安全加固版
3. 安全架构图 + 合规分析文档

---

## Week 10：模型网关、推理服务、性能、容量和成本（v3 重构）

**本周目标**：把 W1 的轻量模型网关升级为平台化 AI Gateway，掌握推理服务工程（vLLM 深度），能做容量规划和成本测算。启动作品③。

### Day 1（4h）：平台化 AI Gateway
- 多模型统一接口
- Provider 适配（OpenAI / 通义 / Claude / 本地）
- 路由策略（按复杂度、按成本、按可用性）
- 主备模型与降级
- Token 统计与限流、熔断、缓存
- 虚拟 API Key、用户和部门配额
- 成本归集、模型访问审计
- 参考：LiteLLM

### Day 2（4h）：推理服务工程（v3 新增）
- vLLM 生产部署
- Continuous Batching、KV Cache
- 模型量化（AWQ、GPTQ、INT4/INT8）
- OpenAI Compatible API

### Day 3（4h）：性能与容量评估（v3 新增）
- **关键指标**：TTFT（首 Token 延迟）、TPOT（单 Token 输出时间）、端到端延迟、队列时间、Tokens Per Second
- GPU 显存估算
- 模型副本数
- 服务扩缩容
- 推理服务监控
- **要能回答**：32B 模型需要多少 GPU、支持多少并发、怎么测算延迟和成本

### Day 4（4h）：成本测算实战
- 不同模型的成本对比（按 token 计费 vs 私有部署）
- GPU 成本估算（A100/H100 租用 vs 自建）
- 不同场景的成本优化策略
- 容量规划文档

### Day 5（4h）：作品③启动：企业 Agent 平台骨架
- 基于 Spring AI 搭建 Java 后端
- 模型网关模块（W1 轻量版升级）
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
1. 平台化 AI Gateway（路由、降级、限流、缓存、Token 预算、虚拟 API Key、成本归集、审计）
2. 推理服务工程 demo（vLLM + 性能指标）
3. 成本测算与容量规划文档
4. **作品③骨架**：企业 Agent 平台最小版

---

## Week 11：Spring AI、企业系统集成、平台最小版本

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
- VectorStore 抽象（接 ES）

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
- **简化原则**：Knowledge Base、Evaluation Center、IAM、Application Marketplace 先做架构设计和接口定义，不完整实现

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

## Week 12：作品打磨、系统设计、Java 高频知识和模拟面试（投第三批简历）

**本周目标**：三个作品全部打磨完成，GitHub 整理，模拟面试，投第三批简历（含架构师/技术负责人岗）。

### Day 1（4h）：作品①最终打磨
- README 终版（架构图、流程图、评测结果、难点、优化点）
- 代码清理、注释完善
- 录制演示视频（3-5 分钟）
- Demo 在线部署（HuggingFace Space / Streamlit Cloud）

### Day 2（4h）：作品②最终打磨
- README 终版（Agent Harness 架构图、Temporal 业务编排图、Trace 看板截图、评测结果）
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
- 更新面试故事（含架构 + Agent Harness + LLMOps + 推理服务经验）
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

> **★ W12 末里程碑：三个作品完成 + 简历三批投完 + 具备面试 AI 解决方案架构师/企业 GenAI 架构师的能力**

---

## 投简历节点与策略

### 三个投简历节点

| 节点 | 时间 | 已有作品 | 主投方向 |
|---|:---:|---|---|
| **第一批** | W4 末 | 作品①（保险知识库） | RAG 工程师、知识库、医疗 AI、保险科技、AI 解决方案架构师 |
| **第二批** | W8 末 | 作品①②（含 Agent + Harness + LLMOps） | Agent 工程师、多 Agent 系统、AI 解决方案架构师 |
| **第三批** | W12 末 | 作品①②③（含平台 + 推理服务） | AI 解决方案架构师、企业 GenAI 架构师、技术负责人 |

### 主投公司方向

| 类别 | 代表公司 |
|---|---|
| 医疗 AI | 医联、丁香园 AI、平安好医生 AI、零氪科技、推想科技、深透医疗 |
| 保险科技 | 众安科技、信美相互 AI、水滴 AI、暖哇科技、保险极客 |
| 健康险/寿险 AI 中台 | 平安、泰康、太平洋 AI 团队 |
| 医疗大数据 | 医渡云、零氪、中电数据 |
| 传统企业 AI 部门 | 各行业正在建 AI 团队的企业 |
| AI 创业公司（应用岗） | 智谱、月之暗面、MiniMax、百川（非算法岗） |

### 暂时减少投递

- 纯 Agent 开发工程师（高强度现场编码）
- 强算法工程师
- 模型训练工程师
- 推理引擎工程师
- 要求现场独立完成大量代码的平台核心开发岗位

---

## 风险与应对

**风险1：时间不足**
> 应对：每周日做一次进度检查，如果落后超过 2 天，压缩选做项（作品③简化、A2A 深入、Milvus、Table Transformer）。

**风险2：面试早于学习完成**
> 应对：W4 末投第一批简历后，可能立即收到面试。遇到答不上的问题，诚实说"这部分还在学，原理是…，预计 X 周后能上手"，回去立刻补。

**风险3：双语言（Python + Java）负担过重**
> 应对：Python 为主线（LangGraph + Temporal），Java 为辅线（Spring AI，每周日恢复）。如果 Java 进度跟不上，作品③可以简化为"架构图 + 核心模块 demo"，不用完整实现。

**风险4：医疗数据获取困难**
> 应对：使用公开保险条款 PDF（重疾险/医疗险）、公开医疗文档、合成数据。不使用真实患者数据。

**风险5：Agent Harness + Temporal 学习曲线陡（v3 新增）**
> 应对：W5-W7 是最重的三周，如果 Temporal 学不动，可以只学概念 + 跑通 quickstart，不深入业务编排。作品②的业务编排可以用简化版（LangGraph Checkpoint + 数据库状态机替代）。

**风险6：LLMOps 闭环实现复杂（v3 新增）**
> 应对：W8 的 LLMOps 不需要完整实现，重点理解完整链路 + 实现核心环节（版本管理 + 回归测试 + 监控），其余用架构图 + 文档说明。

---

## v2 vs v3 关键差异总结

| 维度 | v2 | v3 |
|---|---|---|
| 求职主线 | AI 应用技术负责人优先 | **AI 解决方案架构师 / AI FDE 优先** |
| Agent Harness | W7 半天，理解窄（测试框架） | **W5-W7 核心，完整 7 组件** |
| 生产编排 | 无 | **W7 Temporal 业务编排** |
| LLMOps | 仅有评测 | **W8 完整闭环** |
| 推理服务 | Ollama 体验 + vLLM 概念 | **W10 vLLM 深度 + 性能 + 容量 + 成本** |
| 多 Agent | W7 整周 | **压缩到 8h** |
| A2A | W7 整周 | **压缩到 4h** |
| 向量库 | pgvector + ES 双索引 | **ES 为主，pgvector 对比** |
| 模型网关 | W10 才有 | **W1 轻量版，W10 平台版** |
| Text2SQL | W2 | **移到 W8 后选修** |
| 评测 | RAGAS + LLM as Judge | **四层测试体系** |
| 安全 | 基础权限 | **+ OWASP LLM Top 10 + MCP 安全** |
| 作品表述 | "生产版/完整版" | **"企业级可演示原型"** |
| Java 小时 | 写 34h（算错） | **重算约 60h** |

---

*文档版本：v3.0 · 2026-07-21*
*基于 ChatGPT 专业评估大改*
*用户画像：36 岁 / Java+大数据 / 医疗保险行业 / 每天可投入 4 小时 / 目标 AI 解决方案架构师*
