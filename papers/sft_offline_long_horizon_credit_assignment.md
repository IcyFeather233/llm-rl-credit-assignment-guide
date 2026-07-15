# 面向 Long-Horizon LLM Agent 的 SFT / Offline Credit Assignment

> 研究备忘录与方法方案。文献检索截至 **2026-07-15**。
> 范围：从一组**冻结的已记录轨迹**中学习更好的 agent policy，在 policy optimization 期间不进行环境交互。教师标注、参考答案和 replay 被视为独立设定，并在文中明确标注。

## 执行摘要

标准 agent SFT 会把 demonstration 中的每个 action token 都当作同等正确：

$$
\mathcal L_{\mathrm{SFT}}
=-\sum_i\sum_{t=1}^{T_i}\log \pi_\theta(a_{i,t}\mid h_{i,t}).
$$

这隐含了一个 credit assignment 规则：$w_{i,t}=1$。对长轨迹来说，这个规则尤其脆弱：成功轨迹中可能包含循环、缺乏证据的猜测、偶然的工具调用，或者很久之后才被修复的错误；失败轨迹中也可能包含正确计划、有用证据和若干已经完成的子目标。全轨迹 SFT 会强化前者，并丢弃或惩罚后者。

核心难点不是如何写出一个 weighted SFT loss，而是**可识别性**：从一个 logged action 和一个最终 outcome 出发，通常无法知道未被观察到的替代 action 会带来什么结果。因此，一个可信的 offline 方法不应为每一步伪造精确分数，而应该：

1. 只比较行为上相似、且确实存在 action overlap 的状态；
2. 通过证据和 memory provenance 表达延迟依赖；
3. 返回**区间形式的 credit estimate**，当符号不可识别时选择 abstain；
4. 将正向、负向和不确定片段编译为不同训练目标。

本文提出 **Conservative Provenance Credit Distillation (CPCD)**。其操作规则很简单：

> 只有当置信下界为正时才给予正向 SFT credit；只有当置信上界为负且存在被观察到的更好替代动作时，才构造负向 preference；否则保留该片段作为上下文，但 mask 掉 imitation loss。

这个方法的预期贡献不是声称恢复每个 logged action 的真实因果价值，而是一种保守、可审计的方式：把异质 long-horizon logs 转换为 selective SFT 和 offline preference data，并显式支持 support、confounding 与 falsification tests。

---

## 1. 问题、动机与挑战

### 1.1 这里的 “offline credit assignment” 指什么

设一条已记录的 agent trajectory 为

$$
\tau_i=(x_i,o_{i,1},a_{i,1},o_{i,2},a_{i,2},\ldots,o_{i,T_i},a_{i,T_i},y_i),
$$

其中 $x_i$ 是任务，$h_{i,t}$ 是 action $a_{i,t}$ 之前可见的 prefix，$y_i\in[0,1]$ 是 terminal verifier score。可用数据集为

$$
\mathcal D=\{(\tau_i,y_i,b_i)\}_{i=1}^{N},
$$

其中 $b_i$ 可选记录 behavior policy、checkpoint、sampling temperature 和 logged action probabilities。

目标训练目标是

$$
\mathcal L_{\mathrm{weighted\text{-}SFT}}
=-\sum_i\sum_t w_{i,t}\log \pi_\theta(a_{i,t}\mid h_{i,t}),
$$

也可以加入 preference losses。Credit assignment 的任务是决定 $w_{i,t}$、需要 mask 的 tokens，以及任何 preferred/dispreferred action pairs。

需要区分三种设定：

| 设定 | 优化期间的环境调用 | 额外模型生成或标签 | 例子 |
|---|---:|---:|---|
| **严格 fixed-log offline** | 无 | 可选，但没有新的环境转移 | ATLaS, Q-SFT, HPL, Agentic-DPO, SWE-Lego |
| **Offline curation / privileged annotation** | 无 | 强教师、参考解、patch 或 hindsight labeler | STeP, EEF, AgentHER, P2T, ACC |
| **Semi-online 或 replay-based** | 有，在每轮之前或期间 | 通常有 | IPR, CSO, HSL 的实验设定, SWE-TRACE |

本文提出的主设定是第一行。第二行是可选的更强监督版本，第三行只作为 upper bound。

### 1.2 为什么 long horizon 会让普通 SFT 更糟

#### 延迟效应

第 7 turn 收集到的 observation 可能支撑第 63 turn 的决策。turn distance 不是 relevance 的好 proxy，因此 recency mask 和 uniform discounting 会错过真实依赖。

#### 成功轨迹不是干净 demonstration

terminal success label 只说明整个交互最终成功了，并不证明每个 search query、file edit、memory update 或 reasoning step 都正确。后续恢复可能掩盖有害动作。

#### 失败轨迹不是全都坏

失败可能来自长正确 prefix 之后的一个 pivotal action。Outcome filtering 会丢掉有用探索和稀有子技能，而这些恰恰出现在 successful expert data 稀缺的困难任务中。

#### 部分可观测性造成 confounding

教师可能看到了 reference answer、隐藏 scratchpad、更完整的 browser state 或 repository metadata，而学生的 history 里没有这些信息。一个 action 可能因为这些隐藏信息而与成功相关，而不是因为它在学生可观测状态下有用。

#### 精确状态复现很罕见

两条 80-turn histories 几乎不可能 token-by-token 完全一致。语义状态抽象是比较的必要条件，但过粗的抽象又可能合并约束、memory 内容或环境副作用不同的状态。

#### 长轨迹可能主导优化器

如果没有 trajectory-level normalization，一条 200-turn trace 会比一条 20-turn trace 贡献多得多的 supervised tokens。这会把“更好的 credit”与“每个任务的 gradient mass 变化”混在一起。

#### Credit quality 很少被直接测量

多数工作只报告 downstream success。一个方法可能只是因为 regularization 或减少 tokens 而提升性能，即使它的 step scores 与 intervention-derived contribution 几乎无关。

### 1.3 为什么 offline approach 仍然值得做

Offline agent logs 的吸引力在于，昂贵的环境、浏览器、容器和专有工具在训练期间不需要保持在线。历史部署也包含丰富的失败、恢复和 behavior policies，而普通 expert-only SFT 会忽视这些信息。工具 metadata、source URLs、file paths、test results、memory source indices 和 timestamps 提供了普通 reasoning transcript 中没有的结构信号。

因此机会不是“从一条轨迹中推断一切”，而是组合：

- 来自多 policy 或 stochastic rollouts 的 **cross-trajectory variation**；
- 记录哪些证据后来被使用的 **prefix-grounded provenance**；
- 工具 schema、测试和 state diffs 等 **local verifiers**；
- 将缺少 support 变成 abstention、而不是猜测标签的 **conservative uncertainty estimates**。

这可以降低 online RL 的成本和不稳定性，同时产出一个 SFT-compatible artifact，并且可以在训练前人工检查。

### 1.4 无法绕过的可识别性限制

假设所有到达抽象状态 $c$ 的 logged trajectories 都采取 action $u$ 并成功。观测数据同时兼容两个世界：

- 所有替代 action 都会失败，因此 $u$ 是 pivotal；
- 某个替代 action 也会成功，因此 $u$ 并非必要。

没有任何 estimator 能仅从 fixed logs 中区分这两个世界。此时 point credit score 是 modeling assumption，而不是 identified causal effect。

因此 CPCD 要求在可比较状态中存在 repeated action variation，否则返回 vacuous interval。其因果解释依赖以下假设：

1. **consistency:** 抽象 action 对应稳定 intervention；
2. **overlap:** 合理替代动作以非忽略概率出现；
3. **sequential ignorability:** 记录的 prefix representation 包含 action 和 outcome 的重要 common causes，允许显式 sensitivity allowance；
4. **stable continuation:** 在被比较 segment 之后，value 定义在 logged continuation-policy mixture 下。

如果缺少这些假设，输出应称为 **observational utility credit**，而不是 causal credit。

### 1.5 最小可用数据契约

| 字段 | 状态 | 为什么重要 |
|---|---|---|
| Task ID 与 task-family ID | 必需 | 防止 train/test leakage，并支持 task-clustered uncertainty |
| 有序 actions 与 observations | 必需 | 定义 prefixes 和延迟依赖 |
| Terminal verifier outcome | 必需 | 提供需要归因的最终 utility |
| 每个任务或 support cell 的多条 trajectories | signed credit 必需 | 提供 action overlap；单条 expert trace 只能支持 masking heuristic |
| Tool-call IDs、result IDs、file/URL/test metadata | 强烈推荐 | 支持 deterministic provenance edges |
| Behavior policy/checkpoint/temperature | 强烈推荐 | 让 propensity modeling 和 policy-mixture analysis 更可信 |
| Logged action probabilities | 推荐 | 降低 propensity-model error；若 action classes 是离散的，则不是必需 |
| 可恢复 state snapshots | 仅用于评估 | 支持 intervention-derived credit labels，而不把训练变成 online |
| Reference answer 或 patch | 可选 privileged variant | 对比较有用，但排除在 primary fixed-log estimator 之外 |

---

## 2. 相关工作与可复用思想

### 2.1 直接相关的 SFT 与 offline trajectory 方法

| 工作 | 设定 | Credit 或 curation 信号 | 训练目标 | 贡献 | 对本项目的边界 |
|---|---|---|---|---|---|
| [AWR](https://arxiv.org/abs/1910.00177) (2019) | Offline RL 基础 | Learned advantage | Advantage-weighted behavior cloning | 经典 weighted maximum-likelihood 视角 | 标量 value estimates 可能在 support 外 extrapolate；不是 LLM-agent 方法 |
| [Q-SFT](https://arxiv.org/abs/2411.05193) (2024) | 静态 transition/reward data | 编码在 token probability 中的 Bellman/Q target | 无独立 value head 的 modified SFT | 说明 offline value learning 可保持 SFT-like objective | 需要 transition rewards，并继承 offline Q-learning 假设 |
| [IPR / Watch Every Step](https://arxiv.org/abs/2406.11176) (2024) | Semi-online | 从 expert prefixes 出发的 Monte Carlo continuations | SFT 加 step/outcome preferences | 直接的 intervention-style expert-step value estimate | 中间环境 rollout 昂贵，且违反 strict offline training |
| [ATLaS](https://arxiv.org/abs/2503.02197) (2025) | Fixed trajectories 加 LLM selector | planning、关键 observations/actions、self-correction 的二值 critical-step labels | 完整 context，只在 selected steps 上计算 loss | 清楚地区分“作为 context 可见”和“作为 target 学习” | 重要性由 judge 判断，不来自 counterfactual outcomes；hard mask 无 uncertainty |
| [EEF](https://arxiv.org/abs/2504.13145) (2025) | Offline data curation | 从 failed expert traces 中挖掘 beneficial plans/actions | 把有用失败片段加入 fine-tuning | 证明 failed trajectories 不应整体丢弃 | Credit 依赖 failure-analysis heuristic，且未校准 |
| [STeP](https://arxiv.org/abs/2505.20023) (2025) | Teacher-generated offline traces | Error、reflection 与 correction labels | 错误保留在 context 中，但 mask 其 loss | 展示 self-correction traces 中的 target/context separation | 依赖 synthetic reflection quality；主要是显式 error masking |
| [Reward-Weighted Fine-Tuning](https://arxiv.org/abs/2506.06964) (2025) | Offline outcome-labeled traces | 单个 trajectory reward | Reward-weighted SFT | 强 trajectory-level offline baseline | 把同一个 reward 广播给每个 action，不能解决 within-trace credit |
| [HPL](https://arxiv.org/abs/2510.03253) (2025) | Offline preference learning | Trajectory-, action-group-, step-level preferences | 带 curriculum 的 hierarchical DPO | 有用的多尺度 segmentation 和 preference formulation | 需要 contrasting groups，且没有 support-aware abstention |
| [DML-IL](https://arxiv.org/abs/2502.07656) (2025) | Causal imitation-learning 基础 | Conditional moment restrictions；histories as instruments | History-dependent imitation policy | 明确建模 hidden confounding 和 trajectory history | 在 classical control 中验证，不是 language-agent traces；instrumental assumptions 仍然很强 |
| [SWE-Lego](https://arxiv.org/abs/2601.01426) (2026) | SFT-only SWE training | 显式 tool-error masking 与 difficulty curriculum | 在 validated trajectories 上做 masked SFT | 直接的 long-horizon SFT baseline，含 18K validated traces 和 turn-length curriculum | Tool errors 只捕捉可见失败，不捕捉语义 detours 或延迟证据误用 |
| [InT](https://arxiv.org/abs/2601.14209) (2026) | On-policy reasoning traces 加 references | First-error localization 与 one-step corrective intervention | 正确 prefix 加 correction 的 SFT，然后 RL | 精确 error-boundary supervision | 仅 reasoning、依赖 reference，且不是 fixed-log agent training |
| [AgentHER](https://arxiv.org/abs/2603.21357) (2026) | Offline hindsight relabeling | Failure type、actually achieved outcome、confidence gate | Relabeled SFT/DPO examples | 把部分失败转换为 goal-conditioned successes | 改变 task label，而不是估计对原始目标的贡献 |
| [P2T](https://arxiv.org/abs/2605.21996) (2026) | Privileged SWE curation | 从 reference patch 到 latent process graph；grounded progress 与 length | 在 shortest effective segments 上做 SFT | 对真实 SWE horizon 最接近的 provenance/process-graph 启发 | 需要 reference patch、可执行测试、teacher continuations 和 LLM judges |
| [ACC](https://arxiv.org/abs/2605.21850) (2026) | Offline trajectory compilation | 远距离 tool observations 变成 long-context QA evidence | Direct-answer SFT | 展示 logged evidence 如何监督长程 integration | 训练 context reasoning，而非 action-level utility credit |
| [HSL / Spinning Straw into Gold](https://arxiv.org/abs/2607.04235) (2026) | Hindsight relabeling；实验中使用 iterative rollouts | Achieved goals、irrelevant-action mask、sample weight | 在 relabeled goals 上做 SFT 或 DPO | 利用 unintended successes，并报告 long horizons 上更大收益 | 实验 pipeline 会收集新 trajectories 并优化 relabeled goals，因此本身不是 strict fixed-log 解法 |
| [Agentic-DPO](https://arxiv.org/abs/2607.10601) (2026) | 优化期间无环境交互 | 每个 expert state 上的 expert action vs one-step student negative | DPO 加 SFT anchor 与 policy-preserving augmentation | 最近最接近的 strict-offline state-conditioned action baseline；在 tau-bench retail 与 Mind2Web 上测试 | 默认每个 expert action 都是 preferred；不覆盖 student-only states 和有用 failure segments |

### 2.2 应影响方法或评估的相邻工作

| 工作 | 可复用思想 | 对本方案的影响 |
|---|---|---|
| [ECHO](https://arxiv.org/abs/2606.31650) | Source-indexed memory 与 credit routing 到 evidence turns 和 selection actions | 启发 provenance graph；但 CPCD 从 fixed logs 中估计 utility，并且可以 abstain，而不是只路由正向 terminal credit |
| [CSO](https://arxiv.org/abs/2602.03412) | 识别 critical steps、生成 expert alternatives、从这些状态 branch、验证 outcomes | 作为 semi-online upper bound，也作为 evaluation-only intervention labels 的方案 |
| [SWE-TRACE](https://arxiv.org/abs/2604.14820) | Rubric process reward 与 long-token SWE evaluation | 提供真实 process-judge baseline 和 horizon-stratified evaluation |
| [HORIZON](https://arxiv.org/abs/2604.11978) | 对 3,100+ multi-domain trajectories 做 failure attribution | 启发直接 credit-quality 与 root-cause metrics，而不只报告 success |
| [OpenResearcher](https://arxiv.org/abs/2603.20278) | 含 100+ tool calls 长尾的大规模 offline deep-research corpus | 候选数据源和真正 long-horizon stress test |
| [CFT](https://arxiv.org/abs/2510.10974) 与 [DFT](https://arxiv.org/abs/2508.05629) | Selective 或 dynamically reweighted token SFT | 有用的 optimization controls，但 token salience 不等于延迟 action contribution |
| [CurateEvo](https://arxiv.org/abs/2607.06140) | 用 held-out failures 驱动 iterative data-curation programs | 有用的 system-level baseline；它优化 curator，而不是在冻结 logs 中识别 step effects |

### 2.3 仍然缺失什么

直接相关文献已经覆盖 hard step selection、显式 error masking、failure mining、Q/advantage weighting、hindsight goal relabeling 和 state-conditioned preferences。剩余空白是把以下几项结合起来：

1. **严格 offline、long-horizon agent traces**，而不是短 reasoning responses；
2. **failure 与 recovery-aware signed credit**，而不是假设每个 expert action 都是正向；
3. 表达跨很多 turns 依赖的 **semantic provenance**；
4. **support-aware uncertainty and abstention**，而不是为每一步给 scalar score；
5. 使用 held-out interventions 的**直接 causal-credit evaluation**。

### 2.4 Closest-work 对比

| 维度 | ATLaS | Agentic-DPO | P2T | DML-IL | Proposed CPCD |
|---|---|---|---|---|---|
| 主要监督 | LLM criticality label | Expert vs sampled action | Reference-patch process graph | 来自 demonstrations 的 conditional moments | Outcome variation 加 logged provenance |
| 使用 failed traces | 不是核心 | 否 | Teacher failures 可用于 curation | 一般 demonstrations | 是，若存在 matched support |
| Negative credit | Mask unselected steps | 对 sampled negative 做 preference | 移除低效/不 grounded 片段 | 通过 policy estimation 隐式体现 | 只有 interval 严格为负且存在正向 logged alternative 时使用 |
| 长延迟表达 | 完整 text context | Expert state prefix | Privileged process graph | History-dependent policy | Source-to-use provenance DAG |
| 无 support 时如何处理 | 无显式机制 | sample 一个 plausible negative | Judge/privileged score | 依赖 identification assumptions | 返回 vacuous interval 并 mask loss |
| Causal-credit metric | 无 | 无 | Progress/grounding analyses | Imitation gap | Intervention sign、risk-coverage 与 calibration |

这个对比定义了 novelty boundary。CPCD 不应声称 selective SFT、process graphs、doubly robust estimation 或 DPO 本身是新的。

---

## 3. Proposed method: Conservative Provenance Credit Distillation

### 3.1 Research question 与 hypotheses

**研究问题。** 一个冻结的、异质的 long-horizon agent logs 集合能否支持可靠的 signed segment credit？在不增加环境交互的前提下，保守使用这种 credit 能否优于 full SFT 和 offline preference baselines？

预注册假设：

- **H1, selective utility:** 在相同 supervised-token budget 下，certified-positive segments 比 full SFT 和 random masks 带来更高 Success@1。
- **H2, long-delay structure:** source-to-use distance 和总 trajectory length 越大，provenance 对 credit precision 与 task performance 的帮助越明显。
- **H3, conservatism:** interval abstention 相比 point estimates 能降低 false-sign credit，并改善 risk-coverage curve。
- **H4, failure reuse:** 从 failed trajectories 中恢复的 positive segments 能在 hard/OOD tasks 上提供超出 success-only training 的收益。

### 3.2 一个核心原则

该方法是一个从 frozen logs 到 training targets 的 compiler：

```text
frozen trajectories
        |
        v
typed segments + prefix-grounded provenance DAG
        |
        v
semantic state/action support cells
        |
        v
cross-fitted outcome/propensity models
        |
        v
sensitivity-aware credit interval [L, U]
        |
        +----------------+------------------+
        |                |                  |
      L > 0            U < 0          interval crosses 0
        |                |                  |
 weighted SFT     observed-pair DPO     context-only mask
```

graph、estimator 和 verifier 仅是训练时组件。推理时使用 fine-tuned policy，不需要 critic 或 provenance extractor。

### 3.3 Step 1: 构建 typed provenance DAG

对每条 trajectory，构造有时间戳节点的有向无环图 $G_i=(V_i,E_i)$：

- task constraints 与 subgoals；
- tool actions 与 environment state changes；
- observations 与 evidence spans；
- memory writes、updates、retrievals 与 summaries；
- 使用早期证据的 claims 或 decisions；
- locally verified milestones；
- terminal outcome。

边类型为 `produces`、`supports`、`uses`、`updates`、`retrieves`、`enables` 或 `verifies`。

构造时遵循严格优先级：

1. 来自 tool-call/result IDs、file paths、URLs、test IDs 和 memory source indices 的 deterministic metadata edges；
2. state transitions 与 milestone checks 的 schema rules；
3. 只看 prefix 的 semantic extractor，用于 support/use edges。

在 primary setting 中，extractor 可以看到当前 prefix，但不能看到未来 observations、terminal reference answer 或 developer patch。每条 semantic edge 都必须引用一个更早的 source span。指向未来或缺少 source 的边会被拒绝。

DAG 是一种 **eligibility structure**，不是因果证明。它回答“这个早期 segment 是否可能支持这个后续 decision？”；eligible segment 是获得正向还是负向 utility credit，由 outcome variation 决定。

### 3.4 Step 2: 定义 semantic segments 与 treatments

Turn-level credit 对多调用 subroutines 来说太细，对混合 thought/action turns 又太粗。使用 deterministic boundaries 将 trace 折叠为 semantic segments $z_{i,j}$：

- 一个 tool call 及其返回 observation；
- 一个 memory operation 及其引用 sources；
- 一次 edit 及其随后的 local test；
- 产生一个 evidence item 的 search/read sequence；
- 一次 plan/subgoal transition；
- final decision 或 answer claim。

对每个 segment 定义：

$$
c_{i,j}=\phi(h_{i,j}), \qquad u_{i,j}=\psi(z_{i,j}),
$$

其中 $c$ 是 support-cell representation，$u$ 是 semantic action class。

$\phi(h)$ 包括 task constraints、verified milestones、available tools、environment fingerprints、current memory contents、behavior-policy ID 和 compact provenance features。$\psi(z)$ 包括 event type 和 normalized effect signature，例如 `read(file, symbol-family)`、`run(test-scope)`、`search(query-intent)`、`edit(component, operation-type)` 或 `retrieve(memory-topic)`。

精确 output tokens 仍然是 SFT target。抽象表示只用于寻找可比较的 logged decisions。

#### Action-class audit

如果一个 semantic class 的成员在其他相似 prefixes 下会导致实质不同的 next-state effects，则该 class 无效。在 held-out fold 上：

1. 计算 normalized next-observation 或 state-diff signatures；
2. 在 conditioning on $c$ 之后测量 within-class disagreement；
3. 若 disagreement 超过 held-out 90th-percentile noise floor，则拆分该 class；
4. 如果没有稳定 split 具有足够 support，则给该 class 赋予 $[-1,1]$ credit。

这可以避免把 `read(file)` 这种宽泛 class 当作一个稳定 action，即使具体读哪个文件才是决定性因素。

### 3.5 Step 3: 定义 estimand

对 support cell $c$ 和 semantic action $u$，定义 natural-continuation value：

$$
V(u,c)=\mathbb E\left[Y\mid \operatorname{do}(U=u),C=c,
\text{future follows the logged policy mixture}\right].
$$

Credit 是相对于 observed alternative actions 的 contrast：

$$
\Delta(u,c)=V(u,c)-
\sum_{v\ne u}\bar e(v\mid c,U\ne u)V(v,c),
$$

其中 $\bar e$ 是限制在 alternatives 上的 behavior-policy mixture。这个 estimand 问的是：选择 $u$ 是否优于 logs 中实际出现过的 alternatives。它不估计 unconstrained optimal action，也不要求未来 actions token-by-token 固定。

### 3.6 Step 4: 用 cross-fitted doubly robust learning 估计 credit

按 task 或 repository 切分数据，绝不按 segment 切分。对每个 held-out fold，在其余 folds 上拟合：

- behavior propensity model $\hat e(u\mid c)$；
- outcome model $\hat Q(c,u)\approx\mathbb E[Y\mid C=c,U=u]$。

对 local support cell 中的 action $u$，使用 doubly robust estimate：

$$
\hat V(u,c)=\frac{1}{|I(c)|}\sum_{k\in I(c)}
\left[
\hat Q(c_k,u)+
\frac{\mathbf 1[u_k=u]}{\hat e(u\mid c_k)}
\left(y_k-\hat Q(c_k,u)\right)
\right].
$$

contrast $\hat\Delta(u,c)$ 使用与 estimand 相同的 observed-alternative mixture。Cross-fitting 防止某个 segment 被在它自身 task outcome 上训练过的 nuisance models 评分。由于同一 trajectory 中所有 segments 共享 terminal outcome，standard errors 与 bootstrap resampling 需要按 task cluster。

在 overlap 和 sequential ignorability 成立时，若 propensity model 或 outcome model 任一正确指定，doubly robust estimator 就是一致的。这种保护**不能**消除 hidden confounding，也不能修复无效的 action abstraction。

#### Support gates

初始保守默认值，后续在 pilot 中校准：

- 每个被比较 action 满足 $\hat e(u\mid c)\ge 0.05$；
- inverse-propensity effective sample size 至少为 20；
- 可能时，pooled support cell 至少由两个 behavior-policy sources 贡献；
- 单个 task 贡献不超过 local weight 的 10%。

如果 gate 失败，interval 设为 vacuous range $[-1,1]$，而不是 extrapolate。

### 3.7 Step 5: 生成 sensitivity-aware credit intervals

对每个 supported segment，使用以下信息构造 $[L_{i,j},U_{i,j}]$：

1. task-clustered bootstrap uncertainty；
2. 对被选择 segment classes 的 simultaneous max-$t$ correction；
3. matched semantic neighbors 之间的 disagreement；
4. odds-ratio hidden-confounding sensitivity parameter $\Gamma$。

报告 $\Gamma\in\{1,1.25,1.5,2\}$；只有在 held-out calibration pilot 后，才把 $\Gamma=1.5$ 作为预注册主设定。若结果在 $\Gamma=1.25$ 时就消失，应描述为 fragile observational evidence。

interval 有三种状态：

| Interval | 解释 | 允许的训练用途 |
|---|---|---|
| $L>0$ | 在指定 support/sensitivity model 下可靠为正 | 若也 grounded，则用于 weighted SFT |
| $U<0$ | 可靠为负 | 只有存在正向 observed alternative 时才构造 preference against it |
| $L\le 0\le U$ | 模糊或 unsupported | 仅作为 context；mask semantic action/reasoning loss |

### 3.8 Step 6: 将 groundedness 与 utility 分离

高 outcome correlation 仍可能奖励缺乏证据的猜测。使用以下信息计算 prefix-only groundedness score $g_{i,j}\in[0,1]$：

- tool/schema validity；
- 与先前 environment observations 的一致性；
- emitted claims 的 evidence entailment；
- 不含 future/reference leakage；
- 可用时的 local state-effect verification。

独立地，计算 provenance flow $\rho_{i,j}\in[0,1]$：从 verified milestones 和 terminal output 出发，沿 source-to-use paths 向后路由 normalized mass。primary version 中，没有通向任何 verified milestone 的 segment 得到 $\rho=0$。Infrastructure tokens 由一个小的 schema anchor 单独保护。

这种分解区分两个问题：

- **该 action 是否由当时可用信息支持？** $g$
- **logged evidence 是否表明它改善了最终结果？** $[L,U]$

### 3.9 Step 7: 将 credit 编译为 SFT 与 preference targets

对可靠正向 segment：

$$
w_{i,j}=\min(c_{\max},\max(0,L_{i,j}))\,g_{i,j}\rho_{i,j},
$$

其中 $c_{\max}$ 是 training split 上 positive lower bounds 的 95th percentile。对每条 trajectory 的总权重做 normalize 或 cap，避免长轨迹主导。

对可靠负向 segment $z^-$，只有当同一 support cell 中存在 grounded positive alternative $z^+$ 时，才创建 preference pair。Pair construction 只在两种情况下允许：两个 alternatives 来自完全相同的 restorable state，或 verified schema 能在同一个 canonical prompt 下渲染两者。仅有 semantic similarity 不足以构成有效 DPO pair。

Pair loss 为

$$
\mathcal L_{\mathrm{pair}}=-\log\sigma\left(
\beta\left[
\log\frac{\pi_\theta(z^+\mid h)}{\pi_{\mathrm{ref}}(z^+\mid h)}-
\log\frac{\pi_\theta(z^-\mid h)}{\pi_{\mathrm{ref}}(z^-\mid h)}
\right]\right).
$$

primary experiment 中，不要仅凭 judge 合成一个“更好” action。如果没有 observed positive alternative，就 mask 负向 segment，而不是使用 unlikelihood training。

完整目标为

$$
\mathcal L_{\mathrm{CPCD}}=
\mathcal L_{\mathrm{positive\text{-}SFT}}
+\lambda_{\mathrm{pair}}\mathcal L_{\mathrm{pair}}
+\lambda_{\mathrm{schema}}\mathcal L_{\mathrm{schema}}.
$$

使用 $\lambda_{\mathrm{schema}}=0.05$ 作为 formatting、tool schema 与 mandatory control tokens 的初始 anchor；在 $\{0,0.05,0.1\}$ 中调参。正向但 ungrounded/privileged segments 与 ambiguous segments 保留在输入 context 中，但不获得 semantic target loss。

### 3.10 算法草图

```text
Input: frozen logs D, terminal outcomes, tool metadata

1. Split by task/repository into K folds.
2. For each trajectory:
   a. segment tool, memory, edit/test, and decision episodes;
   b. build deterministic provenance edges;
   c. add prefix-only semantic support/use edges;
   d. derive state representation c and semantic action u.
3. For each held-out fold:
   a. fit propensity e(u|c) and outcome Q(c,u) on other folds;
   b. audit semantic action equivalence;
   c. estimate doubly robust contrasts on the held-out fold;
   d. compute clustered, simultaneous, sensitivity-aware [L, U].
4. Compute prefix-groundedness g and provenance flow rho.
5. Compile examples:
   a. L > 0 and grounded -> weighted SFT;
   b. U < 0 plus observed positive alternative -> DPO pair;
   c. otherwise -> context-only masked segment.
6. Cap loss mass per trajectory and train the policy.
7. At evaluation, discard all credit models and run the policy normally.
```

### 3.11 能保证什么

如果 simultaneous intervals 在 family-wise level $\alpha$ 下有效，并且只在 interval 排除 0 时更新，那么在因果假设成立的条件下：

$$
\Pr(\text{any selected segment has the wrong true sign})\le \alpha.
$$

这是一个保守的 **sign-selection guarantee**，不是精确 causal-effect recovery 或 downstream policy improvement 的保证。如果 support representation 遗漏重要 confounders、semantic action classes 不一致，或 interval procedure 未校准，这个保证会失效。

### 3.12 Practical MVP 与 full version

#### CPCD-Lite: 第一篇 paper-quality pilot

- 只使用 deterministic tool/memory provenance。
- 为 ALFWorld/WebShop 或 tau-bench 使用 hand-specified action classes。
- 要求 exact task/milestone support cells。
- 拟合 cross-fitted logistic propensity 与 outcome models。
- 使用 task bootstrap intervals 与 overlap abstention。
- 训练 positive weighted SFT；只有在 positive-credit calibration 成功后再加入 DPO。

这个版本可以测试核心 claim，而不依赖 learned graph encoder 或大型 LLM judge。

#### CPCD-Full: long-horizon extension

- 加入 prefix-only semantic provenance extraction。
- 使用 task-clustered cross-fitting 学习 state/action embeddings。
- 加入 hidden-confounding sensitivity intervals。
- 包含 memory write/retrieve/update 与 evidence source/use episodes。
- 加入 observed-pair negative preference training。
- 扩展到 SWE 与 frozen-corpus deep research。

### 3.13 预期 failure modes 与内置响应

| Failure mode | Diagnostic | Response |
|---|---|---|
| 每个 state 只有一个 action | Low propensity/ESS | Abstain；在声称 offline CA 前收集更多样的 log set |
| State abstraction 合并了不兼容 histories | High within-class next-state disagreement | 拆分 class 或让 interval vacuous |
| Teacher-only hidden information | Credit 在 policy-ID 或 $\Gamma$ sensitivity 下崩溃 | 排除 privileged traces，或单独报告 privileged variant |
| Provenance extractor 使用未来证据 | Prefix-leakage audit | 拒绝 edge 并重新标注 |
| 长成功轨迹主导 | Per-task gradient mass imbalance | 按 trajectory cap 与 normalize loss mass |
| Negative credit 没有更好 action | No supported positive pair | 只 mask；不要 hallucinate preference target |
| Tuning 后 policy 离开 logged support | OOD action/state rate rises | 更强 SFT anchor、保守 decoding，或把 iterative data collection 作为独立 semi-online extension |

---

## 4. 实验计划

### 4.1 Research questions

1. **RQ1: credit validity.** Offline intervals 能否预测 evaluation-only intervention effects 的符号与排序？
2. **RQ2: policy value.** 在相同 data、token 与 compute budgets 下，certified selective training 是否提升 task success？
3. **RQ3: long-horizon value.** 对 source-to-use distance 长、memory operations 多、超过 50 或 100 turns 的 trajectories，provenance 是否更重要？
4. **RQ4: failure reuse.** 从 failed traces 中提取的有用 segments 是否负责 OOD/hard-task gains？
5. **RQ5: conservatism.** 随着 overlap、confidence 与 $\Gamma$ thresholds 变化，precision/coverage trade-off 如何？

### 4.2 Benchmark stack

使用分阶段 stack；在证明 credit labels 有意义之前，不要从最昂贵的 SWE 设定开始。

| 阶段 | 领域与 split | 为什么需要 | Proposed frozen log construction | Primary evaluation |
|---|---|---|---|---|
| A: controlled attribution | ALFWorld + WebShop | Restorable states 与多个 alternatives 让 intervention credit 可测 | 每个 training task 从多个 checkpoints/temperatures 采样 16-32 条 trajectories，然后 freeze | Success；held-out branch points 上的 intervention sign/precision |
| B: structured tool agent | tau-bench retail，然后 tau2-bench | 真实 tool schemas、business state、mixed recoveries、中等 horizon | 每个 task 从 base/SFT/teacher mixtures 采样 8-16 条 trajectories | Task accuracy、policy violations、turns、credit risk-coverage |
| C: true long horizon | SWE-Gym logs -> SWE-bench Verified | Repository state、延迟 edit/test effects、50-100+ turn traces | 成本允许时每个 issue 4-8 条多样 trajectories；只 pool 经过 audit 的 semantic cells | Pass@1、cost、turns、tests passed、horizon-stratified credit |
| D: optional deep research | OpenResearcher-style frozen corpus -> BrowseComp-Plus/GAIA | Evidence provenance、citations、bounded context、100+ calls | Frozen search corpus 与 source-indexed logs；不依赖 live web | Answer accuracy、citation support、source-to-use distance |

家庭/网页/客服任务使用 task-level splits；SWE 使用 repository-level splits。Near-duplicate tasks、issue variants 与来自同一 environment seed 的 trajectories 必须留在同一个 split。

### 4.3 Offline log design

研究质量更依赖 behavior diversity，而不是原始 trajectory 数量。冻结 corpus 应来自：

- base model、一个 SFT checkpoint，以及至少一个更强 teacher；
- 两到三个 sampling temperatures；
- successful、failed 与 recovered trajectories；
- 显式 policy/checkpoint identifiers，并在可能时记录 action log-probabilities。

在拟合任何 credit model 前冻结 logs。Primary CPCD training run 不进行环境调用。State restoration 与 branching 仅限 held-out credit-evaluation set。

分别评估四种数据设定：

1. 只有 expert successes；
2. mixed expert successes and failures；
3. heterogeneous base/SFT/teacher logs；
4. cross-policy transfer，即从 credit estimation 中 held out 一个 behavior source。

### 4.4 Baselines

#### Strict-offline 与 SFT baselines

| Baseline | 控制的问题 |
|---|---|
| Full-trajectory SFT | 任意 selection 是否能超过标准 imitation？ |
| Success-only RFT/SFT | 使用 failed data 是否真的有用？ |
| Reward-weighted SFT | within-trajectory credit 是否优于 trajectory weighting？ |
| Same-token random mask | 收益是否只是 regularization 或 supervised tokens 变少？ |
| Perplexity/entropy mask | causal structure 是否优于 model uncertainty？ |
| Explicit tool-error mask / SWE-Lego recipe | CPCD 是否超过显而易见的 error removal？ |
| ATLaS-style critical-step selector | outcome-supported credit 是否优于 LLM-perceived importance？ |
| STeP/EEF-style masks or segment mining | calibrated signed credit 是否优于 heuristic reflection/failure reuse？ |
| AWR or Q-SFT | interval selection 是否优于 scalar value weighting？ |
| HPL | provenance-aware support 是否优于单纯 multi-scale preference？ |
| Agentic-DPO | 使用 outcomes、failures 与 abstention 是否优于 expert-state one-step preferences？ |

#### Upper bounds 与 diagnostic controls

- 遵循 IPR/CSO 思路的 evaluation-only Monte Carlo branch credit；
- 从 branch effects 得到的 oracle mask；
- 当 reference patches 与 tests 可用时，在 SWE 上使用 P2T；
- 不使用 provenance 的 flat-history doubly robust estimation；
- 在存在可实现 instrument 时使用 DML-IL-inspired history representation。

Semi-online methods 是 upper bounds，不是严格 apples-to-apples offline baselines。

### 4.5 直接 credit evaluation

对每个领域 300-500 个 held-out branch points：

1. 在 segment 之前恢复环境；
2. 执行 logged semantic action 和一个或多个 observed alternative classes；
3. 使用同一个 fixed continuation policy 继续 $K=16$ 次 stochastic rollouts；
4. 估计 intervention contrast $\Delta^{\mathrm{branch}}$ 及其 uncertainty；
5. 绝不把这些 branch outcomes 回灌给 offline credit estimator。

这测量的是 CPCD 使用的同一个 natural-continuation estimand。在 SWE 中，恢复 container snapshot，并且只在 state effects 可 replay 的 actions 上 branch。在 deep research 中，使用 frozen document corpus 避免 live-web non-stationarity。

主要 credit metrics：

- positive 与 negative credit 的 sign precision and recall；
- 10%、30%、50% coverage 下的 causal precision；
- area under the risk-coverage curve；
- 与 branch-effect magnitude 的 Spearman correlation；
- interval coverage 与 average interval width；
- successful traces 中 harmful steps 被错误奖励的比例；
- failed traces 中 helpful steps 被错误抑制的比例；
- 按 trajectory length 与 source-to-use distance 分层的 metrics。

主 endpoint 应是**预注册 useful coverage 下的 precision**，而不是在 ambiguous steps 占多数的数据集上报告 accuracy。

### 4.6 Downstream policy evaluation

报告：

- Success@1 / Pass@1，以及次要的 Pass@k；
- turns、generated tokens、tool calls、wall time 与 dollar-equivalent environment cost；
- invalid action/schema rate 与 safety/policy violations；
- hard-task 与 OOD generalization；
- horizon bins `<20`、`20-50`、`50-100`、`>100` turns 上的表现；
- supervised-token fraction 与每个 task 的 effective gradient mass；
- post-training state/action OOD rate relative to the frozen corpus。

分别使用 equal raw tasks、equal supervised action-token count、equal optimizer FLOPs 三种比较。Selective method 不应因为训练 token 更少或 optimizer steps 更少而得到不公平 credit。

### 4.7 关键 ablations

#### Attribution structure

- 移除 provenance，只使用 flat turn-recency/history features；
- 随机置换 provenance edges，同时保持 node degree；
- 使用 turn、fixed-length segment 与 semantic-segment units；
- 移除 memory write/retrieve/update nodes；
- 只用 deterministic edges vs deterministic plus semantic edges。

#### Identification and uncertainty

- 没有 intervals 的 point estimate；
- no cross-fitting；
- no propensity model；
- no outcome model；
- no overlap/ESS gate；
- $\Gamma\in\{1,1.25,1.5,2\}$；
- exact cells vs learned semantic matching；
- same-policy vs mixed-policy logs。

#### Training compiler

- positive SFT only；
- negative DPO only；
- no groundedness gate；
- no provenance-flow weight；
- no schema anchor；
- no trajectory mass cap；
- 用 point estimate $\hat\Delta$ 替代 lower bound $L$；
- 在每个 task 内置换 $L$，同时保持其 histogram 与 total loss mass。

最后一个 permutation 是关键 falsifier：如果性能不变，说明 estimated credit values 没有发挥有效作用。

### 4.8 Leakage 与 judge controls

- Credit-model inputs 截止到 segment prefix；未来 observations 与 references 不可访问。
- Reference patches/answers 只用于明确命名的 privileged baselines 或 held-out evaluation。
- 任意 semantic extractor 在 policy training 前冻结，并在人类标注 edges 上 audit。
- Credit-estimator folds、policy-training tasks 与 final evaluation tasks 互不重叠。
- 在 stratified human sample 上校准 judge/groundedness labels，样本包括 successes with regressions 与 failures with good prefixes。
- 报告 inter-annotator agreement 与 judge false-positive rates。
- 记录所有 behavior-policy IDs，以测试“teacher identity”是否充当 hidden success label。

### 4.9 Statistical protocol

- 至少使用三个 policy-training seeds。
- 以 task 而不是 segment 作为 independent unit。
- 对 success differences 报告 paired task-bootstrap 95% confidence intervals。
- 对 credit metrics 使用 cluster bootstrap，并校正少量 co-primary domain comparisons。
- 预注册两个 co-primary endpoints：30% coverage 下的 causal precision，以及 equal supervised-token budget 下的 Success@1。
- 根据 10% pilot 中观察到的 variance 决定最终 sample size；不要用 correlated segment counts 做 power calculation。

### 4.10 Resource plan

以下是 planning bounds，不是 performance claims：

| Scope | Models/domains | Approximate budget |
|---|---|---|
| MVP | 一个 4B model，ALFWorld/WebShop，CPCD-Lite，3 seeds | 12-20 H100-equivalent GPU-days 加 branch-evaluation environment time |
| Main study | 4B 与 8B，controlled + tau-bench + SWE，full ablations | 60-90 H100-equivalent GPU-days |
| Semantic annotation | Prefix-only edge/grounding extraction 与 human audit | paid-model usage 控制在约 USD 4K；质量足够时替换为 open model |

在投入完整研究前，先用 5% 数据 benchmark throughput 与 label cost。主要成本很可能是 branch-based evaluation 和 long-context SWE fine-tuning，而不是 doubly robust estimator。

### 4.11 Success 与 kill criteria

#### 支持该 idea 的证据

- controlled interventions 上，在 30% coverage 下 positive-sign precision 至少 80%；
- 在两个 domains 上，相比 full SFT 和 same-token random masking，Success@1 可复现提升至少 3 absolute points；
- 在 `>50`-turn 或 long source-to-use bucket 中，provenance ablation gap 更大；
- 在匹配 token 与 compute budgets 后，failed-trace segments 仍有正向价值；
- 随着 $\Gamma$ 增大，precision/coverage 行为平滑退化。

#### 应停止或大幅修改项目的结果

- 合理 pooling 后，少于 10% segments 有 non-vacuous intervals；
- equal-mass permuted-credit mask 与 CPCD 持平；
- flat-history estimation 在 long-delay examples 上与 provenance 持平；
- equal supervised-token 或 equal-FLOP controls 下收益消失；
- credit precision 在 $\Gamma=1.25$ 下崩溃；
- Agentic-DPO 或 explicit tool-error masking 在所有 domains 上与 CPCD 持平；
- 改进只在 labels 泄漏 future/reference information 时出现；
- 超过 50 turns 的 trajectories 中没有收益。

这些标准能让工作即使失败也有信息量：结果会说明 fixed logs 是否包含足够 overlap，从而支持 long-horizon signed credit。

---

## 5. 推荐研究形态

### 5.1 Primary paper claim

一个 defensible claim 是：

> Long-horizon offline agent credit 应被视为 conservative sign identification，而不是 dense score prediction。Provenance-defined semantic segments 与 support-aware intervals 可以把 frozen logs 选择性编译为 SFT 和 preference targets，同时提升 intervention-level credit precision 与 downstream policy learning。

避免更强的说法，即 CPCD 恢复了每一步的真实 causal contribution。

### 5.2 Minimum publishable contribution

最小 coherent paper 包含三部分：

1. 一个带 intervention-derived credit signs 和 risk-coverage metrics 的 benchmark protocol；
2. CPCD-Lite，包含 overlap-aware interval abstention 与 context/target separation；
3. controlled evidence，证明带来 policy gain 的是 credit signal，而不是 token reduction。

Learned semantic graph、memory operations、SWE scaling 和 negative DPO 应在这个核心通过 falsification 后再加入。

### 5.3 主要 novelty risks

- **P2T overlap:** process graphs 与 grounded segment selection 已经通过 privileged SWE patches 展示过。区别必须是 fixed-log observational variation、signed intervals 与 abstention。
- **Agentic-DPO overlap:** state-conditioned offline preference learning 已经是强 baseline。区别必须来自 failures、delayed provenance 与 supported negative/ambiguous cases。
- **ATLaS overlap:** 仅把 selector prompt 换成 score model 不够。必须做直接 intervention calibration。
- **DML-IL overlap:** 通用 doubly robust 或 instrumental-variable imitation learning 不是新的。贡献必须是 long-horizon semantic treatment definition、compiler 与 empirical causal-credit benchmark。
- **快速变化的 2026 文献:** 在声称 novelty 或投稿前，必须重新做 primary-source 与 OpenReview 搜索。

### 5.4 Implementation order

1. 冻结一个 multi-policy ALFWorld/WebShop log set，并定义 restorable branch points。
2. 实现 deterministic segmentation、support cells 与 branch-effect evaluation。
3. 在构建 semantic graph model 前，先跑 full SFT、random mask、ATLaS-style mask 与 CPCD-Lite。
4. 加入 failure traces，测试 positive credit 能否恢复有用 prefixes。
5. 只有 intervention precision 校准后，再迁移到 tau-bench。
6. 加入 provenance 与 long-delay buckets。
7. 最后加入 SWE 与 observed-pair DPO。

---

## 6. 文献检索说明

本次 focused search 使用了 `offline trajectory`、`selective SFT`、`critical step`、`expert failure`、`hindsight relabeling`、`state-conditioned preference`、`privileged process supervision` 与 `long-horizon agent` 等组合，覆盖 2024-2026 年。检索源包括 arXiv、OpenAlex 与 Semantic Scholar，并随后用 primary arXiv page 做验证。统一搜索过程中多次遇到 rate limit，且本环境无法穷尽查询 OpenReview，因此本文是 focused research review，而不是 formal systematic review。各论文报告的数值收益不应当作 leaderboard 横向比较。

### 建议阅读顺序

1. [ATLaS](https://arxiv.org/abs/2503.02197)：最清晰的 selective-SFT formulation。
2. [STeP](https://arxiv.org/abs/2505.20023) 与 [EEF](https://arxiv.org/abs/2504.13145)：context/target separation 与 useful failure fragments。
3. [Q-SFT](https://arxiv.org/abs/2411.05193) 与 [AWR](https://arxiv.org/abs/1910.00177)：value-weighted likelihood foundations。
4. [HPL](https://arxiv.org/abs/2510.03253) 与 [Agentic-DPO](https://arxiv.org/abs/2607.10601)：group 与 state 粒度的 offline preference learning。
5. [SWE-Lego](https://arxiv.org/abs/2601.01426) 与 [P2T](https://arxiv.org/abs/2605.21996)：真实 long-horizon SWE SFT 与 privileged process curation。
6. [HSL](https://arxiv.org/abs/2607.04235) 与 [AgentHER](https://arxiv.org/abs/2603.21357)：对 unintended 或 failed outcomes 的 hindsight use。
7. [DML-IL](https://arxiv.org/abs/2502.07656)：causal-identification warning 与理论基础。
8. [IPR](https://arxiv.org/abs/2406.11176) 与 [CSO](https://arxiv.org/abs/2602.03412)：intervention-based upper bounds 与 evaluation design。
9. [ECHO](https://arxiv.org/abs/2606.31650)：long-delay memory provenance 与 credit routing。
