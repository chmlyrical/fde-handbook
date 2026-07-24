# FDE 学习路径 v3.3 · B站学习资源清单

> 对应 v3.3 的 12 周计划，按周/天整理 B 站及配套资源
> 更新时间：2026-07-21

---

## Week 1：大模型基础、模型调用、模型选型、轻量模型网关

### 核心课程（必看）

**李宏毅《生成式 AI 导论 2025》完整版（42 集）**
- B 站链接：https://www.bilibili.com/video/BV1mXpuz7E9v/
- 对应 Day 1-2：Transformer、Token、上下文窗口、KV Cache
- 重点集数：
  - 第 1 讲：生成式 AI 是什么
  - 第 10 讲：今日的语言模型如何做文字接龙（浅谈 Transformer）
  - 第 11 讲：大型语言模型在"想"什么（可解释性）
  - 第 23-24 讲：Transformer 延伸（上下文窗口）
  - 第 12 讲：评估大型语言模型能力

**李宏毅《机器学习 2025》（含 Agent 内容）**
- B 站链接：https://www.bilibili.com/video/BV1aiADewEBC/
- 对应 Day 1：大模型原理宏观
- 重点集数：
  - 第一讲：一堂课搞懂生成式 AI 的技术突破与未来发展
  - 第二讲：一堂课搞懂 AI Agent 的原理（行为调整、工具使用、做计划）
  - 第三讲：AI 的脑科学——语言模型内部运作机制剖析
  - 第四讲：Transformer 的时代要结束了吗？
  - 第七讲：DeepSeek-R1 这类模型如何进行"深度思考"（Reasoning）
  - 第九讲：谈谈有关大型语言模型评估的几件事

### 配套资源

- Day 4 API 调用实战：参考 OpenAI / 通义 / 智谱官方文档
- Day 6 Ollama 本地部署：B 站搜 "Ollama 教程" 有大量实操视频

---

## Week 2：Tool Calling、MCP、工具权限、多模态 Document AI

### Function Calling（Day 1）

**B 站搜 "Function Calling 大模型"**
- 阿里云百炼 Function Calling 官方文档：https://help.aliyun.com/zh/model-studio/qwen-function-calling
- 包含完整的工具调用流程示例代码（Python/Node.js）

### Spring AI Tools（Java 党推荐，配合 W11）

**《Spring AI 快速入门到实战全套教程》（含 Tools + MCP + Agent）**
- B 站链接：https://www.bilibili.com/video/BV1GfyGBqEm6/
- 重点集数：
  - 第 36 集：Tools（function-call）实现票务助手退票服务
  - 第 37 集：Tools 原理详解
  - 第 43 集：Tools 权限控制
  - 第 49-56 集：MCP 全套（3 种传输方式、client、server、STDIO、SSE、源码讲解）
  - 第 57-77 集：RAG 全套（向量、检索增强、rerank、评估）

### MCP 协议（Day 4-6）

**官方规范（必读）**
- 协议版本固定使用：2025-11-25
- Transport 规范：https://modelcontextprotocol.io/specification/2025-11-25/basic/transports
- 授权规范：https://modelcontextprotocol.io/specification/2025-06-18/basic/authorization
- Schema（含 Tool Annotation）：https://modelcontextprotocol.io/specification/2025-06-18/schema

### 多模态 Document AI / OCR（Day 2）

**B 站搜 "PaddleOCR 教程" 或 "医疗单据 OCR"**
- PaddleOCR 官方文档：https://paddlepaddle.github.io/paddleocr/
- 实战可参考 CSDN 上的 OCR + 字段抽取教程

---

## Week 3：文档解析、AI 数据工程、Chunk、Embedding、基础 RAG、知识库选型 ADR

### LangChain RAG（Day 1, 5）

**《LangGraph 构建智能 AI 应用：从零开始的完整教程》**
- B 站链接：https://www.bilibili.com/video/BV1zKx4zWEZP/
- 涵盖：节点、边、状态、条件边、Tool Node、多智能体协作、ReAct、MCP（STDIO + HTTP）

**LangChain 官方 RAG quickstart**
- 文档：https://python.langchain.com/docs/tutorials/rag/

### 文档解析（Day 2）

- PyMuPDF：https://pymupdf.readthedocs.io/
- unstructured：https://unstructured-io.github.io/unstructured/

### 向量检索与 ES（Day 4）

**B 站搜 "Elasticsearch 向量检索" 或 "ES 8.x 教程"**
- Elasticsearch 官方向量检索文档：https://www.elastic.co/guide/en/elasticsearch/reference/current/knn-search.html

### 知识库平台对比（Day 4 ADR）

- Dify 官方文档：https://docs.dify.ai/
- RAGFlow GitHub：https://github.com/infiniflow/ragflow

---

## Week 4：混合检索、Rerank、ACL、RAG 评测、作品一

### Rerank（Day 2）

- bge-reranker 模型：https://huggingface.co/BAAI/bge-reranker-large
- B 站搜 "RAG rerank 重排序" 有实战视频

### RAGAS 评测（Day 4）

**B 站视频：《RAG 评估资料》**
- B 站链接：https://www.bilibili.com/video/BV1aZ421W7DB/
- 涵盖：LangSmith 评估、RAGAS 评估、Embedding/Reranker 评估

**《2025 年最全最细的大模型 RAG 教程》（含 RAGAS）**
- B 站链接：https://www.bilibili.com/video/BV1D37GzLEe5/

**RAGAS 官方资源**
- GitHub：https://github.com/explodinggradients/ragas
- 文档：https://docs.ragas.io
- 中文教程：https://zhuanlan.zhihu.com/p/1892529470419736435

### Spring AI RAG（Java 党，配合周日恢复）

**《Spring AI 快速入门到实战》第 57-77 集**
- B 站链接：https://www.bilibili.com/video/BV1GfyGBqEm6/
- 第 57-77 集涵盖：RAG 介绍、向量、向量数据库、ChatClient 检索增强、文档读取器、分隔器、metadata 过滤、rerank、RAG 实战、RAG 幻觉评估

---

## Week 5：Agent Loop、ReAct、Context Engine、Tool Runtime、Agent 框架选型 ADR

### Agent 核心概念（Day 1）

**李宏毅《机器学习 2025》第二讲：AI Agent 原理**
- B 站链接：https://www.bilibili.com/video/BV1aiADewEBC/
- 一堂课搞懂 Agent 的行为调整、工具使用和做计划

### OpenAI Agents SDK（Day 7）

**OpenAI Agents SDK 中文实战教程**
- 教程：https://www.duoke360.com/post/45952
- GitHub 示例：https://github.com/NanGePlus/OpenAIAgentsSDKTest
- B 站视频：https://www.bilibili.com/video/BV1BvduYKE75/（大模型 LLM 服务接口调用）

**Agent 入门实战（微观篇）：动手构建第一个 Agent**
- CSDN 教程：https://blog.csdn.net/qq_17859117/article/details/162409681
- 基于 OpenAI Agents SDK，对比其他框架

### LangGraph 实战（W6 预习）

**《LangGraph 构建智能 AI 应用》**
- B 站链接：https://www.bilibili.com/video/BV1zKx4zWEZP/
- 涵盖：StateGraph、节点、边、条件边、Tool Calling、ReAct、多智能体、HITL、MCP

**LangGraph 中文官方文档**
- 链接：https://langgraph.com.cn/tutorials/get-started/1-build-basic-chatbot/index.html

---

## Week 6：LangGraph、Checkpoint、HITL、中断恢复

### LangGraph 核心（Day 1-4）

**《LangGraph 构建智能 AI 应用：从零开始的完整教程》**
- B 站链接：https://www.bilibili.com/video/BV1zKx4zWEZP/
- 重点：状态图、条件分支、Checkpoint、Interrupt、Command、HITL

**LangGraph 官方文档（Interrupts）**
- https://docs.langchain.com/oss/python/langgraph/interrupts

### LangChain 官方 RAG + Agent 文档
- Retrieval：https://docs.langchain.com/oss/python/langchain/retrieval

---

## Week 7：Agent Harness 企业扩展、Text2SQL、RAG-SQL-API 三路路由

### Agent Harness 概念（Day 1）

**B 站搜 "Agent Harness" 或 "Harness Engineering"**
- 李宏毅智能体 Harness Engineering 系列课程（B 站有转载）
- 重点理解：Loop Engine、Context Engine、Tool Runtime、State、Policy、Workspace、Trace

### Text2SQL（Day 3）

**B 站搜 "Text2SQL" 或 "NL2SQL"**
- 《NL2SQL 运行演示》：https://blog.csdn.net/u010593516/article/details/153196606
- NL2SQL 技术原理与实战指南：https://blog.csdn.net/weixin_51955414/article/details/162274784
- Awesome-Text2SQL 开源项目：https://github.com/eosphoros-ai/awesome-text2sql

### Temporal（可选，Day 6）

**Temporal Java SDK 官方文档**
- https://docs.temporal.io/develop/java
- B 站搜 "Temporal 教程" 有入门视频

---

## Week 8：评测四层、Trace、OpenTelemetry、LLMOps、作品二

### Trace 与可观测性（Day 1）

**OpenTelemetry AI Agent 可观测性**
- 官方博客：https://opentelemetry.io/blog/2025/ai-agent-observability/
- B 站搜 "OpenTelemetry 教程" 有基础视频

**LangSmith / Langfuse**
- LangSmith：https://smith.langchain.com/
- Langfuse：https://langfuse.com/

### RAGAS 深度（Day 4 复习）

- 参见 W4 的 RAGAS 资源

### Agent 评估

**B 站搜 "Agent 评估" 或 "LLM as Judge"**
- Ragas 官方也支持 Text2SQL Agent 评估：https://blog.csdn.net/yongche_shi/article/details/162515061

---

## Week 9：Agent 安全、红队测试、OWASP、Agent Memory

### OWASP LLM Top 10 + Agentic Applications

**OWASP Gen AI Security Project**
- LLM Top 10：https://genai.owasp.org/llm-top-10/
- Agentic Security Initiative：https://genai.owasp.org/initiatives/agentic-security-initiative/

### Agent Memory

**B 站搜 "Agent Memory" 或 "智能体记忆"**
- LangChain Memory 文档：https://python.langchain.com/docs/how_to/chatbots_memory/

### Spring Security（周日 Java 恢复）

**B 站搜 "Spring Security 教程"**
- 配合 W11 的 Spring AI 安全模块

---

## Week 10：模型网关、vLLM 服务化实验、性能容量、部署交付工程

### vLLM 部署（Day 3-4）

**B 站搜 "vLLM 部署" 或 "vLLM 量化"**

**《Qwen2.5-7B-Instruct 保姆级教程：vLLM 量化部署（AWQ/GPTQ）》**
- CSDN 教程：https://blog.csdn.net/weixin_29363791/article/details/158033304

**《从零部署千问模型：使用 vLLM 部署高性能推理服务》**
- 教程：https://www.cnbugs.com/post-8018.html
- 涵盖：PagedAttention、连续批处理、OpenAI 兼容 API、量化推理、Function Calling、生产环境配置

**vLLM 官方 Benchmark CLI**
- https://docs.vllm.ai/en/stable/benchmarking/cli/
- 输出 TTFT、TPOT、ITL、端到端时延

### LiteLLM 集成（Day 2）

**LiteLLM 官方文档**
- https://docs.litellm.ai/
- 涵盖：统一接口、路由、重试、Fallback、虚拟 Key、预算、多租户成本管理

### 部署交付工程（Day 5）

**B 站搜 "Docker Compose 教程" / "CI/CD 教程" / "Kubernetes 入门"**
- 重点：能画出生产部署拓扑，说明每个组件如何扩容和容灾

---

## Week 11：Spring AI、企业系统集成、平台最小版本

### Spring AI 核心课程（必看）

**《Spring AI 快速入门到实战全套教程》（2025 最新版，84 集）**
- B 站链接：https://www.bilibili.com/video/BV1GfyGBqEm6/
- 配套笔记：https://www.bilibili.com/read/cv42180604/
- 涵盖：
  - 第 1-17 集：ChatClient、多模型配置、流式响应
  - 第 18-25 集：Prompt 模板、Advisor 拦截器
  - 第 26-33 集：多轮对话记忆、数据库/Redis 存储
  - 第 34-44 集：结构化输出、Tools（Function Call）、权限控制
  - 第 45-48 集：航空智能客服项目实战
  - 第 49-56 集：MCP 全套（3 种传输方式、STDIO、SSE、源码）
  - 第 57-77 集：RAG 全套（向量、检索增强、rerank、评估）
  - 第 78-84 集：Agent（编排工作者、链式工作流、并行化模式）

**《Spring AI 大模型应用开发零基础全套教程》**
- B 站链接：https://www.bilibili.com/video/BV1X27GznEsf/
- 19 集，较短，适合快速入门

### Spring AI Alibaba

**《Spring AI Alibaba 零基础速通实战》（尚硅谷）**
- B 站搜 "Spring AI Alibaba 尚硅谷"
- 集成阿里云百炼平台、Qwen、DashScope

### Spring AI 官方文档

- Tool Calling：https://docs.spring.io/spring-ai/reference/api/tools.html
- MCP：https://docs.spring.io/spring-ai/reference/api/mcp/mcp-overview.html

### Java 八股复习

**B 站搜 "Java 面试题" / "JVM 调优" / "Spring Boot 原理"**
- 重点：并发（线程池、锁、CAS、AQS）、JVM（内存模型、GC）、Spring Boot 核心机制

---

## Week 12：作品打磨、NFR、FDE Pack、系统设计、模拟面试

### 系统设计

**B 站搜 "系统设计面试" 或 "AI 平台架构设计"**
- 重点练习：设计一个医疗 AI 平台（架构图 + 组件职责 + 选型理由）

### 模拟面试

**参考《FDE 面试备战手册》中的 20 道模拟题**
- 文件路径：FDE面试备战手册.html
- 路线 A（Text2SQL）10 题 + 路线 B（RAG）10 题

---

## 补充资源（跨周通用）

### 大语言模型系统教材

**《大语言模型》（赵鑫等，人大高瓴学院）**
- 网站：https://llmbook-zh.github.io/
- 配套 B 站课程视频
- 涵盖：Transformer、预训练、微调、对齐、提示学习、检索增强生成、智能体、复杂推理

### AI Agent 综合教程

**B 站搜 "AI Agent 智能体教程"**
- 《2026 最新 AI Agent 智能体搭建教程》：B 站搜关键词
- 涵盖：Agent 原理、工具调用、多智能体、工作流

### 开源项目参考

| 项目 | 用途 | 链接 |
|---|---|---|
| LangChain | RAG/Agent 框架 | https://github.com/langchain-ai/langchain |
| LangGraph | 状态机 Agent | https://github.com/langchain-ai/langgraph |
| RAGFlow | 复杂文档 RAG | https://github.com/infiniflow/ragflow |
| Dify | 低代码 AI 平台 | https://github.com/langgenius/dify |
| RAGAS | RAG 评测 | https://github.com/explodinggradients/ragas |
| vLLM | 推理服务 | https://github.com/vllm-project/vllm |
| LiteLLM | 模型网关 | https://github.com/BerriAI/litellm |
| Spring AI | Java AI 集成 | https://github.com/spring-projects/spring-ai |
| OpenAI Agents SDK | Agent 框架 | https://github.com/openai/openai-agents-python |
| Temporal | 持久工作流 | https://github.com/temporalio/temporal-java-sdk |

### 官方文档（随时查阅）

| 文档 | 链接 |
|---|---|
| LangChain | https://python.langchain.com/docs/ |
| LangGraph | https://langgraph.com.cn/ |
| Spring AI | https://docs.spring.io/spring-ai/reference/ |
| MCP 协议 | https://modelcontextprotocol.io/ |
| A2A 协议 | https://a2a-protocol.org/ |
| vLLM | https://docs.vllm.ai/ |
| RAGAS | https://docs.ragas.io/ |
| LiteLLM | https://docs.litellm.ai/ |
| Temporal Java | https://docs.temporal.io/develop/java |
| OWASP LLM | https://genai.owasp.org/llm-top-10/ |
| OpenTelemetry | https://opentelemetry.io/ |

---

## 使用建议

1. **不要全看**：B 站视频很多很长，按需观看对应章节，不要从头看到尾
2. **边看边做**：看完一节立刻动手写代码，不要囤着
3. **官方文档优先**：视频可能过时，官方文档最准（特别是 MCP、Spring AI、vLLM 这类快速迭代的）
4. **每周保留四类证据**：可运行代码 / 测试评测报告 / ADR / 失败案例及修复
5. **视频倍速**：B 站 1.5x-2x 倍速看，节省时间

---

*文档版本：v1.0 · 2026-07-21*
*对应学习路径：FDE学习路径v3.3*
