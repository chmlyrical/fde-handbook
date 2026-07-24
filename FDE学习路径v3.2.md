# FDE 面试学习路径 v3.2（最终冻结版）

> 基于 ChatGPT 对 v3.1 的评估微调 · 综合评分 8.7/10 · 可直接执行
> ChatGPT 明确结论："到此冻结学习计划，直接开始 W1"
> 用户画像：36 岁 / Java+大数据 / 医疗保险行业 / 每天可投入 4 小时 / 目标 AI 解决方案架构师

---

## 一、v3.1 → v3.2 微调说明

ChatGPT 对 v3.1 评分：求职定位 9/10、技术覆盖 9/10、差异化 9/10、作品 8.5/10、可执行性 7.5/10、面试转化 8.5/10、综合 8.7/10。

核心结论：v3.1 已具备执行条件，可冻结。剩余 10 项微调，形成 v3.2 最终执行版。

### 十项微调

#### 1. A/B/C 定义修订

**v3.1 问题**：A 级定义为"必须亲自实现并能讲代码"，但理论概念（Transformer、模型选型、Java 八股）无法按"实现代码"衡量。

**v3.2 新定义**：

| 级别 | 定义 | 说明 |
|---|---|---|
| **A** | 面试核心能力，必须掌握；涉及工程实现的内容必须亲自编码 | 理论概念只需掌握不需"实现代码" |
| **B** | 必须跑通最小 Demo，并能解释架构、限制和适用场景 | — |
| **C** | 能解释原理、完成选型并说明取舍，无需完整实现 | — |

#### 2. W3 AI 数据工程缩减

**v3.1 问题**：一天 4 小时实现 12 项数据工程能力不现实。

**v3.2 调整**：亲自实现 6 项，其余用设计/接口/架构图表达。

| 亲自实现 [A] | 只做设计 [C] |
|---|---|
| Document ID、Chunk ID 和文件 Hash | 数据血缘 |
| 摄取任务状态 | 多租户隔离 |
| 增量更新 | 全量重建 |
| 文档和 Embedding 版本字段 | 数据摄取可观测性 |
| 失败重试及死信记录 | 索引构建状态 |
| 原文件、解析结果、索引数据的一致性检查 | 数据质量规则 |

#### 3. W4 评测集扩充

**v3.1 问题**："20-30 个问题覆盖 9 类问题"样本过少。检索指标需标注相关文档或 Chunk 集合。

**v3.2 调整**：
- 20 个经过人工审核的 Golden Cases
- 30 个模型辅助生成、人工抽检的扩展 Cases
- 每个问题标记：预期文档、预期 Chunk、答案要点、是否应拒答
- Recall@K、MRR、nDCG@K 需标注相关文档或 Chunk 集合

#### 4. W8 线上指标调整

**v3.1 问题**：作品阶段没有真实线上流量，"线上业务指标"无法真正实施。

**v3.2 调整**：改为"定义线上指标、埋点和计算口径，通过离线回放或模拟流量验证"。面试时明确区分真实生产指标和原型模拟指标。

#### 5. 模型网关 Build vs Buy ADR（新增）

**v3.1 问题**：W10 实现虚拟 API Key、部门配额、成本归集、缓存、限流、熔断、Fallback 和审计——相当于独立平台产品。

**v3.2 调整**：

| 级别 | 自研内容 | 集成现有 |
|---|---|---|
| **A 级自研** | Model Adapter、Provider 路由、超时与重试、Fallback、Token 和成本记录、Trace Hook、简单限流 | — |
| **B 级集成** | — | 使用 LiteLLM 体验虚拟密钥、预算、项目级成本、多租户、管理界面 |

**新增产出**：AI Gateway Build vs Buy ADR

作品③价值：展示你理解模型网关架构、能扩展企业功能、知道哪些能力应采购/集成，哪些值得自研。

#### 6. MCP 两个细节纠正

**纠正1**：OAuth 授权主要适用于 HTTP Transport，且在 MCP 中属于可选项。stdio 通常通过运行环境获得凭据。

**纠正2**：Tool Annotation 只是提示信息，不能当作鉴权或安全策略。来自未受信任 MCP Server 的 `readOnlyHint`、`destructiveHint`、`idempotentHint` 都不能直接相信。

**W2 新增一句**：Tool Annotation 用于风险提示和审批辅助，最终权限由客户端 Policy Engine、身份认证和工具白名单控制。

**补充**：MCP 远程部署、水平扩展和无状态运行仍在持续演进。作品中固定使用某个稳定协议版本，避免跟随 Draft 实时变化。

#### 7. W9 增加 OWASP Agentic Applications 2026

**v3.2 新增**：W9 同时学习两份 OWASP：
- OWASP LLM Top 10 2025
- **OWASP Top 10 for Agentic Applications 2026**（覆盖自主决策、工具链和长期记忆等问题）

#### 8. 增加多模态 Document AI（B 级，放 W2-W3）

**v3.2 新增**：保险理赔场景不只有纯文本 PDF，还会出现扫描件、发票、处方、病历、表格和图片。

- OCR 置信度
- 页面和坐标溯源
- 跨页字段抽取
- 表格结构恢复
- 图片质量检测
- 低置信度人工复核
- OCR 和字段抽取分别评测

放入 W2 和 W3，不另增一周。

#### 9. Text2SQL 从 C 提升为 B

**v3.2 调整**：A2A 从 4h 压缩到 2h，省出时间给 Text2SQL。

考虑用户的大数据、数仓和 SQL 背景，Text2SQL 是明显优势。

Text2SQL [B] 级内容：
- Schema Linking
- 语义层
- SQL 只读控制
- 表和字段权限
- SQL 语法检查
- 执行计划和超时
- 查询结果校验
- RAG、SQL、API 三路数据路由

#### 10. 三个作品补 NFR 和验收指标表（A 级）

**v3.2 新增**：三个作品都补充统一 NFR（非功能需求）和验收指标表：

| 指标 | 说明 |
|---|---|
| 可用性 | 服务可用率 |
| P95/P99 延迟 | 端到端延迟 |
| 单任务 Token 成本 | 每次任务平均消耗 |
| 工具调用成功率 | 工具执行成功/总调用 |
| 任务完成率 | 成功完成/总任务 |
| 人工接管率 | 人工介入/总任务 |
| 最大步骤数 | Agent 循环上限 |
| 并发量 | 同时处理的任务数 |
| RTO/RPO | 恢复时间/恢复点目标 |
| 错误恢复时间 | 异常后恢复时长 |
| 数据更新时效 | 数据变更到索引生效 |
| 权限隔离准确率 | 权限过滤正确性 |

作品从"功能 Demo"提升为"架构师级可演示原型"。

### 其他补充

- **W1 增加 Prompt/RAG/Fine-tuning/规则引擎选型决策树 [C]**：什么时候优化 Prompt、什么时候用 RAG、什么时候微调、什么时候用规则引擎、什么时候蒸馏或小模型
- **W9 增加 Agent Memory 生命周期 [B]**：Working Memory、Conversation Summary、Episodic Memory、用户级长期记忆、保留时间、删除与更正、敏感信息过滤、Memory Poisoning、租户间记忆隔离
- **Agent Harness 重点调整**：放在企业扩展（工具鉴权、执行预算、Context 裁剪、业务状态映射、错误恢复、审计、评测 Hook），不再独立开发 LangGraph 替代品

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
| 时间预算 | **每天 4 小时，每周 28 小时** |
| 12 周总时长 | 336 小时 |

---

## 三、v3.2 新12周主线（含 A/B/C 标记）

| 周 | 核心内容 | 作品进度 | 投简历 |
|:---:|---|---|:---:|
| W1 | 大模型基础 [A]、模型调用 [A]、结构化输出 [A]、模型选型 [A]、**选型决策树 [C]**、轻量模型网关 [A] | — | |
| W2 | Tool Calling [A]、MCP [B]、工具权限 [A]、**多模态 Document AI [B]** | — | |
| W3 | 文档解析 [A]、**AI 数据工程 6 项实现+6 项设计**、Chunk [A]、Embedding [A]、基础 RAG [A]、知识库选型 ADR [C] | 作品① 启动 | |
| W4 | 混合检索 [A]、Rerank [A]、ACL [A]、RAG 评测 [A]（**50 Cases**）、作品一 | 作品① 完成 | **★ 市场验证** |
| W5 | Agent Loop [A]、ReAct [A]、Context Engine [A]、Tool Runtime [A]、Agent 框架选型 ADR [C] | 作品② 启动 | |
| W6 | LangGraph [A]、Checkpoint [A]、HITL [A]、中断恢复 [A] | 作品② 推进 | |
| W7 | Agent Harness 企业扩展 [B]、幂等/重试 [B]、Temporal [B-可选]、业务 Workflow 边界 [C]、**Text2SQL [B]** | 作品② 推进 | |
| W8 | 评测四层 [A]（**线上指标改离线模拟**）、Trace [A]、OpenTelemetry [B]、LLMOps 必须实现 [A] + 只做设计 [C]、作品二 | 作品② 完成 | **★ 第二批** |
| W9 | Agent 安全 [A]、红队测试 [B]、**OWASP LLM Top 10 + Agentic 2026** [C]、权限审计 [A]、**Agent Memory 生命周期 [B]** | — | |
| W10 | **模型网关平台版 [A] + Build vs Buy ADR [C]**、推理服务 [B]、性能容量 [B]、部署交付工程 [B] | 作品③ 启动 | |
| W11 | Spring AI [A]、企业系统集成 [A]、平台最小版本 [A] | 作品③ 推进 | |
| W12 | 作品打磨、**NFR 验收指标表**、系统设计、Java 高频知识、模拟面试 | 作品③ 完成 | **★ 第三批** |

---

## 四、A/B/C 三级分类速查表（v3.2 修订定义）

| 级别 | 定义 | 内容清单 |
|---|---|---|
| **[A] 面试核心能力，必须掌握；涉及工程实现必须亲自编码** | 理论概念只需掌握不需"实现代码"；工程实现必须亲自编码 | RAG 全流程、AI 数据工程（6 项）、Tool Calling、LangGraph、Agent Loop、Context Engine、Tool Runtime、Trace、基础模型网关、平台化 AI Gateway（自研部分）、Spring AI 核心集成、四层测试体系、LLMOps 必须实现部分、安全加固、Java 八股、**NFR 验收指标** |
| **[B] 跑通最小 Demo，能解释架构、限制和适用场景** | 跑通官方 Demo + 能讲架构图和选型理由 | MCP Server/Client、**多模态 Document AI**、Temporal（可选）、vLLM 部署、OpenAI Agents SDK、Spring AI MCP、OpenTelemetry、LangSmith/Langfuse、Agent Harness 企业扩展、Agent Memory 生命周期、红队测试、部署交付工程、**Text2SQL**、LiteLLM 集成体验 |
| **[C] 能解释原理、完成选型并说明取舍，无需完整实现** | 读文档 + 能讲清概念和选型维度 | A2A（2h）、多 Agent 深入、完整 LLMOps（灰度/A/B/回滚/漂移）、模型集群扩缩容、OWASP LLM Top 10 + Agentic 2026、知识库选型 ADR、Agent 框架选型 ADR、AI Gateway Build vs Buy ADR、业务 Workflow 边界、**Prompt/RAG/Fine-tuning/规则引擎选型决策树**、AI 数据工程（6 项设计部分） |

---

## 五、三大作品定位（含 NFR）

### 作品①：企业保险知识库（RAG + AI 数据工程）

| 维度 | 内容 |
|---|---|
| 技术栈 | Python + LangChain + ES（主） + BGE + bge-reranker + AI 数据工程 |
| 制作周 | W3-4 |
| 亮点 | 增量摄取、数据血缘（设计）、权限过滤、混合检索、评测闭环 |
| **NFR 指标** | P95 检索延迟、Recall@K、数据更新时效、权限隔离准确率 |

### 作品②：特药理赔 Agent

| 维度 | 内容 |
|---|---|
| 技术栈 | Python + LangGraph + Agent Harness 企业扩展 + Temporal（可选 Java SDK） + OCR/RAG/Rule Tool + HITL |
| 制作周 | W5-8 |
| 亮点 | Agent Harness 参考模型、LLMOps 闭环、业务编排 |
| **NFR 指标** | 任务完成率、人工接管率、单任务 Token 成本、工具调用成功率、最大步骤数、错误恢复时间 |

### 作品③：企业 Agent 平台最小版

| 维度 | 内容 |
|---|---|
| 技术栈 | Java + Spring AI + 模型网关（自研核心 + LiteLLM 集成） + Tool Registry + MCP + Trace |
| 制作周 | W10-12 |
| 亮点 | AI Gateway Build vs Buy、部署交付拓扑、企业扩展点 |
| **NFR 指标** | 并发量、RTO/RPO、可用性、API 限流、容器资源限制 |
| 简化原则 | Knowledge Base、Evaluation Center、IAM、Application Marketplace 先做架构设计和接口定义 |

---

## Week 1：大模型基础、模型调用、结构化输出、模型选型、选型决策树、轻量模型网关

**本周目标**：建立大模型完整基础知识体系，能独立调用主流模型 API，建立轻量模型网关概念，**产出选型决策树**。

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

### Day 3（4h）：模型选型与权衡 [A] + 选型决策树 [C]
- 开源 vs 闭源 vs 本地部署的选择维度 [A]
- 准确率、延迟、吞吐、成本、上下文长度的权衡 [A]
- SFT、LoRA、量化、蒸馏的概念（不深入算法）[A]
- 模型幻觉成因与缓解策略 [A]
- 主流模型对比：GPT-4o / Claude / Qwen / DeepSeek / Llama [A]
- **Prompt/RAG/Fine-tuning/规则引擎选型决策树 [C]**：
  - 什么时候优化 Prompt
  - 什么时候使用 RAG
  - 什么时候进行微调
  - 什么时候使用规则引擎
  - 什么时候需要蒸馏或小模型
  - 产出：选型决策树文档

### Day 4（4h）：API 调用实战 + 轻量模型网关 [A]
- OpenAI / 通义 / 智谱 / DeepSeek API 调用
- 流式输出（SSE）处理
- JSON 结构化输出与校验
- Token 统计与成本计算
- **轻量 Model Adapter 实现**：多模型统一接口、Provider 适配、基础路由、失败重试、Trace Hook

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
- 写博客/笔记：模型选型决策框架 + 选型决策树
- **Java 恢复**：Java 集合、并发基础复习
- Spring Boot 项目骨架搭建

**本周产出：**
1. 轻量模型网关 v1（Model Adapter + Provider 路由 + 失败重试 + Token 记录 + Trace Hook）[A]
2. Prompt 模板管理系统 [A]
3. 模型选型决策文档 + **Prompt/RAG/Fine-tuning/规则引擎选型决策树** [A] + [C]
4. GitHub 仓库初始化 + Java 项目骨架

---

## Week 2：Tool Calling、MCP、工具权限、多模态 Document AI

**本周目标**：掌握 Function Calling 完整工程实践，理解 MCP 协议（含两个纠正），能独立实现 MCP Server/Client，**新增多模态 Document AI**。

### Day 1（4h）：Function Calling 深入 [A]
- OpenAI / Claude / Qwen Function Calling 规范对比
- 工具描述（Tool Schema）写法
- 参数校验与错误处理
- 多工具并行调用
- 多轮工具调用循环

### Day 2（4h）：实战工具开发①：OCR Tool + 多模态 Document AI [A] + [B]
- 基于 PaddleOCR 或云 OCR 实现医疗单据 OCR [A]
- 包装成 Function Calling 兼容的 Tool [A]
- 错误处理与降级策略 [A]
- **多模态 Document AI [B]**：
  - OCR 置信度
  - 页面和坐标溯源
  - 跨页字段抽取
  - 表格结构恢复
  - 图片质量检测
  - 低置信度人工复核
  - OCR 和字段抽取分别评测

### Day 3（4h）：实战工具开发②：数据库查询 Tool + 规则引擎 [A]
- DB Query Tool：参数化 SQL 执行，只读权限限制
- 规则引擎 Tool：基于 Drools 或简单规则脚本
- 工具权限与白名单设计
- 参数校验与沙箱执行

### Day 4（4h）：MCP 协议学习 [B]
- MCP Host / Client / Server 三方关系
- Tools / Resources / Prompts 三类能力
- **Transport：stdio / Streamable HTTP**（Streamable HTTP 可通过 SSE 承载服务端流式消息；旧 HTTP+SSE 已 2025-03 废弃）
- Tool Discovery、能力协商、进度跟踪、取消
- **MCP Session 生命周期**
- **OAuth 2.1 授权**：主要适用于 HTTP Transport，且在 MCP 中属于可选项；stdio 通常通过运行环境获得凭据
- **Tool Annotation**：只是提示信息，不能当作鉴权或安全策略；来自未受信任 MCP Server 的 readOnlyHint/destructiveHint/idempotentHint 都不能直接相信
- **Tool Annotation 用于风险提示和审批辅助，最终权限由客户端 Policy Engine、身份认证和工具白名单控制**
- **MCP 远程部署、水平扩展、无状态运行仍在演进；作品中固定使用某个稳定协议版本**
- 服务端能力协商
- MCP Server 的来源信任和准入
- MCP 安全风险（Tool Poisoning、工具描述注入）

### Day 5（4h）：实现一个 MCP Server [B]
- 基于 Python MCP SDK 实现（固定使用某个稳定协议版本）
- 暴露医疗相关 Tools：OCR、保单查询、规则校验
- 实现 Resources：保单模板、规则文档
- 实现 Prompts：理赔审核 Prompt 模板
- 实现 Tool Annotation（只读/破坏性/幂等）

### Day 6（4h）：实现一个 MCP Client + 工具权限 [B]
- MCP Client 调用自研 Server
- 工具白名单机制
- 参数校验与沙箱执行
- 错误处理、超时、重试
- 对比 MCP vs 直接 Function Calling 的差异

### Day 7（4h）：整合 + Java 恢复
- 整理 Tool + MCP + 多模态代码到 GitHub
- 画一张 Function Calling → MCP 演进图
- **Java 恢复**：Spring Boot 复习（REST API + 依赖注入 + 配置管理）

**本周产出：**
1. OCR Tool + DB Query Tool + Rule Engine Tool [A]
2. **多模态 Document AI 模块**（OCR 置信度、坐标溯源、跨页抽取、表格恢复、人工复核）[B]
3. 一个可运行的 MCP Server（医疗场景，固定协议版本）[B]
4. 一个 MCP Client + 工具权限白名单（含 Tool Annotation 不可信原则）[B]
5. Spring Boot 项目骨架完善

> **注**：Text2SQL 从 W2 移到 W7 [B]（涉及语义层、Schema Linking 等复杂内容）。

---

## Week 3：文档解析、AI 数据工程（6 实现+6 设计）、Chunk、Embedding、基础 RAG、知识库选型 ADR

**本周目标**：掌握 Naive RAG 完整流程，**AI 数据工程 6 项亲自实现 + 6 项设计**，用 ES 跑通文档问答 demo，启动作品①。

### Day 1（4h）：RAG 概念与流程 [A] + [B]
- Naive RAG 完整流程 [A]
- RAG 四阶段：Naive → Advanced → Modular → Agentic [A]
- LangChain RAG quickstart 跑通 [A]
- Dify 知识库体验（对比低代码方案）[B]

### Day 2（4h）：文档解析与清洗 [A]
- PyMuPDF / unstructured 解析 PDF
- Word / PPT / Excel 解析
- OCR 与版面分析（PaddleOCR）
- 文本清洗：去噪、去重、规范化

### Day 3（4h）：AI 数据工程（6 项实现 + 6 项设计）[A] + [C]
**亲自实现 [A]：**
1. Document ID、Chunk ID 和文件 Hash
2. 摄取任务状态
3. 增量更新
4. 文档和 Embedding 版本字段
5. 失败重试及死信记录
6. 原文件、解析结果、索引数据的一致性检查

**只做设计 [C]：**
- 数据血缘
- 多租户隔离
- 全量重建
- 数据摄取可观测性
- 索引构建状态
- 数据质量规则

### Day 4（4h）：Chunk 切分策略 + Embedding + 知识库选型 ADR [A] + [C]
- Chunk 策略：固定长度、按段落/标题、滑动窗口、Parent-Child、Sentence Window、语义切分 [A]
- Embedding 模型选型：BGE / GTE / OpenAI ada [A]
- Elasticsearch 安装与向量检索配置 [A]
- **知识库平台选型 ADR [C]**：
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
- 架构：摄取 pipeline（含 AI 数据工程 6 项实现） + ES（BM25+向量） + LangChain + 通义 API
- 功能：上传 PDF → chunk → embedding → 检索 → 问答 + 引用
- 放 GitHub，写 README

### Day 7（4h）：整合 + Java 恢复
- 作品①基础版跑通，README 完善
- 画 RAG 流程图（含数据工程环节）
- **Java 恢复**：Spring AI 入门（ChatClient、Model Adapter）

**本周产出：**
1. RAG 基础 demo（ES + LangChain + AI 数据工程 6 项实现）[A]
2. **知识库平台选型 ADR 文档** [C]
3. **作品①基础版**：保险知识库最小可用版（GitHub）[A]
4. Spring AI 入门 demo [B]

---

## Week 4：混合检索、Rerank、ACL、RAG 评测（50 Cases）、作品一（市场验证节点）

**本周目标**：把 Naive RAG 升级到 Advanced RAG，**评测集扩充到 50 个 Cases**，作品①完成企业级可演示原型，第4周末投第一批简历（定位：市场验证）。

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

### Day 4（4h）：RAG 评测（确定性检索指标 + RAGAS + 50 Cases）[A]
- **确定性检索指标**：Hit Rate@K、Recall@K、Precision@K、MRR、nDCG@K
- RAGAS 四指标：Faithfulness / Answer Relevancy / Context Precision / Context Recall
- **结合人工黄金答案、检索指标、引用准确性**
- **测试集 9 类问题**：普通、关键词精确、语义改写、跨条款、无答案、权限受限、易混淆条款、恶意注入、版本失效
- **评测集扩充（v3.2）**：
  - 20 个经过人工审核的 Golden Cases
  - 30 个模型辅助生成、人工抽检的扩展 Cases
  - **每个问题标记**：预期文档、预期 Chunk、答案要点、是否应拒答
  - Recall@K、MRR、nDCG@K 需标注相关文档或 Chunk 集合
- 跑评测，分析薄弱环节，针对性优化

### Day 5（4h）：作品①企业级完成 [A]
- 整合：ES 混合检索 + rerank + 权限 + AI 数据工程（6 实现+6 设计）+ 评测（50 Cases）
- 架构图绘制
- README 完整化：架构、难点、优化点、评测结果、数据工程亮点
- Streamlit/Gradio 前端界面

### Day 6（4h）：简历改造 + 投简历准备
- 简历突出：医疗/保险行业 + 大数据 + AI 数据工程 + Java + RAG 作品
- 三个面试故事定稿（数据治理→RAG、Java架构→工程化、医疗行业→差异化）
- 整理目标公司清单（医疗AI / 保险科技 / 健康险）

### Day 7（4h）：开始投简历（市场验证）+ Java 恢复
- **★ 投第一批简历（定位：市场验证、测试简历定位、获取真实面试问题）**
- 主投：行业高度匹配的 AI 解决方案岗位、RAG/知识库应用岗位、医疗保险 AI 应用岗、部分 AI 项目负责人岗位
- **Java 恢复**：Spring AI Tool Calling
- 准备面试常见问题清单

**本周产出：**
1. 企业级 RAG 系统完整版（混合检索 + rerank + 权限 + AI 数据工程 + 50 Cases 评测）[A]
2. **作品①完成**：保险知识库企业级可演示原型 [A]
3. 改造版简历 + 三个面试故事
4. **投第一批简历（市场验证）**

> **面试话术**：遇到答不上的问题，不说"预计 X 周后能上手"，改为"该部分完成了原理研究和原型验证，尚未经历生产规模验证。我可以说明设计方案、当前边界和需要进一步验证的指标"。

---

## Week 5：Agent Loop、ReAct、Context Engine、Tool Runtime、Agent 框架选型 ADR

**本周目标**：理解 Agent 核心抽象，掌握 ReAct 模式，深入 Agent Harness 的 Loop Engine、Context Engine、Tool Runtime，**产出 Agent 框架选型 ADR**。

### Day 1（4h）：Agent 核心概念 [A]
- Agent vs Workflow vs Agentic Workflow 的区别
- Tool Calling 作为 Agent 的执行原语
- ReAct（Reasoning + Acting）模式
- Plan-and-Execute 模式
- Reflection、Self Correction

### Day 2（4h）：Agent Harness 参考能力模型 [B]
- **明确表述**：这是参考能力模型，非行业标准
- **七大能力域**（根据企业 Agent 运行需求拆分）：
  - Agent Loop Engine、Context Engine、Tool Runtime
  - State 与 Checkpoint Engine、Policy Engine
  - Artifact 与 Workspace、Trace 与 Evaluation Hooks
- **不重复开发框架已提供的能力**：OpenAI Agents SDK 已含 Loop/工具/Handoff/Guardrails/Tracing；LangGraph 本身就是面向长期运行、有状态 Agent 的低层编排框架和 Runtime
- **Harness 重点放在企业扩展**：工具鉴权、执行预算、Context 裁剪、业务状态映射、错误恢复、审计、评测 Hook
- 无需独立开发 LangGraph 替代品

### Day 3（4h）：Agent Loop Engine 实战 [A]
- 实现 Agent Loop：模型调用 → 工具调用 → 结果回填 → 继续执行 → 停止条件
- 最大步骤数、最大 Token 数、执行预算
- 异常处理与重试

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
- **Agent 框架选型 ADR [C]**：
  - 比较 LangGraph / Spring AI / OpenAI Agents SDK
  - 维度：编排模型、状态管理、HITL、工具体系、MCP、多 Agent、可观测性、部署方式、技术语言、锁定风险、团队适配
  - **明确区分**：Agents as Tools（主 Agent 保留控制权）vs Handoff（控制权转移）
  - 产出：选型决策文档

### Day 6（4h）：作品②启动：特药理赔 Agent 需求设计 [A]
- 场景：用户上传理赔材料（医疗单据、处方、保单）
- 流程：OCR → 信息抽取 → 保单匹配 → 规则校验 → 知识库查询 → 人工审批 → 结论
- 设计 Agent 状态结构
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

**本周目标**：掌握 LangGraph 核心抽象，能用 LangGraph 实现带中断恢复和 Human in the Loop 的复杂 Agent。

### Day 1（4h）：LangGraph 核心概念 [A]
- State、Node、Edge、Conditional Edge
- Checkpointer（状态持久化）
- Interrupt（中断机制）
- Command（恢复机制）
- Subgraph（子图）
- **LangGraph 已有 Durable Execution、Interrupt、HITL、失败恢复**（避免与 Temporal 重复建设）

### Day 2-4（12h）：LangGraph 实战（状态图 + Checkpoint + HITL）[A]
- 状态图 + 条件分支 + Tool Node 集成
- PostgreSQL Checkpointer 持久化 + Interrupt + Command 恢复
- Human in the Loop：审批插入、通过/驳回分支、超时降级、人工修改状态

### Day 5-6（8h）：作品②推进：理赔 Agent 核心流程 + 人工审批 [A]
- 用 LangGraph 搭建理赔 Agent 骨架
- 集成 W2 的工具：OCR、保单查询、规则引擎
- 集成 W4 的 RAG 知识库
- 实现状态图：OCR → 抽取 → 校验 → 检索 → 审批 → 结论
- 理赔金额 > 5000 元触发人工审批
- 审批中断 → 状态持久化 → 异步恢复
- Trace 记录全程

### Day 7（4h）：整合 + Java 恢复
- 作品②核心流程跑通
- LangGraph 流程图绘制
- **Java 恢复**：Spring AI RAG Advisor

**本周产出：**
1. LangGraph 实战 demo（含 Checkpoint、Interrupt、HITL）[A]
2. **作品②核心流程跑通**：理赔 Agent 状态机版本 [A]
3. Spring AI RAG Advisor 集成 demo [B]

---

## Week 7：Agent Harness 企业扩展、幂等、重试、业务 Workflow 边界、Text2SQL

**本周目标**：深入 Agent Harness 企业扩展，**Temporal 可选**，**新增 Text2SQL [B]**（利用用户大数据优势）。

### Day 1（4h）：Agent Harness 企业扩展 [B]
- State 与 Checkpoint Engine：状态持久化、断点恢复、状态版本、人工修改、任务重放、失败恢复
- Policy Engine：模型权限、工具权限、风险判断、人工审批、数据访问范围、执行预算
- Artifact 与 Workspace：文件、报告、代码、图片、中间结果、业务对象管理
- **重点**：企业扩展（工具鉴权、执行预算、Context 裁剪、业务状态映射、错误恢复、审计、评测 Hook），通过接口设计和架构图表达

### Day 2（4h）：生产级持久任务编排概念 + Temporal（可选）[C] + [B-可选]
- **LangGraph vs Workflow Engine 边界** [C]：
  - LangGraph 管理 Agent 内部状态
  - Workflow Engine 管理跨小时、跨天、跨系统的业务流程
  - Kafka 处理事件传递和系统解耦
  - 数据库保存最终业务状态和审计结果
- **状态归属**：Agent 消息→LangGraph；理赔单→MySQL；跨系统流程→Temporal（可选）；事件通知→Kafka
- 幂等、重试与退避、补偿机制 [C]
- **Temporal 入门（可选，Java SDK）[B-可选]**：
  - Temporal 用 Java SDK（与用户技术背景匹配）
  - Workflow 与 Activity
  - 实现一个简单的持久工作流
  - **如果不学 Temporal**：用 LangGraph + PostgreSQL Checkpointer + 业务数据库 + 消息队列模拟也能完成作品②

### Day 3（4h）：作品②业务编排设计 [A] + [C]
- 理赔流程的跨系统编排设计：
  - OCR → LangGraph Agent 内部流程
  - 人工审批（跨小时/跨天）→ LangGraph Interrupt 或 Temporal Workflow（可选）
  - 结果回写 → 数据库 + 审计
- 幂等设计：同一理赔单重复提交只处理一次
- 补偿机制：审批驳回后的回滚
- **状态归属图绘制**

### Day 4-5（8h）：Text2SQL [B]（v3.2 新增，利用用户大数据优势）
考虑用户的大数据、数仓和 SQL 背景，Text2SQL 是明显优势。
- Schema Linking
- 语义层
- SQL 只读控制
- 表和字段权限
- SQL 语法检查
- 执行计划和超时
- 查询结果校验
- **RAG、SQL、API 三路数据路由**
- 跑通一个 Text2SQL Demo（基于公开数据集或自建 schema）

### Day 6（4h）：多 Agent 协作（压缩）+ A2A（压缩到 2h）[B] + [C]
- Router Agent、Supervisor Agent [B]
- **Agents as Tools vs Handoff 明确区分** [B]
- LangGraph Supervisor 模式实现 [B]
- 什么时候用/不用多 Agent [C]
- **A2A 协议（v3.2 压缩到 2h）[C]**：
  - A2A vs MCP 的层级差异
  - A2A 核心概念：Agent Card、Task、Message
  - 能力发现机制
- **作品②可选升级**：多 Agent（OCR Agent + Knowledge Agent + Rule Agent + Review Agent + Supervisor）[B-可选]

### Day 7（4h）：整合 + Java 恢复
- 作品②业务 Workflow 版本跑通
- Agent Harness 企业扩展图绘制
- **Java 恢复**：Spring AI MCP Client

**本周产出：**
1. Agent Harness 企业扩展实现 [B]
2. Temporal 持久工作流 demo（可选）[B-可选]
3. **Text2SQL Demo** [B]
4. **作品②业务 Workflow 版本** [A]
5. A2A 协议笔记（2h）[C]

---

## Week 8：评测四层（线上指标改离线模拟）、Trace、OpenTelemetry、LLMOps（必须实现+只做设计）、作品二

**本周目标**：掌握评测与可观测性建设，**线上指标改为离线模拟**，**LLMOps 拆成必须实现 + 只做设计**，完成作品②，第8周末投第二批简历。

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

### Day 3（4h）：四层测试体系（v3.2 线上指标调整）[A]
1. **工具单元测试**：验证 OCR、数据库查询、规则引擎、RAG 工具的输入输出
2. **流程集成测试**：验证状态流转、条件分支、异常重试、人工审批
3. **模型离线评测**：验证场景识别、字段抽取、检索召回、答案正确率、工具选择
4. **线上业务指标（v3.2 调整）**：**定义线上指标、埋点和计算口径，通过离线回放或模拟流量验证**（作品阶段没有真实线上流量）

测试 Case 从 20-30 个扩充到 50-100 个，按错误类型分组：
- OCR 错误 / 抽取错误 / 检索错误 / 规则错误 / 工具选择错误 / 参数生成错误 / 幻觉 / 权限错误 / 流程错误

**面试时明确区分**：真实生产指标 vs 原型模拟指标。

### Day 4（4h）：LLMOps 必须实现 [A]
- **Prompt 版本号**
- **模型与 Embedding 配置版本**
- **评测集版本**
- **一键离线回归脚本**
- **Trace**
- **Token 成本统计**
- **错误 Case 保存**

### Day 5（4h）：LLMOps 只做设计 [C] + 完整链路文档
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
- **★ 投第二批简历**（Agent 工程师 / 多 Agent 系统 / AI 解决方案架构师）

### Day 7（4h）：面试复盘 + Java 恢复
- 回顾 W4 投出简历后的面试反馈
- 整理被问倒的问题，针对性补缺
- **Java 恢复**：Spring AI Observability

**本周产出：**
1. Trace 看板 + Token 成本看板 [A]
2. 四层测试体系（线上指标改离线模拟）[A]
3. **LLMOps 必须实现** [A] + **只做设计** [C]
4. **作品②完成**：特药理赔 Agent 企业级可演示原型 [A]
5. **投第二批简历**

> **面试表达**：原型实现了版本、回归、Trace、成本和错误样本闭环；灰度、A/B 和漂移监控完成了架构设计及接口定义。线上指标通过离线回放和模拟流量验证，尚未经历真实生产规模验证。

---

## Week 9：Agent 安全、红队测试、OWASP（LLM + Agentic）、Agent Memory 生命周期

**本周目标**：掌握 Agent 安全与治理（**新增 OWASP Agentic Applications 2026**），**新增 Agent Memory 生命周期**，能给作品①②加上安全防护层。

### Day 1（4h）：Prompt Injection 防护 [A]
- Direct Prompt Injection
- Indirect Prompt Injection（来自文档/工具返回）
- 防御策略：输入过滤、输出校验、指令隔离
- 工具返回内容隔离

### Day 2（4h）：供应链与 MCP 安全 + OWASP [C]
- 第三方模型供应链风险
- MCP Server 信任管理
- Tool Poisoning、工具描述注入
- 恶意文档注入、RAG 数据投毒
- Secret 和 API Key 管理
- **OWASP LLM Top 10 2025**：敏感信息泄露、供应链、数据与模型投毒、不安全输出处理、Excessive Agency
- **OWASP Top 10 for Agentic Applications 2026（v3.2 新增）**：覆盖自主决策、工具链和长期记忆等问题

### Day 3（4h）：数据越权与泄露防护 + Agent Memory 生命周期 [A] + [B]
- 知识库越权 [A]
- 敏感信息泄露（PII / 医疗数据）[A]
- 数据脱敏策略 [A]
- 租户隔离 [A]
- **Agent Memory 生命周期（v3.2 新增）[B]**：
  - Working Memory
  - Conversation Summary
  - Episodic Memory
  - 用户级长期记忆
  - 记忆保留时间
  - 删除与更正
  - 敏感信息过滤
  - **Memory Poisoning**
  - 不同租户之间的记忆隔离

### Day 4（4h）：工具滥用防护 + 审批与审计 [A]
- 任意 SQL/代码执行风险
- 最小权限原则、工具白名单
- 参数校验与沙箱执行
- Human Approval 机制
- Agent 身份与权限
- 审计日志设计
- 合规要求（医疗数据 HIPAA / 国内数据安全法）

### Day 5（4h）：执行预算 + 红队测试 [A] + [B]
- 执行预算（Token / 调用次数 / 时长）[A]
- 最大步骤数、最大 Token 数 [A]
- 超限降级策略 [A]
- 红队测试：主动攻击自己的 Agent 系统 [B]

### Day 6（4h）：作品①②安全加固 [A]
- 给保险知识库加权限过滤、脱敏、Memory Poisoning 防护
- 给理赔 Agent 加工具白名单、审批、审计
- 记录安全事件日志
- 红队测试 + 修复

### Day 7（4h）：整合 + Java 恢复
- 安全加固后的作品①②测试
- 安全架构图绘制
- **Java 恢复**：Spring Security

**本周产出：**
1. Agent 安全防护层（Prompt Injection、OWASP LLM + Agentic、MCP 安全、权限、审计）[A] + [C]
2. **Agent Memory 生命周期设计** [B]
3. 作品①②安全加固版 [A]
4. 安全架构图 + 合规分析文档 [A]

---

## Week 10：模型网关（自研+集成）、推理服务、性能、容量、部署交付工程

**本周目标**：把 W1 轻量网关升级为平台版（**自研核心 + LiteLLM 集成 + Build vs Buy ADR**），掌握推理服务工程，**新增部署交付工程**。启动作品③。

### Day 1（4h）：平台化 AI Gateway（自研核心）[A]
- 多模型统一接口
- Provider 适配（OpenAI / 通义 / Claude / 本地）
- 路由策略（按复杂度、按成本、按可用性）
- 主备模型与降级、Fallback
- Token 统计与简单限流
- Trace Hook

### Day 2（4h）：LiteLLM 集成体验 + Build vs Buy ADR [B] + [C]
- **使用 LiteLLM 体验**：虚拟密钥、预算、项目级成本、多租户、管理界面 [B]
- **AI Gateway Build vs Buy ADR（v3.2 新增）[C]**：
  - 自研 vs 集成 LiteLLM 的决策分析
  - 哪些能力应自研（Adapter/路由/重试/Fallback/Trace）
  - 哪些能力应集成（虚拟密钥/预算/多租户/管理界面）
  - 产出：选型决策文档

### Day 3（4h）：推理服务工程 [B]
- vLLM 生产部署
- Continuous Batching、KV Cache
- 模型量化（AWQ、GPTQ、INT4/INT8）
- OpenAI Compatible API

### Day 4（4h）：性能与容量评估（v3.2 表述调整）[B]
- **关键指标**：TTFT、TPOT、端到端延迟、队列时间、Tokens Per Second
- **v3.2 目标**：跑通 vLLM 服务，完成一次条件明确的 Benchmark，输出容量规划和优化建议
- 需要先明确：权重精度/量化格式、最大上下文长度、输入输出 Token 分布、并发请求数量、KV Cache 占用、Tensor Parallel 数量、GPU 型号和显存、TTFT/TPOT 服务目标、是否允许请求排队
- **没有 GPU 时**：小模型实测 + 32B 公式估算和方案设计，不伪造运行数据
- vLLM 官方 Benchmark 已提供 TTFT、TPOT、ITL 和端到端时延指标，可直接用于性能测试报告

### Day 5（4h）：部署交付工程（v3.2 新增）[B]
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
- 模型网关模块（W1 轻量版升级为平台版，自研核心 + LiteLLM 集成）
- Tool Registry + MCP Registry
- Prompt 管理
- 基础 Trace

### Day 7（4h）：架构图与文档 + Java 强化
- 完整 AI 中台架构图
- 组件职责说明
- 技术选型决策文档（含 AI Gateway Build vs Buy ADR）
- 与作品①②的集成关系图
- **Java 强化**：Spring AI 全模块串讲

**本周产出：**
1. 平台化 AI Gateway（自研核心 + LiteLLM 集成）[A] + [B]
2. **AI Gateway Build vs Buy ADR** [C]
3. 推理服务工程 demo（vLLM + Benchmark）[B]
4. **容量规划和优化建议文档** [B]
5. **部署交付工程文档**（生产部署拓扑 + 扩容容灾方案）[B]
6. **作品③骨架** [A]

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
- 模型网关（Spring AI 实现，自研核心 + LiteLLM 集成）
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

## Week 12：作品打磨、NFR 验收指标表、系统设计、Java 高频知识、模拟面试

**本周目标**：三个作品全部打磨完成，**补充统一 NFR 和验收指标表**，GitHub 整理，模拟面试，投第三批简历。

### Day 1（4h）：作品①最终打磨 + NFR 指标表
- README 终版（架构图、流程图、评测结果、难点、优化点、AI 数据工程亮点）
- **NFR 验收指标表（v3.2 新增）**：P95 检索延迟、Recall@K、数据更新时效、权限隔离准确率
- 代码清理、注释完善
- 录制演示视频（3-5 分钟）
- Demo 在线部署

### Day 2（4h）：作品②最终打磨 + NFR 指标表
- README 终版（Agent Harness 架构图、业务编排图、Trace 看板截图、评测结果、LLMOps 边界）
- **NFR 验收指标表**：任务完成率、人工接管率、单任务 Token 成本、工具调用成功率、最大步骤数、错误恢复时间
- 理赔流程演示视频
- 在线 Demo 部署

### Day 3（4h）：作品③最终打磨 + NFR 指标表
- README 终版（AI 中台架构图、模块说明、部署交付拓扑、AI Gateway Build vs Buy ADR）
- **NFR 验收指标表**：并发量、RTO/RPO、可用性、API 限流、容器资源限制
- 架构决策文档
- 部署文档

### Day 4（4h）：GitHub 整体整理
- 个人 GitHub 主页优化（README、置顶三个作品）
- 建立 Portfolio 仓库（导航到三个作品）
- 技术博客发布（1-2 篇深度文章）

### Day 5（4h）：简历终版 + 投第三批
- 简历加入三个作品 + **NFR 指标**
- 更新面试故事（含架构 + Agent Harness + LLMOps + 推理服务 + 部署交付 + NFR 经验）
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
1. 三个作品最终版（GitHub + 在线 Demo + 演示视频 + **NFR 验收指标表**）
2. 简历终版 + 面试故事全集
3. **投第三批简历**
4. 模拟面试复盘记录

---

## 三个作品统一 NFR 和验收指标表（v3.2 新增）

### 作品①：保险知识库

| 指标 | 目标值 | 测量方法 |
|---|---|---|
| P95 检索延迟 | < 500ms | 压测 |
| Recall@5 | > 80% | 评测集 |
| 数据更新时效 | < 5 分钟 | 增量索引测试 |
| 权限隔离准确率 | 100% | 权限测试用例 |
| 可用性 | 99% | 本地部署监控 |

### 作品②：特药理赔 Agent

| 指标 | 目标值 | 测量方法 |
|---|---|---|
| 任务完成率 | > 85% | 50+ case 评测 |
| 人工接管率 | 15-30% | 评测统计 |
| 单任务 Token 成本 | < ¥0.5 | Token 看板 |
| 工具调用成功率 | > 95% | Trace 统计 |
| 最大步骤数 | 10 | 配置 |
| 错误恢复时间 | < 30s | 异常注入测试 |

### 作品③：企业 Agent 平台

| 指标 | 目标值 | 测量方法 |
|---|---|---|
| 并发量 | 50 QPS | 压测 |
| RTO | < 5 分钟 | 故障恢复测试 |
| RPO | < 1 分钟 | 数据库配置 |
| 可用性 | 99% | 部署监控 |
| API 限流 | 100 req/min/user | 网关配置 |

---

## 投简历节点与策略

### 三个投简历节点

| 节点 | 时间 | 已有作品 | 定位 | 主投方向 |
|---|:---:|---|---|---|
| **第一批** | W4 末 | 作品① | **市场验证、测试简历定位、获取真实面试问题** | 行业高度匹配的 AI 解决方案岗位、RAG/知识库应用岗位、医疗保险 AI 应用岗 |
| **第二批** | W8 末 | 作品①② | 正式投递 | Agent 工程师、多 Agent 系统、AI 解决方案架构师、FDE |
| **第三批** | W12 末 | 作品①②③ + NFR | 集中投递高级岗 | AI 解决方案架构师、企业 GenAI 架构师、技术负责人 |

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
> 应对：每周日做进度检查，如果落后超过 2 天，压缩 [C] 级内容（A2A、多 Agent、完整 LLMOps、模型集群扩缩容、Temporal 深入）。

**风险2：面试早于学习完成**
> 应对：W4 末投第一批简历（定位市场验证），可能立即收到面试。遇到答不上的问题，**不说"预计 X 周后能上手"**，改为"该部分完成了原理研究和原型验证，尚未经历生产规模验证。我可以说明设计方案、当前边界和需要进一步验证的指标"。

**风险3：双语言（Python + Java）负担过重**
> 应对：Python 为主线（LangGraph），Java 为辅线（Spring AI，每周日恢复）。如果 Java 进度跟不上，作品③可以简化为"架构图 + 核心模块 demo"。

**风险4：医疗数据获取困难**
> 应对：使用公开保险条款 PDF、公开医疗文档、合成数据。不使用真实患者数据。

**风险5：Agent Harness 学习曲线陡**
> 应对：Agent Harness 是参考能力模型，非行业标准。重点实现 Loop/Context/Tool Runtime/Trace 四个核心组件 [A]，其余通过接口设计和架构图表达 [B]。不重复开发框架已提供的能力。

**风险6：Temporal 学习曲线陡（可选）**
> 应对：**Temporal 改为可选**。作品②第一版只用 LangGraph + PostgreSQL Checkpointer + 业务数据库 + 消息队列模拟。Temporal 作为独立对比 Demo，用 Java SDK。

**风险7：LLMOps 实现复杂（拆分）**
> 应对：LLMOps 拆成"必须实现"（版本管理 + 回归 + Trace + 成本 + 错误回流）[A] +"只做设计"（灰度/A/B/回滚/漂移/审批）[C]。面试时如实表达边界。

**风险8：vLLM 没有 GPU 实测条件**
> 应对：小模型实测 + 32B 公式估算和方案设计，不伪造运行数据。vLLM 官方 Benchmark 可直接用于性能测试报告。

---

## 执行检查清单（每周检查三件事）

ChatGPT 最终建议：执行过程中每周只检查三件事——

1. **是否有可运行代码**
2. **是否有可量化测试结果**
3. **是否能在面试中讲清楚选型、边界和失败案例**

只要这三项持续形成，竞争力会比单纯记忆 Agent 框架和大模型概念提升得更快。

---

## 面试叙事线

> 从企业数据和知识库建设出发，完成 RAG、Agent、Harness、评测、安全和平台化，再通过 Java 与现有企业系统集成，最终具备医疗保险 AI 方案设计和落地能力。

**三个面试故事升级版：**

1. **数据治理 → RAG + AI 数据工程**："我做了 X 年数仓，知道企业数据质量是 AI 落地最大的坑。我做的 RAG 不是简单扔文档进向量库，而是有增量摄取、数据血缘、权限过滤、混合检索和评测闭环的保险知识库——这是我跟纯应用工程师的差别。"

2. **Java 架构 → AI 工程化 + 平台化**："我做过 Java 后端架构，知道怎么把新能力稳定接进现有系统。大模型 demo 好做，但上生产要处理模型网关（自研核心+集成 LiteLLM）、工具注册、Trace、成本管控、部署交付——这些都是工程问题，不是算法问题。"

3. **医疗行业 → 差异化定位**："我有医疗/保险行业经验，懂业务流程、合规要求、数据特点。医疗 AI 恰恰是最需要懂业务+懂数据+懂合规的赛道，纯 AI 工程师玩不转，这是我的主场。"

---

## v3.1 vs v3.2 关键差异总结

| 维度 | v3.1 | v3.2 |
|---|---|---|
| A/B/C 定义 | "必须亲自实现并能讲代码" | **"面试核心能力必须掌握；涉及工程实现必须亲自编码"**（理论概念不需"实现代码"） |
| W3 AI 数据工程 | 12 项全实现 | **6 项实现 + 6 项设计** |
| W4 评测集 | 20-30 个 | **50 个（20 Golden + 30 扩展），标注预期文档/Chunk/答案要点** |
| W8 线上指标 | "线上业务指标" | **"定义线上指标、埋点和计算口径，通过离线回放或模拟流量验证"** |
| 模型网关 | 全部自研 | **自研核心 [A] + LiteLLM 集成 [B] + Build vs Buy ADR [C]** |
| MCP | 已纠正 Transport | **+ OAuth Transport 边界 + Tool Annotation 不可信原则 + 固定协议版本** |
| W9 OWASP | LLM Top 10 | **LLM Top 10 + Agentic Applications 2026** |
| 多模态 | 无 | **新增 Document AI [B]（OCR 置信度/坐标溯源/跨页抽取/表格恢复/人工复核）** |
| Text2SQL | C 级选修 | **B 级（A2A 压缩到 2h 省出时间）** |
| Agent Memory | 提及 | **完整生命周期 [B]（Working/Summary/Episodic/Memory Poisoning/租户隔离）** |
| 选型决策树 | 无 | **Prompt/RAG/Fine-tuning/规则引擎选型决策树 [C]** |
| 作品 NFR | 无 | **三个作品统一 NFR 和验收指标表 [A]** |

---

*文档版本：v3.2 最终冻结版 · 2026-07-21*
*基于 ChatGPT 对 v3.1 的评估微调（综合评分 8.7/10）*
*ChatGPT 明确结论："到此冻结学习计划，直接开始 W1"*
*用户画像：36 岁 / Java+大数据 / 医疗保险行业 / 每天可投入 4 小时 / 目标 AI 解决方案架构师*
