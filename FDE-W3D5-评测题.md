# FDE W3D5 评测题 · RAG 评测（A 级）

>  candidate: 36 岁，Java + 大数据 + 医疗保险背景，目标 FDE。
>  场景统一用「医疗保险 / 特药理赔 / 保险知识库」。
>  评分维度通用：①概念准确 ②公式/口径清晰 ③工程落地 ④避坑意识。每题满分 5 分。

---

## L1 基础（概念理解）

### Q1. 为什么 RAG 评测必须"分层"？检索层和生成层各关注什么？
- **考察点**：分层评测的必要性；两层关注点。
- **评分维度**：能否说明"只测最终答案会掩盖检索错误"；能否列出检索层（召回/排序）与生成层（忠实/相关/引用/拒答）。
<details>
<summary>参考答案</summary>

只测最终答案无法区分"检索就错了"还是"检索对但生成坏"，导致改错方向。检索层关注：是否召回相关文档、排序质量（Hit Rate/Recall/Precision/MRR/nDCG）。生成层关注：答案忠实度、相关性、引用准确性、拒答准确率。分层才能定位根因、指导迭代。

</details>

### Q2. 请解释 Hit Rate@K、Recall@K、Precision@K 三者区别。
- **考察点**：三个检索指标的定义与差异。
- **评分维度**：Hit=至少命中一个；Recall=相关被捞到比例；Precision=捞上来的里相关比例；是否提"固定 K"。
<details>
<summary>参考答案</summary>

- Hit Rate@K：topK 中至少命中一个相关文档的 query 占比 → 有没有捞到。
- Recall@K：|相关∩topK| / |全部相关| → 相关文档漏没漏（覆盖）。
- Precision@K：|相关∩topK| / K → 捞上来的对不对（噪声）。
三者必须固定 K 再横向比较；Hit 只看"有无"会掩盖"只捞到一个但漏了关键那个"，故要配合 Recall。

</details>

### Q3. 测试集为什么是"20 人工 Golden + 30 模型辅助人工抽检"？各有什么作用？
- **考察点**：测试集建设策略与权衡。
- **评分维度**：能否说清 Golden 作金标准锚定；模型生成扩量；人工抽检保底质量并补难例。
<details>
<summary>参考答案</summary>

20 条人工 Golden 由专家编写，质量最高，作为"金标准"锚定评测可信度。30 条模型辅助生成（LLM 基于知识库片段批量产出问题+参考答案+期望文档）再人工抽检修正，扩量且控成本；抽检时特意补拒答/越权/长尾难例，避免集子偏科。纯人工 50 条太慢，纯模型易同质化，折中最优。

</details>

---

## L2 进阶（指标机制）

### Q4. MRR 和 nDCG@K 衡量排序质量的什么？为什么需要它们而不能只靠 Recall？
- **考察点**：排序类指标；与覆盖率指标的互补。
- **评分维度**：MRR=第一个相关项排名倒数的均值；nDCG=位置加权相关性；能否说明"排序影响生成质量"。
<details>
<summary>参考答案</summary>

MRR（Mean Reciprocal Rank）= 每个 query 第一个相关文档排名倒数的平均，衡量"对的相关项排多前"。nDCG@K 对排序位置做折扣加权（越靠前相关性贡献越大），衡量整体排序质量。Recall 只管"捞到没"，不管"排第几"；但 RAG 生成通常只用 top 几片，排得靠前才能进上下文，故必须 MRR/nDCG 补位。

</details>

### Q5. Faithfulness 和 Answer Relevancy 有什么区别？为什么两者要一起看？
- **考察点**：两个生成指标的本质差异与互补。
- **评分维度**：Faithfulness=基于上下文无编造；Relevancy=切题；能否举"忠实于错误上下文"反例。
<details>
<summary>参考答案</summary>

Faithfulness = 答案是否完全基于检索上下文、有无编造（防幻觉）。Answer Relevancy = 答案是否切题、答到点上（不管对错）。要一起看：Faithfulness 高但 Relevancy 低 = 忠实地复述了无关上下文；Relevancy 高但 Faithfulness 低 = 答到点上却编造。且两者都依赖检索质量——检索错了 Faithfulness 也会"忠实于错误上下文"。

</details>

### Q6. 引用准确率包含哪三个维度？在保险场景为什么是"生死线"？
- **考察点**：引用准确性的细分；业务必要性。
- **评分维度**：存在性/正确性/完整性；能否联系合规审计、可回溯。
<details>
<summary>参考答案</summary>

三维度：①引用存在性（chunk_id 真实存在，无虚构）；②引用正确性（该 chunk 真支持对应 claim，无张冠李戴）；③引用完整性（关键结论都挂来源）。保险场景理赔结论涉及金额与责任，引用错 = 审计事故、合规风险，必须可回溯到具体条款段落供人工复核，故是生死线。

</details>

### Q7. RAGAS 和 LangSmith 在 RAG 评测里分别扮演什么角色？怎么组合？
- **考察点**：工具定位与组合。
- **评分维度**：RAGAS=指标算法库（少标注）；LangSmith=平台（数据集/Trace/在线评测）；组合用法。
<details>
<summary>参考答案</summary>

RAGAS 是开源指标库（faithfulness/answer_relevancy/context_precision/context_recall 等，多用 LLM 当裁判、少人工标注）。LangSmith 是实验平台：Dataset 管理测试集、Tracing 看每条链路、在线 evaluator 跑指标、回归对比。组合：用 RAGAS 的指标定义 + LangSmith 跑数据集并看 Trace 定位坏 case（B站 BV1aZ421W7DB 有实操）。

</details>

---

## L3 深度（Case 设计 + 拒答评测）

### Q8. 请写出你设计的"一个 Case"完整字段，并说明每个字段服务哪个环节。
- **考察点**：Case 字段设计完整性。
- **评分维度**：9 字段齐全；expected_* 服务检索判分，key_points 服务生成判分，refuse/roles 服务安全，version 服务可复现。
<details>
<summary>参考答案</summary>

字段：case_id / question / question_type / expected_document_ids / expected_chunk_ids / answer_key_points / should_refuse / allowed_roles / document_version。
- expected_document_ids / expected_chunk_ids → 检索层判分（Recall/Precision/Hit，chunk 级更精确）。
- answer_key_points → 生成层判分（Relevancy/Correctness）。
- should_refuse / allowed_roles → 拒答与越权评测。
- document_version → 固定知识库版本，保证可复现、可对比。

</details>

### Q9. 拒答准确率怎么测？为什么"一律拒答"是陷阱？
- **考察点**：拒答评测的口径；精确率/召回率陷阱。
- **评分维度**：在 should_refuse=true 子集算 P/R/F1；能否说明一律拒答精确率虚高、召回崩。
<details>
<summary>参考答案</summary>

在测试集标 should_refuse=true 的 case（含 allowed_roles 不符的越权）上：精确率 = 正确拒答 / 系统拒答总数（防误答幻觉）；召回率 = 正确拒答 / 应拒答总数（防漏答）；F1 综合。并单独统计 allowed_roles 越权拒答。陷阱：若系统"一律拒答"，精确率虚高但召回率崩（该答的也拒了），用户体验差、业务损失，故必须同时看 P 和 R。

</details>

### Q10. 如果用 LLM 当裁判（LLM-as-judge）评 Faithfulness，有哪些偏差？怎么缓解？
- **考察点**：自动评测的局限与校准。
- **评分维度**：偏好长答案、自评偏高、依赖检索上下文正确；缓解：人工抽检校准、多裁判、结合规则。
<details>
<summary>参考答案</summary>

偏差：①偏好长/结构化答案；②对"自己生成风格"的答案评分偏高（自评偏）；③裁判本身依赖检索上下文传入正确，检索错了它也错；④中文保险领域提示词未校准会失真。缓解：人工抽检纠偏并校准阈值；多模型交叉裁判；关键指标（如拒答）保留人工标注子集做金标准对照；RAGAS 中文需定制提示词与模型。

</details>

---

## L4 场景（医疗保险实战）

### Q11. 场景：设计一个"特药理赔"多跳 Case（投保人同时有重疾险和百万医疗，确诊肺癌用奥希替尼），写出字段与评测执行。
- **考察点**：综合 Case 设计 + 分层评测执行。
- **评分维度**：字段完整；检索层看 3 chunk 进 topK；生成层看 key_points/忠实/引用。
<details>
<summary>参考答案</summary>

字段示例：case_id=C0012；question="投保人有重疾险和百万医疗，确诊肺癌用奥希替尼，两份各自报多少、有先后吗？"；question_type=multi_hop；expected_document_ids=[doc_重疾条款,doc_百万医疗条款,doc_补偿关系]；expected_chunk_ids=[chunk_重疾_肺癌给付,chunk_医疗_靶向报销,chunk_补偿_顺序]；answer_key_points=["重疾按保额给付","医疗报销靶向药80%","医疗补偿重疾已付","无重复超额"]；should_refuse=false；allowed_roles=[policy_holder,agent]；document_version=v2026.07。
评测执行：检索层看 3 个 chunk 是否进 topK（Recall/Hit）；生成层看答案是否覆盖 4 个 key_points（Relevancy/Correctness）、是否忠实（Faithfulness）、引用是否指向正确 chunk（引用准确率）。

</details>

### Q12. 场景：你的拒答准确率召回率只有 0.6（该拒的 40% 没拒，硬编了）。请定位原因并给出修复。
- **考察点**：拒答失败的根因分析与修复路径。
- **评分维度**：能否定位"检索阈值低 + 无 should_refuse 约束 + 越权未拦截"；修复是否对应。
<details>
<summary>参考答案</summary>

原因：①检索分数阈值过低，低相关 chunk 被当依据硬答；②生成 prompt 无"无依据就 should_refuse"约束，模型倾向硬编讨好用户；③allowed_roles 越权未拦截（无权限角色也答了）。修复：用评测集提高检索分数阈值让"无依据"触发拒答；prompt + 结构化 should_refuse 字段双保险；加 allowed_roles 校验，越权直接拒；把修复后重新在 should_refuse=true 子集回归，盯召回率回升且不牺牲精确率。

</details>

### Q13. 场景：老板要"评测报告"，你会怎么组织，才能让他相信 RAG 系统可靠、且知道下一步改什么？
- **考察点**：评测汇报与迭代闭环能力。
- **评分维度**：报告结构（概览/分层/细分/bad case/结论）；是否体现迭代闭环与 bad case 回流。
<details>
<summary>参考答案</summary>

报告结构：①概览（50 条规模、分布、document_version、对比基线）；②分层指标（检索 Hit/Recall/Precision/MRR/nDCG + 生成 Faith/Relevancy/引用/拒答，配趋势）；③分类型细分（事实/多跳/拒答/模糊各子集，定位短板）；④典型 bad case（3~5 个失败样本 + 根因：检索漏/幻觉/误拒）；⑤结论与 action（本轮改了什么、指标涨跌、下一步）。迭代闭环：跑分→看短板子集→改对应环节→回归对比→把 bad case 补回测试集，让集子越测越硬，证明"可靠且持续改善"。

</details>

---

## 评分汇总表

| 题号 | 层级 | 主题 | 满分 | 候选得分 | 备注 |
|------|------|------|------|----------|------|
| Q1 | L1 | 分层评测必要性 | 5 | | |
| Q2 | L1 | 三检索指标区别 | 5 | | |
| Q3 | L1 | 测试集建设策略 | 5 | | |
| Q4 | L2 | MRR / nDCG | 5 | | |
| Q5 | L2 | Faithfulness vs Relevancy | 5 | | |
| Q6 | L2 | 引用准确率三维度 | 5 | | |
| Q7 | L2 | RAGAS vs LangSmith | 5 | | |
| Q8 | L3 | Case 完整字段 | 5 | | |
| Q9 | L3 | 拒答准确率测法 | 5 | | |
| Q10 | L3 | LLM 裁判偏差 | 5 | | |
| Q11 | L4 | 特药多跳 Case | 5 | | |
| Q12 | L4 | 拒答召回低修复 | 5 | | |
| Q13 | L4 | 评测报告汇报 | 5 | | |
| **合计** | | | **65** | | |

### 达标线
- **L1+L2 全对（基础达标）**：能讲清检索类与生成类核心指标分别衡量什么，能解释测试集 50 条构成与三个检索指标差异。
- **L3 两题以上达标（进阶达标）**：能完整设计一个 Case 的 9 字段并说明各字段用途；能正确描述拒答准确率（P/R/F1）与"一律拒答"陷阱；了解 LLM 裁判偏差。
- **L4 两题以上达标（实战达标 / 面试通过线）**：能结合医疗保险给出完整多跳 Case 与评测执行；能定位并修复拒答召回低；能组织可信的评测报告与迭代闭环。
- **总分建议**：≥52/65（80%）为"熟练"；40~52 为"基本掌握需补强"；<40 为"不达标，重学手册"。

---

> 配套学习手册：`FDE-W3D5-RAG评测-学习手册.html`
