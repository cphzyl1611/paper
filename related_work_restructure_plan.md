# Related Work 重构方案与讨论成果

## 1. 讨论最终解决了什么

这次讨论的核心成果不是简单地“降低 AI 味”，而是把论文中容易混在一起的四组概念正式分开。

### 1.1 Structural applicability 与 empirical defense performance

- **Structural applicability**：由 `Defense-Tech Crosswalk` 或 `Defense Opportunity Map` 推导。它表示某类控制在理论上与哪些 action 有关，不表示某个真实系统一定能成功检测或阻断。
- **Empirical defense performance**：由真实实验中的 `PREVENTED / BLOCKED / DETECTED / MISSED` 观察结果计算。

后续写作必须为每个百分比说明分母，禁止在只有结构映射时使用无修饰的 `covers`、`defends against` 或 `handles`。

### 1.2 完整框架与 Mininet 实证子集

论文保留完整的 53 个 playbook 和 1,758 个 action，用于分类体系、映射、评分边界和参数稳健性验证。真实实验则只选择 Mininet 能合理重放并产生网络证据的 action，例如扫描、网络服务利用、C2、工具传输、网络横向移动、隧道和外传。

因此：

- framework scope：完整控制点域；
- benchmark scope：完整 playbook/action 集；
- empirical scope：network-replayable subset。

Mininet 是完整框架的一个网络实证切片，不是对全部端点、身份和本地运行时行为的实验覆盖。

### 1.3 非网络前置动作的处理

后续 Methodology 应新增：

```text
evaluation_status ∈ {
  scored,
  assumed_precondition,
  out_of_scope,
  execution_failed
}

subset_stage_semantics ∈ {
  preserved,
  projected,
  resegmented
}
```

只有 `scored` action 进入真实实验分数。前置动作可作为 `assumed_precondition` 保留上下文，但不能记为 `MISSED`。筛选改变原阶段语义时，输出必须称为 `network-projected stage score` 或 `resegmented network-path score`。

### 1.4 Related Work 从“生成文本”改为“证据驱动”

导师要求的 25 篇是指至少 25 篇来自 IEEE S&P、USENIX Security、ACM CCS、NDSS、IEEE TDSC、IEEE TIFS 的独立论文。ATT&CK、D3FEND、ATT&CK Evaluations、CVSS、NIST 和行业报告可继续引用，但不计入 25 篇。

本轮建立了 37 篇合格论文的核验池。每篇论文都记录：

- 官方题名、作者、venue 和来源；
- 研究问题、输入、方法和输出；
- 与本文相关的范围边界；
- Related Work 可以安全写出的断言；
- 技术分组依据；
- Mininet 可复现性；
- `retain / correct / add / replace / remove` 处理结论。

这避免了“七篇论文共用一个模糊谓语”的 citation dumping。

## 2. 已发现的仓库问题

提交 `76b21be` 中至少存在四项 BibTeX 元数据问题：

1. `zhang2025tapas` 的题名错误；
2. `yang2023prographer` 的作者列表与 USENIX 官方记录不一致；
3. `rehman2024flash` 的作者列表与 IEEE S&P 官方程序不一致；
4. `jia2024magic` 的作者列表包含了 USENIX 官方记录中不存在的作者。

这些问题应由附带的 BibTeX patch 修复。

## 3. 四个小节的最终方案

### 2.1 Attack Knowledge Bases and Adversary-Emulation Evaluation

**目标**：说明 benchmark 的攻击描述、能力映射和步骤级观察分别来自哪里。

段落顺序：

1. ATT&CK 提供攻击行为语言；ATT&CK Evaluations 提供步骤级产品观察。
2. D3FEND 是防御能力知识空间：回答“哪些防御可能相关”，不是“实际哪一个控制首先失守”。
3. iACE、ATTACK2VEC、Reading the Tea Leaves 说明 CTI 的抽取、演化和来源差异。
4. POIROT 与 ATLAS 说明攻击描述如何和系统证据对齐/重建。
5. 收束：这些工作提供 benchmark 输入，但没有定义 control-failure attribution 和 stage-aware scoring。

### 2.2 APT Detection, Correlation, and Attack Investigation

**目标**：按技术路线比较检测系统，而不是按年份列论文。

段落顺序：

1. SLEUTH 与 HOLMES：标签传播、规则/TTP 驱动的攻击重建与关联。
2. NoDoze 与 OmegaLog：告警压缩、调查支持和多层日志融合。
3. DeepLog 与 Log2Vec：序列日志和异构日志图学习。
4. UNICORN、Tactical Provenance、threaTrace：运行时 provenance、行为抽象和节点级检测。
5. DISTDET：分布式企业检测；PROGRAPHER、NODLINK、TAPAS：图规模、在线处理和细粒度输出。
6. MAGIC、KAIROS、FLASH：不同的图表示学习；R-CAID：root-cause-aware detection。
7. 收束：这些系统输出 alert、subgraph、attack story 或 causal root；本文的输出是 action outcome、control attribution 和可追溯 score。

### 2.3 Network Enforcement and Defense-System Evaluation

**目标**：为 Mininet 网络实证切片建立直接相关的研究基础，但不把整篇论文改写成网络 IDS 论文。

段落顺序：

1. SANE 与 FIREMAN：网络架构执行和策略分析。
2. AVANT-GUARD 与 SPHINX：SDN 中的主动执行和状态一致性检测。
3. DELTA：自动化 SDN 攻击重放与安全评估。
4. Poseidon、Jaqen、Ripple、SmartCookie：可编程数据平面中的不同 DDoS 检测/阻断机制，分别说明平台和输出差异。
5. 收束：这些系统能产生真实 `DETECTED/BLOCKED` 证据；本文增加跨 action、stage、playbook 的统一评分。明确 Mininet 只覆盖网络可执行子集。

### 2.4 Vulnerability, Attack-Graph, and Defense-Effectiveness Metrics

**目标**：比较不同量化方法的评价单元和证据基础。

段落顺序：

1. CVSS：vulnerability-level severity。
2. Sheyner attack graph、MulVAL 和 scalable attack graph：configuration/path reachability。
3. k-zero-day safety 和 network diversity：结构性 resilience。
4. SoK：安全度量需要显式假设和证据边界。
5. 收束：这些是 prospective structural metrics；本文同时报告 structural applicability 与 replay-derived empirical performance。

## 4. 结尾综合比较

不再重复 Introduction，而是用以下四个比较轴：

| Research line | Input | Unit | Output | Remediation traceability |
|---|---|---|---|---|
| Knowledge bases/emulation | technique descriptions, emulation logs | technique/step | vocabulary/observation | partial |
| Detection/investigation | telemetry/provenance | event/entity/subgraph | alert/story/root cause | attack-oriented |
| Network enforcement | policy/traffic/state | rule/flow/attack instance | allow/block/detect | mechanism-specific |
| Risk/attack graphs | vulnerabilities/configuration | vulnerability/path | severity/reachability | configuration-oriented |
| This work | playbook action + observation | action–outcome–control tuple | stage-aware score | explicit first-failed control |

## 5. 写作检查表

- 每句话最多通常引用 2–3 篇论文；
- 每个引用支持一个具体断言；
- 最接近本文的工作必须单独解释；
- 不从二手综述推断核心方法；
- 不把论文输出统称为“detection”；
- 不虚构局限；优先描述研究目标和输出边界；
- 不把 structural applicability 写成 empirical performance；
- 所有百分比说明分母；
- 标准和网站不计入 25 篇；
- BibTeX 与官方页面逐条一致。
