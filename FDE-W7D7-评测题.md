# FDE W7D7 评测题 · K8s 基础拓扑、灰度回滚与企业平台整合

> 配套手册：《FDE-W7D7-K8s基础拓扑灰度回滚与企业平台整合-学习手册.html》
> 候选人背景：36 岁，Java + 大数据 + 医疗保险，偏企业 AI 平台与部署交付。
> 题型：L1 基础 / L2 进阶 / L3 深度 / L4 场景，共 13 题。
> 评分：每题满分 5 分（4 档：0 / 2 / 3.5 / 5），折叠区为参考答案，先自答再展开。

---

## L1 基础（概念清晰、能准确表述）

### Q1. K8s 三大核心对象（Deployment / Service / Ingress）各自做什么？在 AI 平台怎么对应？
- **考察点**：核心对象语义与 AI 场景映射。
- **评分维度**：① Deployment 管副本版本；② Service 稳定地址+负载均衡；③ Ingress 七层外部路由；④ 分别对应业务/网关、内部互访、外部入口。
<details>
<summary>参考答案（点击展开）</summary>

Deployment 管理无状态应用的副本与版本（Spring AI 业务、LiteLLM 网关多副本）；Service 给一组 Pod 稳定虚拟 IP+DNS 并负载均衡，屏蔽 Pod IP 变化（内部业务↔网关互访）；Ingress 是七层 HTTP 入口，按域名/路径路由外部流量到 Service（网关/管理界面）。三者构成生产级 AI 平台部署骨架。

</details>

### Q2. 为什么需要 Service？ClusterIP 和外部访问有什么关系？
- **考察点**：Service 存在意义、类型边界。
- **评分维度**：① Pod IP 会变，Service 提供稳定地址；② ClusterIP 仅集群内；③ 外部需 Ingress/LoadBalancer。
<details>
<summary>参考答案（点击展开）</summary>

Pod 会漂、会重建、IP 变，Service 给稳定虚拟 IP+DNS，自动负载均衡到健康 Pod，调用方只认 Service 名。ClusterIP 只在集群内可达，外部访问必须靠 Ingress（七层路由）或 LoadBalancer。Spring AI 的 base-url 在 K8s 应写 Service 名（如 `http://litellm-gateway:4000/v1`），网关扩缩容业务无感。

</details>

### Q3. 滚动更新（Rolling Update）是怎么做到不中断的？关键参数有哪些？
- **考察点**：滚动更新原理与参数。
- **评分维度**：① 逐批换 Pod、新旧并存；② maxSurge/maxUnavailable 控节奏；③ maxUnavailable=0 零停机 + Readiness 配合。
<details>
<summary>参考答案（点击展开）</summary>

滚动更新逐批用新 Pod 替换旧 Pod，期间新旧并存，服务不中断。关键参数：`maxSurge`（最多比期望多起几个新 Pod）、`maxUnavailable`（更新期间最多允许几个旧 Pod 下线，网关设 0 保零停机）、`minReadySeconds`（新 Pod 就绪后等待再继续）。必须配合 Readiness 探针，新 Pod 真就绪才接流量。

</details>

### Q4. 回滚（Rollback）在 K8s 里是怎么实现的？有什么前提？
- **考察点**：revision 机制与回滚条件。
- **评分维度**：① Deployment 有 revision 历史；② kubectl rollout undo 一键回退；③ 旧镜像保留、状态兼容、监控及时发现。
<details>
<summary>参考答案（点击展开）</summary>

Deployment 每次更新生成 revision，`kubectl rollout undo deployment/xxx` 回滚到上一稳定 revision，K8s 自动换回旧镜像 Pod。前提：旧镜像/旧 revision 保留（设 revisionHistoryLimit）、数据库状态兼容（不兼容迁移需策略）、有监控告警能及时发现问题。AI 平台还要注意模型/Prompt 版本一并回退。

</details>

---

## L2 进阶（能结合企业/Java 平台场景分析）

### Q5. AI 服务（业务/网关/模型/数据库）在 K8s 分别用什么对象部署？GPU 怎么调度？
- **考察点**：不同负载类型的 K8s 映射与 GPU 调度。
- **评分维度**：① 业务/网关 Deployment 多副本无状态；② 模型 Deployment/StatefulSet 申 GPU；③ 数据库 StatefulSet+PVC；④ GPU 用 nodeSelector/toleration + device plugin。
<details>
<summary>参考答案（点击展开）</summary>

Spring AI 业务与 LiteLLM 网关用 Deployment 多副本无状态；模型推理用 Deployment/StatefulSet 申请 GPU 资源（limits.nvidia.com/gpu），节点需装 device plugin，用 nodeSelector/toleration 调度到 GPU 节点，权重走 PVC/对象存储不进镜像；Postgres 治理库用 StatefulSet + PVC 持久化。多数保险交付先接外部厂商模型，不自管 GPU 集群。

</details>

### Q6. Ingress 和 Service 是什么关系？金融企业常给它配什么安全能力？
- **考察点**：Ingress/Service 协作与安全边界。
- **评分维度**：① Ingress 七层路由、Service 四层转发；② 外部只暴露 Ingress；③ TLS/WAF/限流；呼应环境隔离（不同环境不同域名/ns）。
<details>
<summary>参考答案（点击展开）</summary>

Ingress 是"大门+路径指示牌"（七层，按域名/路径路由），Service 是"房间号"（四层，IP:端口负载均衡）。外部请求先到 Ingress，再转内部 Service 到 Pod；外部只暴露 Ingress，内部 Service 不对外。金融客户常配 TLS（HTTPS）、WAF、限流作为安全边界，并借 namespace/不同域名实现环境隔离（呼应 D6）。

</details>

### Q7. 金丝雀发布（Canary）适合什么场景？在 K8s 有哪几种实现方式？
- **考察点**：金丝雀适用性与实现。
- **评分维度**：① 小步验证、看系统+业务指标；② 多 Deployment+权重、Istio、Ingress canary 注解；③ 模型升级风险高必用。
<details>
<summary>参考答案（点击展开）</summary>

金丝雀先放 5%~10% 流量到新版本，观察错误率/延迟/质量正常再放量，比滚动更稳，适合模型/网关升级（新模型效果未知、新网关配置可能错）。K8s 实现：多 Deployment + Service 权重分流、Service Mesh（Istio VirtualService 配比例）、或 Ingress Controller 的 canary 注解。注意不仅要看系统指标，还要看业务/质量指标（避免"HTTP 200 但答非所问"的坏版本）。

</details>

### Q8. 为什么 AI 平台回滚时要"模型/Prompt 版本一并回退"？只回代码会怎样？
- **考察点**：AI 特有的版本一致性。
- **评分维度**：① 代码与模型/Prompt 是耦合的整体；② 只回代码不回模型 = 半吊子状态、行为不一致；③ 需版本绑定管理。
<details>
<summary>参考答案（点击展开）</summary>

AI 平台的行为 = 代码 + 模型 + Prompt 共同决定。只回滚业务/网关代码、不回退模型或 Prompt，会出现"代码旧、模型新"的半吊子状态，行为不一致、难排查。应把模型版本、Prompt 版本与代码版本一起纳入发布与回滚管理（如配置中心记录版本绑定），保证回退是整体一致回退。

</details>

---

## L3 深度（能辨析细节、能设计）

### Q9. 滚动更新里 maxUnavailable 设成 0 有什么好处和代价？为什么网关尤其要设 0？
- **考察点**：参数权衡与咽喉组件考量。
- **评分维度**：① 好处：零停机、容量不降；② 代价：需额外资源容纳新旧并存；③ 网关是咽喉，downtime 全瘫。
<details>
<summary>参考答案（点击展开）</summary>

maxUnavailable=0 保证更新期间旧 Pod 不低于期望副本数，零停机、容量不降。代价：新旧并存期间需要额外资源（maxSurge 允许多起的 Pod）。网关是所有 AI 流量的咽喉，一旦容量下降或中断，全公司 AI 能力受影响，所以尤其要设 0 + 配合 Readiness 确保新 Pod 真就绪才接流量。

</details>

### Q10. 如果新版本改了数据库 schema 且不向后兼容，直接回滚会发生什么？怎么规避？
- **考察点**：回滚与数据迁移的兼容性。
- **评分维度**：① 旧代码读不了新 schema 数据，可能崩；② 需兼容迁移（expand/contract）、双写、或回滚连带数据；③ 发布前评估。
<details>
<summary>参考答案（点击展开）</summary>

若新版本改了不兼容 schema，直接回滚到旧代码，旧代码按旧结构读写，可能解析失败/报错，服务仍不可用。规避：用"可向后兼容的迁移"策略（expand/contract：先加字段不改旧、再切、后删），或发布前保证迁移可逆；必要时回滚要连带数据回退（有备份/补偿脚本）。发布前必须评估"回滚是否安全"。

</details>

### Q11. 可观测三件套（Logs/Metrics/Traces）在 AI 平台怎么支撑排障与成本？
- **考察点**：可观测体系与成本闭环。
- **评分维度**：① Logs(ELK/Loki) 查错误；② Metrics(Prometheus+Grafana) 看 QPS/延迟/错误率/成本；③ Traces(Jaeger/OTel) 跨层调用链；④ 成本由网关落库→看板。
<details>
<summary>参考答案（点击展开）</summary>

Logs（Loki/ELK）查各层错误日志；Metrics（Prometheus+Grafana）看 QPS/延迟/错误率/预算水位；Traces（Jaeger/OpenTelemetry）串联跨层调用链（Ingress→业务→网关→模型），断链即难排障。成本闭环：网关（LiteLLM）落库的成本数据→Prometheus/BI 抓取→Grafana 看板，可视化"各业务线/项目花多少"+预算告警。数据源必须以网关为准（呼应 D6）。

</details>

---

## L4 场景（企业 Agent 平台 / 保险实例综合应用）

### Q12. 某保险公司要上线"企业 Agent 平台最小版"，请画出 K8s 组件清单与端到端调用链，并说明发布策略。
- **考察点**：W7 整体整合——组件映射 + 调用链 + 发布能力。
- **评分维度**：① 组件清单映射到 K8s 对象（业务/网关 Deployment、Postgres StatefulSet、模型、Ingress、Secret/探针）；② 调用链 Ingress→业务→网关(虚拟Key)→模型，成本落库、Trace 串联；③ 发布用滚动/金丝雀/回滚。
<details>
<summary>参考答案（点击展开）</summary>

组件清单与 K8s 对象：Spring AI 业务=Deployment+Service；LiteLLM 网关=Deployment+Service（多副本高可用）；Postgres 治理库=StatefulSet+PVC；模型=外部厂商 API 或 Deployment(GPU)；Ingress=外部入口+TLS；Secret/探针/隔离=交付件。端到端调用链：用户→Ingress→Spring AI 业务→(Service)→LiteLLM 网关→(虚拟 Key)→模型→原路返回；网关落库记成本，Trace id 贯穿各层。发布策略：网关与业务各自 Deployment 独立滚动更新（maxUnavailable=0）/金丝雀（先 5% 看质量）/一键回滚（revision + 旧镜像保留 + 模型 Prompt 一并回退）。

</details>

### Q13. 场景：保险客户要求"模型升级不能影响线上理赔业务，出问题 5 分钟内回退"。请设计发布与回滚方案，并指出哪些属于 D5/D6/D7 的产出
- **考察点**：约束下的发布设计 + W7 跨天整合归因。
- **评分维度**：① 金丝雀（5% 流量 + 质量评估）再放量；② 一键回滚（revision/旧镜像/监控告警/模型 Prompt 同退）；③ 明确对应 D5(网关/ADR)、D6(单一调用链/Secret/探针)、D7(K8s 拓扑/灰度回滚)。
<details>
<summary>参考答案（点击展开）</summary>

方案：
① 发布用金丝雀——先放 5% 真实理赔流量到新模型版本，对比错误率与人工抽检质量，确认不劣化再逐步放量到 100%，避免全量盲更影响理赔结论。
② 回滚用 K8s revision——`kubectl rollout undo` 一键回退，前提是旧镜像/revision 保留、监控告警能 5 分钟内发现劣化、模型版本与 Prompt 版本一并回退（避免半吊子）。
③ 发布过程零停机：网关滚动更新 maxUnavailable=0 + Readiness 配合；外部只暴露 Ingress + TLS。
跨天归因：D5 产出=选 LiteLLM 作网关、虚拟 Key/预算/多租户治理能力、ADR 留痕决策；D6 产出=单一调用链（路由/重试/成本各一层）、Docker 交付件（Secret 不落代码、双探针、环境隔离、降级兜底）；D7 产出=K8s 拓扑（Deployment/Service/Ingress）、灰度（滚动/金丝雀）与回滚能力、可观测与成本看板。整套串成"企业 Agent 平台最小版"。

</details>

---

## 评分汇总表

| 题号 | 层级 | 主题 | 满分 | 自评 | 考官评 |
|------|------|------|------|------|--------|
| Q1 | L1 | K8s 三核心对象 | 5 | | |
| Q2 | L1 | Service 与访问 | 5 | | |
| Q3 | L1 | 滚动更新原理 | 5 | | |
| Q4 | L1 | 回滚机制 | 5 | | |
| Q5 | L2 | AI 负载部署+GPU | 5 | | |
| Q6 | L2 | Ingress/Service+安全 | 5 | | |
| Q7 | L2 | 金丝雀实现 | 5 | | |
| Q8 | L2 | 模型/Prompt 同退 | 5 | | |
| Q9 | L3 | maxUnavailable 权衡 | 5 | | |
| Q10 | L3 | 回滚与 schema 兼容 | 5 | | |
| Q11 | L3 | 可观测三件套 | 5 | | |
| Q12 | L4 | 平台最小版整合 | 5 | | |
| Q13 | L4 | 约束发布+跨天归因 | 5 | | |

**计分**：每题 0 / 2 / 3.5 / 5 四档。总分 65。
- **达标线①（K8s 部署与灰度回滚）**：Q1–Q4、Q9 合计应 ≥ 21/25，能讲清拓扑 + 滚动/金丝雀/回滚。
- **达标线②（W7 整体整合）**：Q5、Q12、Q13 合计应 ≥ 14/15，能列出组件清单 + 调用链，并讲清 W7 三天如何串成"企业 Agent 平台最小版"。
- **整体达标**：总分 ≥ 50/65，且两条达标线同时达标，W7 周正式收官，可进入下一阶段（W8+）。