# FDE Day 1 评测卷 · 大模型核心基础

> 配合《FDE-Day1-大模型核心基础-学习手册.html》使用
> 用法：先按题目自答（写在每题下方），再对照「参考答案要点」自评，最后把答案发给我逐题点评打分
> 评分维度：① 概念准确 ② 原理清晰 ③ 工程直觉（各 1-3 分，单题满分 9 分）

---

## 评测说明

- **L1 基础题**：概念识别，及格线，必须全对
- **L2 进阶题**：原理机制，面试高频
- **L3 深度题**：计算估算 + 工程权衡，区分度高
- **L4 场景题**：结合你的特药理赔 Agent 项目，面试必考
- **达标线**：L1 全对 + 两条面试达标线（Q12、Q13）答完整 = Day 1 过关
- **建议**：先答 L1 + Q12 + Q13（5 题），再答 L2/L3/L4

---

## L1 基础题

### Q1. Self-Attention 的 Q/K/V
用你自己的话解释 Self-Attention 的作用，Q、K、V 分别代表什么？为什么 Q·Kᵀ 之后要除以 √dₖ？

**考察点**：Attention 机制核心
**评分维度**：Q/K/V 物理含义 / 缩放原因 / 表达清晰度

<details>
<summary>参考答案要点（先自答再展开）</summary>

- Q=查询（当前 token 想找什么）、K=键（能提供什么）、V=值（实际内容）
- Q·Kᵀ 算相关性 → softmax 变权重 → 乘 V 加权聚合
- 除以 √dₖ：点积随 dₖ 增大方差变大，会让 softmax 进入饱和区（输出接近 one-hot），梯度消失；缩放把方差控制在敏感区间，稳定训练
- 类比检索：Q 是搜索词，K 是文档关键词，V 是正文
</details>

---

### Q2. Token 估算
Token 和"词"是一回事吗？"医疗保险理赔"6 个汉字，用 Qwen 的 tokenizer 大概几个 token？为什么中英文 token 消耗差异大？

**考察点**：token 本质 + 跨模型差异
**评分维度**：token 定义 / 估算合理性 / 中英差异原因

<details>
<summary>参考答案要点（先自答再展开）</summary>

- 不是一回事，token 是子词单元（非词非字）
- "医疗保险理赔"用 Qwen 约 4-6 个 token（Qwen 对中文优化较好，常见词可能整词保留）；不同模型结果不同，必须实测
- 中英差异：BPE 主要在英文语料上训练，英文合并得更整（1 token ≈ 4 字符），中文常见 1 字 1-2 token
- 关键：不同模型 tokenizer 不通用，token 数不能跨模型换算，做成本估算和 RAG chunk 切分必须用目标模型实测
</details>

---

### Q3. 上下文窗口
什么是上下文窗口？如果输入已经占满窗口，模型还能输出吗？为什么长对话到后面模型会"忘事"？

**考察点**：上下文窗口定义 + 超限行为
**评分维度**：定义准确 / 超限处理 / 长对话遗忘原因

<details>
<summary>参考答案要点（先自答再展开）</summary>

- 上下文窗口 = 输入 + 输出 token 总上限（如 128K）
- 占满后无法输出：现代 API 直接报错；早期会截断丢信息
- 长对话遗忘：早期内容超出窗口被截断/挤出；即使没超，也有"中间迷失"（Lost in the Middle）——对中间内容利用率低
- 工程对策：RAG 精准检索 / 摘要压缩 / 把关键信息放开头
</details>

---

## L2 进阶题

### Q4. Multi-Head Attention
为什么需要 Multi-Head Attention？单头不行吗？"头数"多些或少些分别有什么影响？现代模型为什么用 GQA？

**考察点**：多头动机 + GQA 工程价值
**评分维度**：多头动机 / 头数影响 / GQA 理解

<details>
<summary>参考答案要点（先自答再展开）</summary>

- 单头只能学一种关注模式；语言关系多样（语法/指代/语义/位置），多头在不同子空间学不同模式
- 多头是"拆分"不是"叠加"：把 d_model 拆成 h 份，总维度守恒，计算量基本不变
- 头多：模式多样但单头维度小、投影参数增；头少：单头强但模式少；d_head 常取 64/128
- GQA：多个 Q 头共享一组 K/V，几乎不损效果但大幅减少 KV Cache 显存 → 同卡并发和上下文长度翻倍；LLaMA-3/Qwen2 都用
- 工程意义：选模型看 n_kv_heads，GQA 模型部署更省显存
</details>

---

### Q5. KV Cache 原理
KV Cache 为什么能加速？它用"空间"换了什么"时间"？为什么多轮对话里它能省大量重复计算？它有没有代价？

**考察点**：KV Cache 机制 + 代价权衡
**评分维度**：加速原理 / 空间换时间 / 代价认知

<details>
<summary>参考答案要点（先自答再展开）</summary>

- 缓存历史 token 的 K/V，每生成新 token 只算自己的 Q/K/V，不用重算历史
- 把每步计算量从 O(n) 降到 O(1)，整体生成成本从 O(n²) 降到 O(n)
- 多轮对话：历史 K/V 不变，留着缓存下一轮直接用，不用重算 Prefill
- 代价：吃显存！KV Cache = 2 × n_layers × seq_len × n_kv_heads × d_head × dtype_bytes；长上下文单请求几 GB，压低并发
- 注意：无状态 API 每轮重传全部历史，KV Cache 每轮要重算 Prefill，是否复用取决于引擎是否支持前缀缓存（prefix caching）
</details>

---

### Q6. Prefill vs Decode
Prefill 和 Decode 哪个是计算瓶颈？哪个是访存瓶颈？为什么 Decode 阶段 GPU 利用率往往很低？Continuous Batching 怎么缓解？

**考察点**：两阶段计算特征 + 优化手段
**评分维度**：瓶颈判断 / 利用率原因 / 优化理解

<details>
<summary>参考答案要点（先自答再展开）</summary>

- Prefill：计算密集（一次处理整个 prompt，大矩阵乘，GPU 满载），决定 TTFT
- Decode：访存密集（每步只算 1 token 却要读全部权重+缓存，算力闲置等数据），决定 TPOT
- Decode 利用率低：本质是访存瓶颈，不是模型大；每步 1 个 token 的算力远小于读权重的开销
- Continuous Batching：把多个请求的 Decode 步动态拼成 batch，多个"1 token"凑成大矩阵，摊薄访存，提高利用率（vLLM 核心）
- 其他：Speculative Decoding（小模型猜+大模型验）、Chunked Prefill
</details>

---

### Q7. Decoder-only 主流
现在主流大模型几乎都是 Decoder-only，而不是原始的 Encoder-Decoder。Decoder-only 在生成任务上有什么结构性优势？

**考察点**：架构选型理解
**评分维度**：统一范式 / 训练效率 / Scaling

<details>
<summary>参考答案要点（先自答再展开）</summary>

- 统一范式：所有任务归约为"预测下一个 token"，翻译/问答/写代码/Agent 一套架构通吃
- 训练效率：因果注意力 + teacher forcing，每个位置都有监督信号，样本利用率高
- Scaling Law 友好：参数和数据规模上去后能力持续提升，无明显架构瓶颈
- 推理简单：自回归生成天然契合 KV Cache
- 工程红利：一套推理引擎、一套缓存、一套 Scaling 适用所有任务
</details>

---

## L3 深度题

### Q8.（面试达标线①）一次 API 请求的完整过程
完整描述一次 API 请求从你发出到返回 token 的全过程，按顺序串起每一步，并说清 Prefill 和 Decode 在哪、KV Cache 何时填入、流式怎么返回。

**考察点**：端到端链路理解（Day 1 核心）
**评分维度**：步骤完整 / 阶段划分 / 工程细节

<details>
<summary>参考答案要点（先自答再展开）</summary>

8 步链路：
1. 客户端 POST 请求（prompt + 模型名 + 参数）
2. 网关：鉴权/限流/路由/记录 Trace ID
3. Tokenizer 切分 prompt → token id 序列（定输入 token 数=计费依据）
4. Embedding 查表 + 位置编码（RoPE）→ 初始向量序列
5. **Prefill**：整个 prompt 过 N 层 Block，每层算 Attention（带因果掩码）+FFN，K/V 存入 **KV Cache**；输出 logits。计算密集，决定 TTFT
6. **Decode 循环**：最后位置 logits → softmax → 采样（temperature/top_p）出下一个 token → 新 token 的 K/V 追加缓存 → 重复到 stop/max_tokens。每步访存密集，决定 TPOT
7. 反 Tokenize：token id → 文本
8. 流式：每生成一个 token 通过 SSE 推给客户端（打字机效果）；非流式等全部生成完返回

加分：点出 Prefill/Decode 计算特征、TTFT/TPOT 来源、采样策略、因果掩码
</details>

---

### Q9.（面试达标线②）上下文/延迟/成本关系
上下文长度、延迟、成本三者是什么关系？为什么同样生成 1000 token，8K 上下文和 128K 上下文的价格/延迟差很多？背后的计算量差异在哪？这跟"为什么用 RAG"有什么关系？

**考察点**：三者关系 + RAG 选型逻辑
**评分维度**：关系清晰 / 计算量解释 / RAG 关联

<details>
<summary>参考答案要点（先自答再展开）</summary>

- 延迟：上下文长 → Prefill 计算量 O(n²) 平方增长 → TTFT 高；128K 比 4K 的 Prefill 计算量约 1024 倍
- 成本：input token 按量计费，长 prompt 直接贵；部分长上下文模型输入单价本身更高
- 显存：KV Cache 线性增长，128K 单请求几 GB，单卡并发骤降，单请求分摊成本上升
- 计算量差异在 Attention 的 n×n 矩阵 + KV Cache 显存
- RAG 关联：正因为长上下文又贵又慢 + 中间迷失，企业知识场景该用 RAG 精准喂几 K，而不是堆满长上下文。长上下文不是 RAG 替代，是补充（适合分析单个长文档）
</details>

---

### Q10. KV Cache 显存估算
KV Cache 占用显存怎么估算？一个 7B 模型（32 层，GQA n_kv_heads=8，d_head=128），上下文 32K，fp16，单请求 KV Cache 多大？这跟"长上下文贵"什么关系？

**考察点**：显存估算公式 + 工程意义
**评分维度**：公式正确 / 计算准确 / 工程关联

<details>
<summary>参考答案要点（先自答再展开）</summary>

- 公式：KV Cache = 2 × n_layers × seq_len × n_kv_heads × d_head × dtype_bytes
  - 2 = K 和 V 两份
- 代入：2 × 32 × 32768 × 8 × 128 × 2 = 4,294,967,296 bytes ≈ 4 GB
- 7B 模型参数 fp16 约 14 GB，单请求 KV Cache 就占近 30%
- 关系：长上下文单请求 KV Cache 几 GB，显存被吃光 → 单卡并发请求数骤降 → 单请求分摊的固定成本上升 → 又贵又慢
- 这解释了 GQA（减 n_kv_heads）、KV 量化（减 dtype_bytes）、PagedAttention（减少碎片）为何重要
</details>

---

### Q11. 推理模型选型
推理模型（DeepSeek-R1）和普通模型（Qwen-Plus）在 API 调用、成本、延迟上有什么不同？在你的特药理赔 Agent 里，OCR 字段抽取、风险等级判断、材料完整性检查、拒赔理由生成，分别该用哪种？为什么？

**考察点**：推理模型理解 + 项目场景应用
**评分维度**：差异认知 / 场景判断 / 理由充分

<details>
<summary>参考答案要点（先自答再展开）</summary>

- 差异：推理模型先产生思维链(CoT)再答，返回 reasoning_content + content；思考 token 计费、延迟高；擅长数学/逻辑/规划；普通模型直接答，快且便宜，擅长分类/抽取/对话
- OCR 字段抽取 → 普通模型（抽取任务，推理模型又慢又贵）
- 材料完整性检查 → 普通模型（规则性判断）
- 风险等级判断 → 推理模型（需综合多条款推理，高风险漏判代价大）
- 拒赔理由生成 → 推理模型（需引用多条规则串联论证）
- 策略：Agent 主流程用普通模型保速度，关键决策节点调推理模型，符合"规则/RAG/Agent/人工职责划分"
- 注意：部分推理模型 function calling 支持有限，做 Agent 前要验证；reasoning_content 要单独记 Trace
</details>

---

## L4 场景应用题

### Q12. RAG 成本估算
你的企业保险知识库 RAG，每次查询检索 5 个 chunk（每个 500 token）+ system prompt 800 token + 用户问题 50 token，输出 300 token。用 Qwen-Plus（输入 ¥0.0008/1K token，输出 ¥0.002/1K token）。单次查询成本多少？如果每天 10000 次查询，月成本多少？如果要降本，你会怎么做？

**考察点**：token 成本计算 + 降本策略
**评分维度**：计算正确 / 降本思路 / 工程可行

<details>
<summary>参考答案要点（先自答再展开）</summary>

- 输入 = 5×500 + 800 + 50 = 3350 token
- 输出 = 300 token
- 单次成本 = 3350/1000 × 0.0008 + 300/1000 × 0.002 = 0.00268 + 0.0006 = ¥0.00328
- 日成本 = 10000 × 0.00328 = ¥32.8
- 月成本 ≈ ¥984（约 1000 元）
- 降本：① 减少检索 chunk 数（5→3，用 Rerank 提精度）② 压缩 system prompt ③ 缓存高频问答（语义缓存）④ 简单问题路由到更小/更便宜模型 ⑤ 输出长度限制 ⑥ chunk 大小优化（避免冗余）
- 加分：指出输出单价是输入 2.5 倍，控制输出长度收益更大
</details>

---

### Q13. TTFT 优化
你的特药理赔 Agent，用户上传理赔材料后要等 8 秒才出第一个字（TTFT 高）。可能的原因有哪些？你会怎么排查和优化？(提示：从 prompt 长度、检索、Prefill、模型选型、并发角度想)

**考察点**：延迟问题排查 + 优化手段
**评分维度**：原因全面 / 排查逻辑 / 优化可行

<details>
<summary>参考答案要点（先自答再展开）</summary>

可能原因：
- prompt 过长：检索召回 chunk 太多/太大 → Prefill O(n²) 慢
- 检索本身慢：ES 向量检索 + Rerank 耗时
- 模型大：72B 比 7B 的 Prefill 慢得多
- 并发高：其他请求占满 GPU，本请求排队
- 网关/网络延迟

排查：Trace 各阶段耗时（检索/Prefill/Decode 分别多少），定位瓶颈

优化：
- 减 prompt：检索 chunk 5→3、chunk 压缩、system prompt 精简
- 检索优化：ES 索引调优、Rerank 用小模型、异步预检索
- 模型：简单步骤用小模型，关键步骤才用大模型
- 并发：Continuous Batching、限流、扩容
- Prefill：Chunked Prefill 交错执行避免阻塞
- 前缀缓存：system prompt 等固定部分复用 KV Cache
- 流式：确保开了流式，TTFT 体验比端到端重要
</details>

---

## 评分汇总表

| 题号 | 等级 | 考察点 | 满分 | 自评 | 我评 |
|---|---|---|---|---|---|
| Q1 | L1 | Self-Attention Q/K/V | 9 | | |
| Q2 | L1 | Token 估算 | 9 | | |
| Q3 | L1 | 上下文窗口 | 9 | | |
| Q4 | L2 | Multi-Head/GQA | 9 | | |
| Q5 | L2 | KV Cache 原理 | 9 | | |
| Q6 | L2 | Prefill/Decode | 9 | | |
| Q7 | L2 | Decoder-only 主流 | 9 | | |
| Q8 | L3 | API 请求全过程（达标线①）| 9 | | |
| Q9 | L3 | 上下文/延迟/成本（达标线②）| 9 | | |
| Q10 | L3 | KV Cache 显存估算 | 9 | | |
| Q11 | L3 | 推理模型选型 | 9 | | |
| Q12 | L4 | RAG 成本估算 | 9 | | |
| Q13 | L4 | TTFT 优化 | 9 | | |

**总分 117 分。达标线：L1 全对 + Q8 + Q9 完整 = Day 1 过关。**

---

## 答题指引

1. 先不看参考答案，逐题自答（可简答，写关键词即可）
2. 答完对照参考答案要点自评，在「自评」列打分
3. 把答案发给我，我逐题点评、给「我评」分数、指出漏点
4. 重点关注：Q8（API 全过程）和 Q9（三者关系）是面试必考，必须能流畅讲完
5. Q10、Q12、Q13 是工程估算题，能答出来直接证明"懂工程"，面试加分

---

*FDE Day1 评测卷 · 配合学习手册使用 · 答题后发我逐题点评*
