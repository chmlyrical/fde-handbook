# FDE W1D4 评测题 · 多模型统一接入

> 配套手册：`FDE-W1D4-多模型统一接入-学习手册.html`
> 候选人背景：36 岁，Java + 大数据 + 医疗保险行业。场景示例围绕医疗保险 / 特药理赔 / 保险知识库。
> 满分 **117** 分。达标线：**≥ 90 分**（且 L4 场景题两题均不得 0 分）。

---

## L1 基础（Q1–Q3，每题 5 分，共 15 分）

### Q1（5 分）统一接入层价值
**题目**：直接裸调各家模型 API 有哪些痛点？统一接入层解决了哪几类问题？

- **考察点**：对统一接入层存在意义的认知。
- **评分维度**：
  - 痛点：厂商锁定、无容错、不可观测、重复造轮子（答出 3 个得 3 分，4 个得满分）
  - 解决：换模型/降级/观测/复用（2 分）
- <details>
  <summary>参考答案</summary>

  裸调痛点：① 厂商锁定（换模型改代码）；② 无容错（某家宕机全挂）；③ 不可观测（成本/延迟/错误散落）；④ 重复造轮子（超时/重试/统计各写一遍）。
  统一层解决：业务只面对一个接口，背后可任意换模型、可降级、可统计成本、可追踪。
  </details>

### Q2（5 分）统一请求结构
**题目**：请列出统一请求/响应结构应该包含的至少 5 个关键字段，并说明 `usage` 字段为什么要归一。

- **考察点**：Model Adapter 的接口设计意识。
- **评分维度**：
  - 字段：model_key、messages、params、response_format、stream、trace_id、timeout_ms、latency_ms 等（5 个得 4 分，含 usage 说明得满分）
  - usage 归一理由：各家字段名不同，不归一成本统计错（1 分）
- <details>
  <summary>参考答案</summary>

  关键字段：model_key（逻辑模型名）、messages、params（temperature 等）、response_format、stream、trace_id、timeout_ms；响应含 content、finish_reason、usage、provider、latency_ms。
  usage 必须归一：各家命名不同（prompt_tokens vs input_tokens），统一层若不归一，成本统计会错。
  </details>

### Q3（5 分）路由是什么
**题目**：模型路由（Routing）决定什么？请说出至少 3 种路由策略。

- **考察点**：路由的基本概念与策略多样性。
- **评分维度**：
  - 路由定义：决定请求走哪个模型（2 分）
  - 3 种策略：静态/能力级联/健康度/灰度/A/B/语义分类（3 分）
- <details>
  <summary>参考答案</summary>

  路由决定"这次请求走哪个逻辑模型"。策略：① 静态配置；② 基于能力级联（简单→小模型）；③ 基于健康度（主挂切备）；④ 基于灰度/A-B（user_id 分流）；⑤ 基于语义分类（先分类再路由）。
  </details>

---

## L2 进阶（Q4–Q7，每题 8 分，共 32 分）

### Q4（8 分）Provider Adapter 差异
**题目**：Qwen、DeepSeek、Claude、Ollama 在接入上有哪些主要差异？Adapter 需要针对 Claude 特别处理什么？

- **考察点**：多厂商适配的真实经验。
- **评分维度**：
  - 差异点：鉴权、system 位置、流式、结构化、错误码（每点 1 分，共 4 分）
  - Claude 特殊处理：system 顶层字段、tool_use、限流码、错误归一（4 分）
- <details>
  <summary>参考答案</summary>

  差异：鉴权方式（Bearer vs x-api-key）、system 位置（Claude 是顶层字段）、流式事件格式、结构化方式（Claude 用 tool_use/预填充，其他用 response_format）、限流码（Claude 429/529）。
  Claude 特别处理：① system 是顶层字段不是 messages[role=system]；② 工具调用是 tool_use 格式；③ 限流/过载码不同需映射；④ 错误码归一化成统一错误类型。Qwen/DeepSeek/Ollama 多为 OpenAI 兼容可共用基类。
  </details>

### Q5（8 分）超时与重试
**题目**：请设计模型调用的超时与重试机制：超时分哪几种？重试必须包含哪三个要素？哪些错误不应该重试？

- **考察点**：容错机制的设计功底。
- **评分维度**：
  - 超时三种：连接/读取/整体预算（3 分）
  - 重试三要素：指数退避+抖动、限次、仅幂等/可重试（3 分）
  - 不重试：400/401 参数/鉴权错（2 分）
- <details>
  <summary>参考答案</summary>

  超时：连接超时（短，建连慢早失败）、读取超时（长，生成慢）、整体预算（业务 SLA 上限）。
  重试三要素：① 指数退避+抖动（防重试风暴）；② 限次（2~3 次）；③ 仅重试可重试错误（429/5xx 临时）。
  不重试：400（参数错）、401（鉴权错）——重试无意义，直接报错。
  </details>

### Q6（8 分）Fallback 层级
**题目**：请画出 Fallback 的层级链，并说清设计 Fallback 时必须注意的 3 个要点。

- **考察点**：降级策略的完整性。
- **评分维度**：
  - 层级链：换模型→换厂商→小模型/规则→缓存/转人工（3 分）
  - 三要点：备选任务等价、超时防连环、熔断（5 分）
- <details>
  <summary>参考答案</summary>

  层级链：主模型失败且重试尽 → 同厂商换模型 → 换厂商 → 小模型/规则 → 缓存/默认/转人工。
  三要点：① 备选要"任务等价"，否则降级成答非所问比报错更糟（保险场景错答比拒答危险）；② 给 Fallback 自身设超时，防"主慢+备慢"连环拖时；③ 熔断——某 provider 连续失败临时摘除，恢复再放。
  </details>

### Q7（8 分）Token 成本记录
**题目**：Token 成本记录要包含哪些维度？为什么必须记录 `retry_count` 和 `is_fallback`？

- **考察点**：成本可观测性。
- **评分维度**：
  - 维度：model/provider、prompt/completion tokens、单价、tenant/user、trace_id、retry_count、is_fallback（5 分）
  - 为什么含 retry/fallback：重试和降级额外烧 token，不含则账单失真、发现不了疯狂重试（3 分）
- <details>
  <summary>参考答案</summary>

  维度：model/provider、prompt_tokens/completion_tokens（拆分，单价不同）、单价配置、user_id/tenant_id、trace_id、retry_count、is_fallback。
  必须含 retry_count 和 is_fallback：重试和 Fallback 会额外消耗 token，若不记录，成本账单对不上，也无法发现"某路径在疯狂重试"的异常。
  </details>

---

## L3 深度（Q8–Q11，每 12 分，共 48 分）

### Q8（12 分）画出统一接入架构
**题目**：请完整画出多模型统一接入层的架构图（文字或图示），并说明每一段的职责。这是达标线①。

- **考察点**：整体架构表达能力（核心达标线）。
- **评分维度**：
  - 业务侧发统一请求（带 trace_id）（2 分）
  - 网关：路由/超时/重试/Fallback/流式归一（5 分）
  - Provider Adapter 层（翻译+归一）（2 分）
  - 厂商/本地（2 分）
  - 横切：成本+Trace+监控（1 分）
- <details>
  <summary>参考答案</summary>

  ```
  业务服务
    │ UnifiedRequest(trace_id, model_key, messages, params)
    ▼
  [统一接入网关]
    ├─ 路由 Routing（规则/健康度/灰度）
    ├─ 超时 Timeout（连接/读取/预算）
    ├─ 重试 Retry（退避+限次，仅可重试）
    ├─ Fallback（换模型→换厂商→兜底，熔断）
    └─ 流式归一 Streaming（chunk 归一、TTFT）
    ▼
  [Provider Adapter]：Qwen / DeepSeek / Claude / Ollama
    ▼
  [厂商 API / 本地模型]
    ▲
  横切：Token 成本记录 + Trace 全链路 + 监控告警
  ```
  每段职责见手册 s10。漏掉横切（成本/Trace）或 Fallback 即为不及格。
  </details>

### Q9（12 分）Trace ID 全链路设计
**题目**：请设计 Trace ID 在统一接入层的贯穿方案：它要解决什么问题？在重试和 Fallback 时如何保证"一次请求可关联"？记录哪些字段？

- **考察点**：可观测性设计深度。
- **评分维度**：
  - 解决问题：线上排查、成本归因、复现（3 分）
  - 透传：重试/Fallback 时 trace_id 不变（4 分）
  - 记录字段：model/provider/latency/usage/error/降级、脱敏（5 分）
- <details>
  <summary>参考答案</summary>

  解决问题：一次用户投诉无法定位是路由错、provider 超时还是 prompt 问题；成本按请求归因；问题复现。
  透传：入口生成 trace_id，在路由、各 provider 调用、重试、Fallback 之间全程透传且<strong>保持不变</strong>——这样一次请求即使经历多次重试/降级，所有 span 都能用同一个 trace_id 关联。
  记录字段：trace_id、每次调用的 model_key/实际 provider/latency_ms/usage/finish_reason、retry_count、is_fallback、fallback_to、error_type；业务上下文（user_id、模块）需脱敏，医保场景不落明文 PII。建议用 OpenTelemetry 把模型调用做成 span。
  </details>

### Q10（12 分）流式输出统一层
**题目**：流式输出下，统一接入层要额外处理哪些事？为什么说"流式中断重试"比普通重试更难？

- **考察点**：流式场景的工程细节。
- **评分维度**：
  - chunk 格式归一、TTFT/TPOT 指标、取消/中断、流式重试（6 分）
  - 流式重试难点：已返回部分内容、可能重复、需优雅降级（6 分）
- <details>
  <summary>参考答案</summary>

  额外处理：① 归一各厂商 chunk 格式为统一增量结构；② 统计 TTFT（首字延迟）和 TPOT（每 token 间隔）作为体验指标；③ 支持用户取消（关闭连接即取消后端调用省算力）；④ 流式下的超时/重试要谨慎。
  流式重试难点：普通重试是"整体重发拿完整结果"；流式已开始返回内容，中途失败若整体重发，用户会看到"前半段 + 重复前半段"或卡死。需设计：要么断点续传（难），要么明确提示"重新生成"，要么对可切分任务做局部重试，避免重复与卡死。
  </details>

### Q11（12 分）用开源方案但懂原理
**题目**：如果让你用 LiteLLM 做统一接入网关，它已经帮你覆盖了哪些能力？但作为 FDE，你仍需要自己设计和决策什么？

- **考察点**：工具与原理的边界认知。
- **评分维度**：
  - LiteLLM 覆盖：统一接口、路由、重试、Fallback、成本追踪、虚拟 key（5 分）
  - 仍需自己设计：路由策略、熔断阈值、合规脱敏、私有 Trace、监控告警、密钥管理（7 分）
- <details>
  <summary>参考答案</summary>

  LiteLLM 已覆盖：100+ 模型统一接口、负载均衡、retry、fallbacks、timeout、cost tracking、虚拟 key——可直接当生产网关。
  仍需自己设计：① 路由策略（什么请求走什么模型，结合 Day3 选型）；② 熔断阈值与摘除策略；③ 合规脱敏（医保出域前 PII 打码、Trace 不落明文）；④ 私有 Trace/日志方案；⑤ 监控告警（token 异常、延迟、错误率）；⑥ 密钥管理（不硬编码）。面试考的是"原理"而非"用哪个库"——懂原理才能在定制时改得动。
  </details>

---

## L4 场景（Q12–Q13，每 11 分，共 22 分）

### Q12（11 分）【场景】医保多模型冗余接入
**题目**：某医保理赔系统对可用性和合规都极敏感。请设计其统一接入层：主备如何冗余？Fallback 链怎么排？出域数据怎么处理？成本与 Trace 怎么落到"理赔单号"维度？

- **考察点**：达标线②在真实场景的落地——Fallback + 成本/Trace。
- **评分维度**：
  - 主备冗余：本地模型主（保隐私）+ 地端大模型备（脱敏）（3 分）
  - Fallback 链：本地→地端→规则/转人工（3 分）
  - 出域脱敏：PII 打码、Trace 不落明文（2 分）
  - 成本/Trace 落到单号：按理赔单号/机构记录 token、trace_id 贯穿（3 分）
- <details>
  <summary>参考答案</summary>

  主备冗余：主用本地 Qwen/Ollama（数据不出域）做抽取/初审；备用地端 DeepSeek/Claude，仅对脱敏后的复杂 case 调用。
  Fallback 链：本地模型超时/失败 → 地端大模型（脱敏数据）→ 仍失败 → 规则兜底/转人工。
  出域处理：Adapter 出域前做 PII 脱敏（姓名/身份证/病历摘要打码）；Trace 日志不落明文 PII。
  成本/Trace 落单号：每次模型调用都带 tenant/理赔单号，记录 prompt/completion tokens、retry_count、is_fallback，按机构/单号分摊成本；trace_id 从用户提交贯穿到最终判定（含人工环节），用于全链路排查。
  </details>

### Q13（11 分）【场景】成本失控复盘
**题目**：监控发现某保险客服功能的模型费用一周暴涨 3 倍，但调用量没变。作为 FDE，你如何用接入层的成本与 Trace 数据定位原因？可能的原因有哪些？

- **考察点**：用可观测数据排查真实问题。
- **评分维度**：
  - 定位方法：按 tenant/功能/trace 下钻，看 retry_count、is_fallback、平均 tokens（4 分）
  - 可能原因：疯狂重试、频繁 Fallback、prompt 膨胀、恶意注入循环、模型切换更贵（4 分）
  - 措施：设重试上限、熔断、告警、prompt 收紧（3 分）
- <details>
  <summary>参考答案</summary>

  定位：用成本记录按 tenant/功能模块下钻，结合 Trace 看哪些请求的 retry_count 高、is_fallback=true 占比高、平均 tokens 异常。锁定"高成本路径"后按 trace_id 回放具体请求。
  可能原因：① 某 provider 不稳导致疯狂重试（retry 烧 token）；② 主模型频繁失败触发 Fallback 到更贵的模型；③ prompt 被注入/拼接膨胀导致 tokens 暴涨；④ 误切换到高单价模型；⑤ 死循环多轮调用。
  措施：设重试上限与熔断、对 token 异常暴涨告警、收紧 prompt、修复 provider 健康度、对高成本路径限流。
  </details>

---

## 评分汇总表（满分 117）

| 级别 | 题号 | 单题分 | 小计 |
|---|---|---|---|
| L1 基础 | Q1–Q3 | 5×3 | 15 |
| L2 进阶 | Q4–Q7 | 8×4 | 32 |
| L3 深度 | Q8–Q11 | 12×4 | 48 |
| L4 场景 | Q12–Q13 | 11×2 | 22 |
| **合计** | | | **117** |

### 达标线
- **达标（推荐录用底线）**：≥ 90 分，且 **L4 两题均不得 0 分**。
- **优秀（senior 水平）**：≥ 105 分，且能徒手画出架构图（Q8）与讲清医保场景的 Fallback+成本/Trace（Q12），并主动提到"熔断""脱敏""重试会烧成本"。
- **不达标**：< 90 分，或 L4 任一题 0 分——说明统一接入的工程框架未建立，无法独立设计生产级接入层。

### 评分说明
- L1/L2 看"记忆与理解"，按点给分，答出核心关键词即可。
- L3/L4 看"结构化表达 + 业务贴合度"，需结合医疗保险场景举例方可得满分；纯背概念缺场景扣 30%。
- 鼓励候选人在任何一题补充"熔断 / 脱敏 / 重试烧成本"等生产视角，酌情 +1~2 分（不超单题上限）。
