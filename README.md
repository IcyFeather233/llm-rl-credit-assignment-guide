# LLM RL Credit Assignment Guide

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

A curated, **self-contained** guide to **credit assignment (CA) in reinforcement learning for LLMs** (2024–2026): taxonomy + reproducibility + navigation.

1. **Taxonomy** — reasoning & agentic · granularity × methodology  
2. **Reproducibility** — featured entries use **[📄 Paper] · [💻 Code] · [📦 Data]**  
3. **Navigation** — Training / Evaluation / Analysis · date-desc · major labs first  

Survey: [arXiv:2604.09459](https://arxiv.org/abs/2604.09459).

**Legend:** `✅` open · `◐` partial · `❌` none · plain `—` = missing (not a link)

---

## Contents

- [How CA changes model training](#how-credit-assignment-changes-model-training)
- [Long-horizon frontier (2026-06/07)](#long-horizon-frontier-2026-0607)
- [Featured (major labs)](#featured-major-labs)
- [Recently added / gap-fill](#recently-added--gap-fill)
- [Awesome cross-check](#awesome-cross-check) → [`papers/awesome_gap_fill.md`](papers/awesome_gap_fill.md)
- [Taxonomy](#taxonomy-map) → [`papers/taxonomy.md`](papers/taxonomy.md)
- [Training](#training) → [`papers/training.md`](papers/training.md)
- [Evaluation](#evaluation) → [`papers/evaluation.md`](papers/evaluation.md)
- [Analysis](#analysis--surveys) → [`papers/analysis.md`](papers/analysis.md)
- [Default stacks](#default-stacks-2026)
- [Full catalog](#full-catalog) → [`papers/catalog.md`](papers/catalog.md)

---

## How credit assignment changes model training

### The common baseline

For a prompt, outcome-supervised GRPO samples a group of trajectories, scores each trajectory with a terminal reward, and computes

$$
A_i^{\text{GRPO}} = \frac{R(\tau_i)-\mu_R}{\sigma_R+\epsilon}.
$$

The same $A_i^{\text{GRPO}}$ is assigned to every generated token in trajectory $\tau_i$. This is cheap and removes PPO's learned value model, but it cannot distinguish a pivotal decision from boilerplate, a correct prefix in a failed rollout, or a bad step followed by recovery. The methods below replace this broadcast signal with token-, segment-, step-, turn-, or agent-specific advantages.

Most training pipelines still have the same five stages:

1. Sample on-policy trajectories from the current policy.
2. Obtain a terminal verifier reward and, where available, process signals.
3. Convert those signals into a fine-grained advantage $A_{i,t}$.
4. Assign $A_{i,t}$ to the corresponding generated tokens only.
5. Update the policy with a clipped PPO/GRPO objective, DPO, or REINFORCE, usually with KL regularization.

The important differences are therefore **where the credit signal comes from**, **what unit receives it**, and **how much extra rollout, critic, verifier, or judge compute is required**.

### Reasoning RL: concrete training mechanisms

| Method | Credit unit and signal | How the model is trained | Extra requirements / main limitation |
|--------|------------------------|--------------------------|--------------------------------------|
| [GRPO / DeepSeekMath](https://arxiv.org/abs/2402.03300), [DeepSeek-R1](https://arxiv.org/abs/2501.12948) | One normalized terminal reward per response; copied to all tokens | Clipped group-relative policy objective plus KL to a reference policy | No critic and low overhead, but no within-response credit differentiation; this is the default baseline rather than a fine-grained CA method |
| [VinePPO](https://arxiv.org/abs/2410.01679) | Reasoning-step advantage from $K$ continuations sampled after an intermediate prefix; MC estimates $V(s)$ and $A(s,a)$ | Replaces PPO's learned value estimates with unbiased MC estimates; the remaining PPO update is unchanged | No learned critic, but auxiliary continuations make cost scale with the number of sampled prefixes and $K$ |
| [SPO](https://arxiv.org/abs/2505.23564) | Segment advantage $V(s_{k+1})-V(s_k)$; boundaries use fixed spans or low-probability cutpoints | Estimates segment values with chain or reusable tree MC rollouts, then applies the segment advantage to all tokens or only low-probability cutpoints | Middle ground between trajectory and token credit; still requires extra rollouts from segment boundaries |
| [PRIME](https://arxiv.org/abs/2502.01456) | Token reward $\beta\log(\pi_{\rm PRM}/\pi_{\rm ref})$ from an implicit PRM trained only with outcome labels | Updates the implicit PRM online on current rollouts; combines discounted token rewards with an outcome RLOO advantage, then uses PPO clipping | Avoids step labels and keeps the PRM on-policy, but trains and serves an additional causal LM |
| [PURE](https://arxiv.org/abs/2504.15275) | Step-level PRM rewards, transformed so the lowest-scoring future step dominates the return | Places transformed process reward on each step's final token, combines it with verifiable outcome RLOO, and updates with a PPO-style loss | Requires a usable PRM and explicit step boundaries; min-form credit is designed to prevent sum-form reward hacking |
| [CAPO](https://arxiv.org/abs/2508.02298) | A stronger off-the-shelf LLM identifies erroneous reasoning steps in one generative pass; multiple critiques may vote | Starts from the outcome reward, penalizes tokens in flagged steps, keeps outcome weight larger than process weight, then applies clipped RLVR optimization | No separately trained PRM, but online judge calls are expensive and credit quality depends on the GenPRM |
| [OAR](https://arxiv.org/abs/2601.07408) | Token importance is the change in the final-answer distribution after masking a token (OAR-P), or a gradient-based sensitivity approximation (OAR-G) | Suppresses low-importance token advantages, boosts important ones, and renormalizes to preserve GRPO's total advantage mass | OAR-P needs $O(L)$ forward passes; OAR-G reduces this to one extra backward pass but is only a first-order proxy |
| [DelTA](https://arxiv.org/abs/2605.21467) | Tokens are weighted by how well their gradient vectors discriminate positive- from negative-advantage responses | Iteratively refines positive/negative gradient centroids, converts discriminative scores into stop-gradient weights, and reweights the DAPO/RLVR token loss | Critic-free and no new reward, but coefficient computation needs an LM-head gradient proxy and captures local update geometry rather than causal effects |
| [SCRL](https://arxiv.org/abs/2605.22074) | Per-subproblem binary rewards for an increasing curriculum extracted from a reference solution | Keeps only the consecutively solved prefix, normalizes each subproblem position across rollouts, maps its advantage to tagged answer tokens, and mixes curriculum GRPO with original-prompt GRPO | Produces objective process credit, but needs reference solutions, an LLM-generated curriculum, reliable parsing, and per-subproblem verifiers |

### Agentic RL: concrete training mechanisms

| Method | Credit unit and signal | How the model is trained | Extra requirements / main limitation |
|--------|------------------------|--------------------------|--------------------------------------|
| [ArCHer](https://arxiv.org/abs/2402.19446) | Turn-level $Q(s,a)-V(s)$ from an off-policy utterance critic; the turn advantage is the terminal reward for its tokens | Trains utterance-level Q/V models with replay-buffer TD targets and the token actor with REINFORCE; the offline variant uses IQL + AWR | Sample-efficient environment use, but maintains learned critics and separates high-level off-policy from low-level on-policy training |
| [SWEET-RL](https://arxiv.org/abs/2503.15478) | A turn-wise advantage LLM sees privileged training-only information such as the reference solution | First trains the advantage model from preferred/rejected trajectory pairs with a Bradley-Terry loss; then ranks candidate actions per turn and trains the actor with DPO | Strong for partially observable collaboration, but needs offline interaction pairs and privileged labels; it is a two-stage method rather than online PPO |
| [AgentPRM](https://arxiv.org/abs/2511.08325) | A PRM predicts both long-term promise $Q(s_t,a_t)$ and local progress/advantage between adjacent decisions | Generates automatic targets using TD bootstrapping plus GAE, trains the PRM with value and advantage losses, then uses it for search or as dense RL reward | Much cheaper than per-state MC labeling, but bootstrapping introduces critic bias; the paper's main evidence is PRM/search, with policy RL as a secondary study |
| [GiGPO](https://arxiv.org/abs/2505.10978) | Episode GRPO advantage plus a step advantage computed among actions observed at the same anchor state | Reuses repeated states already present across rollout groups, compares their discounted returns, and applies the combined advantage with a clipped group objective | Critic-free and no extra rollout, but exact anchor-state recurrence can be sparse |
| [ProxMO](https://arxiv.org/abs/2602.19225) | Success-rate-modulated episode credit plus a soft step baseline weighted by TF-IDF state similarity | Amplifies rare successes, attenuates noisy failures on easy tasks, computes $A_t=R_t-\sum_j w_{ij}R_{j,t}$, and uses clipped PPO | Avoids singleton hard groups; quality depends on textual similarity being a meaningful proxy for state equivalence |
| [HGPO](https://arxiv.org/abs/2602.22817) | Multiple step-relative advantages from nested groups sharing the current state and increasingly long histories | Computes one advantage per context level and combines them with larger weight on longer-context groups before the clipped update | Critic-free and offline over collected rollouts; trades lower contextual bias against higher variance as groups shrink |
| [HiPER](https://arxiv.org/abs/2602.16165) | Separate advantages for subgoal switching, subgoal choice, and primitive execution | One LLM emits structured `switch/subgoal/action` fields; learned high- and low-level critics provide boundary-aware hierarchical GAE for PPO updates | Fits explicit plan-execute tasks and long horizons, but requires structured actions and two coupled value baselines |
| [HCAPO](https://arxiv.org/abs/2603.08754) | Step hindsight value $Q^H_t=\rho_t G_t$; $\rho_t$ is derived by re-scoring the action after revealing the final state | Adds normalized hindsight credit to the GRPO outcome advantage, masks harmful negative corrections on successful trials, and applies PPO clipping | Does not re-execute the environment or train a separate critic, but requires extra hindsight prompting and relies on self-normalized LLM likelihoods |
| [VPR](https://arxiv.org/abs/2605.10325) | Objective per-turn verifier reward from search, a constraint solver, or an exact posterior | Normalizes verifier rewards at each turn across active rollouts and inserts the resulting turn advantage into a clipped GRPO objective | Clean dense credit without a learned judge, but only applies when every intermediate action is objectively verifiable |
| [GraphGPO](https://arxiv.org/abs/2605.26684) | Step reward from shortest cost-to-go in a graph made by merging all observed state transitions | Builds a rollout graph, scores edges by distance reduction toward a successful state, normalizes outgoing edges per state, and mixes graph and episode advantages | Critic-free and reuses rollouts; needs mergeable states and sufficiently covered, mostly stable transition structure |
| [TRIAGE](https://arxiv.org/abs/2606.32017) | An LLM judge labels each segment as decisive, exploratory, no-progress, or regressive; fixed role constants correct GRPO credit | Adds a bounded role reward to the episode advantage, whitens segment advantages, broadcasts them only to that action's tokens, then uses standard clipped GRPO | Interpretable and no judge at inference, but adds one judge call per segment and can degrade when role classification is unreliable |

### Long-horizon frontier (2026-06/07)

These additions are the methods in the accompanying long-horizon survey that were missing or only listed by name in the repository. They extend the assignable unit beyond ordinary action tokens to graph edges, evidence turns, retry boundaries, memory operations, context summaries, and intervention decisions.

| Method | Credit signal and model update | Cost / applicability |
|--------|--------------------------------|----------------------|
| [G2PO](https://arxiv.org/abs/2606.22995) | Merges identical observations across rollouts into a global transition graph, averages rollout returns into node values, globally normalizes edge TD increments, and mixes edge-, step-, and episode-level advantages in a GRPO-style update | Critic-free and reuses the batch; depends on repeated observations or a trustworthy state equivalence rule |
| [PBSD](https://arxiv.org/abs/2606.09348) | Compares each sampled turn's likelihood under the ordinary policy and the same model conditioned on the verified answer; the detached Bayesian evidence ratio calibrates how much terminal advantage that turn inherits | No rerollout or external judge, but requires a verified answer and calibrated privileged likelihoods; demonstrated with up to 300 turns, 64K training context, and 256K evaluation context |
| [CRAFT](https://arxiv.org/abs/2606.29476) | Reuses other rollouts in the same GRPO group to estimate the signed counterfactual effect of moving toward a privileged teacher's token preference, then gates distillation and chooses the KL direction token by token | Adds a teacher forward pass but no new environment rollout; relies on sibling coverage and privileged self-distillation context |
| [ECHO](https://arxiv.org/abs/2606.31650) | Stores source-indexed turn memories, lets the policy select records for bounded-context reconstruction, and routes positive outcome advantage only to the final segment, selected evidence turns, memory findings, and selection actions | Direct provenance credit for search agents; source tracing must survive compression and the current rule only routes positive credit |
| [PivoARL](https://arxiv.org/abs/2607.03702) | Reflects over a failed trace to locate the pivotal erroneous turn, retries from that state, rewards the reusable prefix, isolates the faulty suffix, and trains reflection from retry returns | Concentrates counterfactual interaction near the error boundary; requires restorable intermediate environments and reliable pivotal-turn reflection |
| [ProGPO](https://arxiv.org/abs/2607.04242) | Keeps exact-prefix peer comparison, fills singleton gaps with rollout-derived state-potential differences, and fuses potential estimates across history depths using inverse variance | Learned-critic-free; semantic state expansion can still compare behaviorally different states |
| [IGRPO](https://arxiv.org/abs/2607.06223) | Scores tree nodes by ground-truth-answer information gain, allocates a fixed expansion budget toward informative nodes, and trains the policy toward the resulting tilted teacher distribution with group-relative advantages | Connects exploration allocation to learning, but assumes answer likelihood is a meaningful information proxy |
| [SAO](https://arxiv.org/abs/2607.07508) | Replaces GRPO-style per-prompt groups with one rollout per prompt in asynchronous training, uses rollout log-probs as the behavior policy for direct double-sided importance-sampling masks, and estimates advantages with a trained value model plus skip-observation token-level GAE | System-level asynchronous RL method rather than a causal step-attribution estimator; reduces group-waiting and policy-lag issues but depends on reliable rollout log-probs, a strong value model, and infrastructure for long traces |
| [TACO](https://arxiv.org/abs/2607.07976) | Computes a context-aware tail-risk score and attenuates positive credit for implausible low-probability tokens without zeroing their gradients | Low-overhead token calibration; complements rather than replaces cross-turn CA |
| [Memory-R2](https://arxiv.org/abs/2605.21768) | LoGo-GRPO combines global trajectory reward with local rerollouts from the same intermediate memory state; a shared extractor/manager learns insert, update, and delete operations under an 8→16→32-session curriculum | Makes group comparison fair after memory states diverge, at the cost of state checkpointing and local rerollouts |
| [CompactionRL](https://arxiv.org/abs/2607.05378) | Treats summary generation as part of the rollout, jointly optimizes summary and execution tokens with the task reward, token-normalized loss, and cross-trajectory GAE over compacted segments | Trains context compaction rather than using it only at inference; summary and action credit still share the terminal reward |
| [Proactive Memory Agent](https://arxiv.org/abs/2607.08716) | A separate memory policy maintains structured execution state and chooses whether to inject a reminder or remain silent; the open-weight variant is trained with SFT then GRPO | Adjacent to CA: exposes `intervene` and `remain silent` as learnable long-delay decisions, but does not yet provide explicit provenance credit to each intervention |
| [SWE-TRACE](https://arxiv.org/abs/2604.14820) | Trains a rubric PRM for repository-agent steps, combines its process reward with memory-augmented RL, and reuses it to prune weak actions during test-time search | Targets realistic SWE horizons; depends on rubric/judge calibration and executable training infrastructure |
| [DeltaBelief-RL](https://arxiv.org/abs/2602.12342) | Uses the change in the policy's probability of the target solution after each interaction as an intrinsic intermediate reward | Can learn from informative progress before success; belief change may be miscalibrated or increase for spurious evidence |
| [CARL](https://arxiv.org/abs/2512.04949) | Uses action entropy to identify critical states, expands them in a rollout tree, computes edge advantage as child minus parent expected return, and excludes low-criticality actions from updates | Focuses rollout and gradient compute on pivotal decisions; entropy is only a proxy for causal criticality |
| [SPA-RL](https://arxiv.org/abs/2505.20732) | Trains a progress estimator whose step contributions sum to terminal completion, combines each contribution with an action-grounding signal, and uses the redistributed rewards in policy optimization | Dense and inexpensive at policy time, but learned progress can differ from causal contribution |
| [CriticSearch](https://arxiv.org/abs/2511.12159) | A frozen asymmetric critic sees the full completed trace and gold answer, retrospectively scores each search turn, and supplies dense rewards for policy training | No extra environment rollout; depends on privileged answers and critic robustness |

[STRACE](https://arxiv.org/abs/2607.07702) is tracked under [Analysis](papers/analysis.md), not in this training table: it builds a textual dependency graph and backward causal slice to diagnose long failure traces for reflective agent optimization, but does not itself define a token-policy credit estimator.


### Practical reading of the design space

- If intermediate prefixes can be reset cheaply, MC methods such as VinePPO and SPO provide direct value estimates at the price of extra rollouts.
- If every intermediate decision is objectively checkable, VPR or SCRL gives the cleanest signal; PURE is useful when a trained PRM is available.
- If rollout groups repeatedly visit comparable states, GiGPO, HGPO, ProxMO, and GraphGPO extract finer credit without training a critic. Their main difference is how they define a comparable state: exact state, state plus history, soft textual proximity, or graph connectivity.
- If intermediate actions are not verifiable, the main options are learned critics (ArCHer, HiPER, AgentPRM), privileged or generative judges (SWEET-RL, CAPO, TRIAGE), and hindsight scoring (HCAPO). These replace verifier cost with model bias and additional inference.
- For very long tasks with explicit subgoals, hierarchical credit (HiPER, ArCHer) is more structurally appropriate than assigning an independent score to every token or turn.
- For bounded-context or cross-session tasks, ECHO, Memory-R2, and CompactionRL explicitly make evidence provenance, memory-state divergence, or summaries part of the learning problem. A standard trajectory advantage does not account for those state changes.
- For expensive deep-search rollouts, PivoARL and IGRPO spend additional interaction budget only near an estimated error boundary or high-information node; PBSD instead densifies credit without rerollout by using a verified answer during training.
- For asynchronous long-horizon training pipelines, SAO is the relevant systems paper: it gives up group-relative credit and uses a value model plus strict token-level off-policy filtering so each completed rollout can train immediately.

These papers use different models, rollout budgets, environments, and baselines, so their reported gains are not a controlled leaderboard. In particular, a method that spends extra compute on continuations or judge calls should be compared against a baseline with both matched policy updates and matched wall-clock or rollout budget. The linked primary papers are the source of the mechanism summaries above; the broader, lower-evidence method surface remains in [`papers/taxonomy.md`](papers/taxonomy.md).

---

## Featured (major labs)

| Date | Work | Org | Links | OSS |
|------|------|-----|-------|-----|
| 2026-06 | **GLM-5.2** | Zhipu AI | [📄 Paper/Blog](https://huggingface.co/blog/zai-org/glm-52-blog) · [💻 Weights](https://huggingface.co/zai-org) · 📦 — | ✅ |
| 2026-05 | **GraphGPO** | NTU | [📄 Paper](https://arxiv.org/abs/2605.26684) · [💻 Code](https://github.com/langfengQ/verl-agent/tree/master/recipe/GraphGPO) · [📦 ALFWorld](https://github.com/alfworld/alfworld) · [📦 WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| 2026-05 | **DelTA** | RUC | [📄 Paper](https://arxiv.org/abs/2605.21467) · [💻 Code](https://github.com/RUCBM/DelTA) · 📦 — *(online RLVR)* | ✅ |
| 2026-03 | **HCAPO** | NJU | [📄 Paper](https://arxiv.org/abs/2603.08754) · 💻 — · [📦 ALFWorld](https://github.com/alfworld/alfworld) · [📦 WebShop](https://github.com/princeton-nlp/WebShop) | ◐ |
| 2026-02 | **HGPO** | NTU | [📄 Paper](https://arxiv.org/abs/2602.22817) · [💻 Code](https://github.com/langfengQ/verl-agent/tree/master/recipe/hgpo) · [📦 ALFWorld](https://github.com/alfworld/alfworld) · [📦 WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| 2026-02 | **HiPER** | Northwestern / UMN / Cisco | [📄 Paper](https://arxiv.org/abs/2602.16165) · [💻 Code](https://github.com/JonP07/HiPER-agent) · [📦 ALFWorld](https://github.com/alfworld/alfworld) · [📦 WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| 2025-05 | **GiGPO** (NeurIPS 2025) | NTU / Skywork | [📄 Paper](https://arxiv.org/abs/2505.10978) · [💻 Code](https://github.com/langfengQ/verl-agent) · [📦 ALFWorld](https://github.com/alfworld/alfworld) · [📦 WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| 2025-04 | **Who&When** (ICML 2025) | Microsoft et al. | [📄 Paper](https://arxiv.org/abs/2505.00212) · [💻 Code](https://github.com/ag2ai/Agents_Failure_Attribution) · [📦 Data](https://huggingface.co/datasets/Kevin355/Who_and_When) | ✅ |
| 2025-03 | **SWEET-RL** | Meta / FAIR | [📄 Paper](https://arxiv.org/abs/2503.15478) · [💻 Code](https://github.com/facebookresearch/sweet_rl) · [📦 ColBench](https://github.com/facebookresearch/sweet_rl) | ✅ |
| 2025-01 | **DeepSeek-R1** | DeepSeek | [📄 Paper](https://arxiv.org/abs/2501.12948) · [💻 Models](https://huggingface.co/deepseek-ai) · 📦 — | ✅ |
| 2025-01 | **PRIME** | PRIME-RL | [📄 Paper](https://arxiv.org/abs/2502.01456) · [💻 Code](https://github.com/PRIME-RL/PRIME) · [📦 HF](https://huggingface.co/PRIME-RL) | ✅ |
| 2024-10 | **VinePPO** (ICML 2025) | McGill | [📄 Paper](https://arxiv.org/abs/2410.01679) · [💻 Code](https://github.com/McGill-NLP/VinePPO) · [📦 MATH/GSM8K](https://github.com/McGill-NLP/VinePPO) | ✅ |
| 2024-02 | **ArCHer** (ICML 2024) | Berkeley et al. | [📄 Paper](https://arxiv.org/abs/2402.19446) · [💻 Code](https://github.com/YifeiZhou02/ArCHer) · [📦 Envs](https://github.com/YifeiZhou02/ArCHer) | ✅ |
| 2024-02 | **GRPO / DeepSeekMath** | DeepSeek | [📄 Paper](https://arxiv.org/abs/2402.03300) · [💻 PDF](https://arxiv.org/pdf/2402.03300) · 📦 — | ✅ |

**Infra:** [💻 veRL](https://github.com/volcengine/verl) · [💻 verl-agent](https://github.com/langfengQ/verl-agent) · [💻 OpenRLHF](https://github.com/OpenRLHF/OpenRLHF) · [💻 ProxMO-RL](https://github.com/FlyTune/ProxMO-RL)

---

## Recently added / gap-fill

Pulled from citation neighborhoods of GiGPO / VinePPO / survey / Meta–Berkeley lines:

| Date | Work | Org | Links | OSS |
|------|------|-----|-------|-----|
| 2026-07 | **SAO** | Tsinghua / Z.AI | [📄 Paper](https://arxiv.org/abs/2607.07508) · 💻 — · [📦 SWE-bench Verified](https://www.swebench.com/) | ◐ |
| 2026-06 | **TRIAGE** | — | [📄 Paper](https://arxiv.org/abs/2606.32017) · 💻 — · [📦 ALFWorld](https://github.com/alfworld/alfworld) · [📦 WebShop](https://github.com/princeton-nlp/WebShop) | ◐ |
| 2026-06 | **QVal** | U. Tübingen | [📄 Paper](https://arxiv.org/abs/2606.32034) · 💻 — · 📦 — | ◐ |
| 2026-02 | **ProxMO** | — | [📄 Paper](https://arxiv.org/abs/2602.19225) · [💻 Code](https://github.com/FlyTune/ProxMO-RL) · [📦 ALFWorld](https://github.com/alfworld/alfworld) · [📦 WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| 2025-11 | **AgentPRM** | Fudan / Ant | [📄 Paper](https://arxiv.org/abs/2511.08325) · 💻 — · 📦 — | ◐ |
| 2025-08 | **CAPO** | — | [📄 Paper](https://arxiv.org/abs/2508.02298) · [💻 Code](https://github.com/andyclsr/CAPO) · 📦 — | ✅ |
| 2025-05 | **SPO** | — | [📄 Paper](https://arxiv.org/abs/2505.23564) · [💻 Code](https://github.com/AIFrameResearch/SPO) · 📦 — | ✅ |
| 2025-04 | **PURE** | — | [📄 Paper](https://arxiv.org/abs/2504.15275) · [💻 Code](https://github.com/CJReinforce/PURE) · [📦 PRM800K](https://github.com/openai/prm800k) | ✅ |
| 2026 | **C3** | — | [📄 Search](https://arxiv.org/search/?query=Contextual+Counterfactual+Credit+Assignment+C3+LLM) · [💻 Code](https://github.com/EIT-EAST-Lab/C3) · 📦 — | ✅ |

→ [`papers/catalog.md`](papers/catalog.md) · [`papers/taxonomy.md`](papers/taxonomy.md)

---

## Awesome cross-check

Compared against [xxzcc/Awesome-Credit-Assignment-in-LLM-RL](https://github.com/xxzcc/Awesome-Credit-Assignment-in-LLM-RL) on 2026-07-14. Missing or less-covered items are tracked in [`papers/awesome_gap_fill.md`](papers/awesome_gap_fill.md). Foundational/background papers are separated from training methods so the main tables stay focused on methods that change model training or credit routing.

---

## Taxonomy map

| Regime | Granularity | Examples |
|--------|-------------|---------|
| Reasoning RL | Token / Segment / Step | VinePPO, SPO, PURE, CAPO, DelTA, PRIME |
| Agentic RL | Step / Turn / Edge / Hierarchy / Memory | GiGPO, G2PO, PBSD, ECHO, Memory-R2, CompactionRL, HiPER, TRIAGE, SAO |

| Methodology | Examples |
|-------------|---------|
| Monte Carlo / group | GRPO, VinePPO, GiGPO, SPO |
| TD / critic / GAE | PPO, AgentPRM, GLM-5.2 critic PPO, SAO, SWEET-RL |
| LLM-as-critic / GenPRM | CAPO, HCAPO, TRIAGE |
| Hindsight / counterfactual / graph | HCAPO, PBSD, CRAFT, PivoARL, GraphGPO, G2PO |
| Process / verifiable / implicit PRM | PURE, PRIME, PRM800K, VPR |
| Hierarchical | HiPER, ArCHer |
| Belief / information-directed | DeltaBelief-RL, IGRPO, CARL |
| Memory / provenance / compaction | ECHO, Memory-R2, CompactionRL, Proactive Memory Agent |

→ [`papers/taxonomy.md`](papers/taxonomy.md)

---

The full training and catalog tables include `Org`, `Base model`, `Train data`, and `Eval data` columns for the main experiments.

## Training

→ [`papers/training.md`](papers/training.md)

---

## Evaluation

→ [`papers/evaluation.md`](papers/evaluation.md) · [`resources/benchmarks.md`](resources/benchmarks.md)

| Priority | Resource | Links |
|----------|----------|-------|
| P0 | ALFWorld | [📦](https://github.com/alfworld/alfworld) |
| P0 | WebShop | [📦](https://github.com/princeton-nlp/WebShop) |
| P0 | ColBench | [📦 sweet_rl](https://github.com/facebookresearch/sweet_rl) |
| P1 | Who&When | [📄](https://arxiv.org/abs/2505.00212) · [💻](https://github.com/ag2ai/Agents_Failure_Attribution) · [📦](https://huggingface.co/datasets/Kevin355/Who_and_When) |
| P1 | QVal | [📄](https://arxiv.org/abs/2606.32034) |
| P2 | PRM800K | [📦](https://github.com/openai/prm800k) |

---

## Analysis & surveys

→ [`papers/analysis.md`](papers/analysis.md)

| Date | Work | Links | OSS |
|------|------|-------|-----|
| 2026-06 | GLM-5.2 | [📄 Blog](https://huggingface.co/blog/zai-org/glm-52-blog) · [💻 Weights](https://huggingface.co/zai-org) | ✅ |
| 2026-04 | Survey | [📄 arXiv:2604.09459](https://arxiv.org/abs/2604.09459) | ✅ |

---

## Default stacks (2026)

| Piece | Choice | Links |
|-------|--------|-------|
| Benches | ALFWorld + WebShop | [ALFWorld](https://github.com/alfworld/alfworld) · [WebShop](https://github.com/princeton-nlp/WebShop) |
| Policy | Qwen2.5-Inst 1.5B/7B (+ Qwen3 transfer) | Model Hub |
| Trainer | veRL / verl-agent | [veRL](https://github.com/volcengine/verl) · [verl-agent](https://github.com/langfengQ/verl-agent) |
| Baseline | GRPO + GiGPO | [GiGPO](https://github.com/langfengQ/verl-agent) |

→ [`resources/stacks.md`](resources/stacks.md) · [`resources/models.md`](resources/models.md)

---

## Full catalog

→ [`papers/catalog.md`](papers/catalog.md)

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Prefer PRs that add/fix **📄 Paper · 💻 Code · 📦 Data**.
