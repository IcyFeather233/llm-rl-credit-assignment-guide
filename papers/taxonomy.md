# Taxonomy index (granularity × methodology)

Self-contained coverage of reasoning & agentic CA methods.  
Homepage features major labs; this file indexes the broader method space with **Paper · Code · Data · OSS** when known.

---

## Reasoning RL

### Token-level

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| [VinePPO](https://arxiv.org/abs/2410.01679) (ICML 2025) | MC vine | [McGill-NLP/VinePPO](https://github.com/McGill-NLP/VinePPO) | MATH, GSM8K | ✅ |
| [TACO](https://arxiv.org/abs/2607.07976) | Tail-risk positive-credit calibration | [xiuyilou/TACO](https://github.com/xiuyilou/TACO) | math/coding RLVR | ✅ |
| [DelTA](https://arxiv.org/abs/2605.21467) | Discriminative token | [RUCBM/DelTA](https://github.com/RUCBM/DelTA) | RLVR math | ✅ |
| [GRPO-λ](https://arxiv.org/abs/2510.00194) | λ-return / eligibility | — | math RLVR | ❌/◐ |
| [GRAIL](https://arxiv.org/abs/2606.04889) | Gradient-reweighted adv | — | RLVR | ❌/◐ |
| [SC-GRPO](https://arxiv.org/abs/2606.18810) | Self-conditioned token | — | RLVR | ❌/◐ |
| [OAR](https://arxiv.org/abs/2601.07408) | Outcome-grounded reshape | — | math | ❌/◐ |
| RED (2024) | Reward redistribution | — | preference / RM | ❌/◐ |
| T-REG (2024) | Token reward reg. | — | preference | ❌/◐ |
| From r to Q* (2024) | Implicit Q / DPO | — | preference | ✅ paper |

### Segment-level

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| [SPO](https://arxiv.org/abs/2505.23564) | Segment MC | [AIFrameResearch/SPO](https://github.com/AIFrameResearch/SPO) | math CoT | ✅ |
| SCAR (2025) | Shapley segments | — | math | ❌/◐ |
| TEMPO (2025) | Tree-TD | — | math | ❌/◐ |
| [SCRL](https://arxiv.org/abs/2605.22074) | Verifiable subproblems | — | math curriculum | ❌/◐ |

### Step-level / process

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| [PURE](https://arxiv.org/abs/2504.15275) | Min-form PRM | [CJReinforce/PURE](https://github.com/CJReinforce/PURE) | [PRM800K](https://github.com/openai/prm800k) | ✅ |
| [CAPO](https://arxiv.org/abs/2508.02298) | GenPRM / LLM-as-critic | [andyclsr/CAPO](https://github.com/andyclsr/CAPO) | math + OOD | ✅ |
| [PRIME](https://arxiv.org/abs/2502.01456) | Implicit process reward | [PRIME-RL/PRIME](https://github.com/PRIME-RL/PRIME) | [HF](https://huggingface.co/PRIME-RL) | ✅ |
| SPRO (2025) | Masked step advantage | — | math | ❌/◐ |
| ACPO (2025) | Attribution CA | — | RLVR | ❌/◐ |
| HICRA (2025) | Hierarchy on planning tokens | — | reasoning | ❌/◐ |
| PRL / InT / FinePO (2026) | Process / intervention / sub-step | — | reasoning | ❌/◐ |
| Math-Shepherd / OmegaPRM (2024) | PRM foundations | varies | math process labels | ✅/◐ |

---

## Agentic RL

### Critic-free step / group

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| [GiGPO](https://arxiv.org/abs/2505.10978) | Group-in-group | [verl-agent](https://github.com/langfengQ/verl-agent) | [ALFWorld](https://github.com/alfworld/alfworld), [WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| [HGPO](https://arxiv.org/abs/2602.22817) | Hierarchy-of-groups | [recipe/hgpo](https://github.com/langfengQ/verl-agent/tree/master/recipe/hgpo) | [ALFWorld](https://github.com/alfworld/alfworld), [WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| [GraphGPO](https://arxiv.org/abs/2605.26684) | Graph step credit | [recipe/GraphGPO](https://github.com/langfengQ/verl-agent/tree/master/recipe/GraphGPO) | [ALFWorld](https://github.com/alfworld/alfworld), [WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| [G2PO](https://arxiv.org/abs/2606.22995) | Global graph edge TD | [Nala-YN/G2PO](https://github.com/Nala-YN/G2PO) | ALFWorld, WebShop, AppWorld | ✅ |
| [ProGPO](https://arxiv.org/abs/2607.04242) | Exact-prefix peers + state potentials | — | ALFWorld, WebShop | ◐ |
| [CARL](https://arxiv.org/abs/2512.04949) | Entropy-selected tree-edge advantage | announced | search QA | ❌/◐ |
| [ProxMO](https://arxiv.org/abs/2602.19225) | Soft proximity baseline | [ProxMO-RL](https://github.com/FlyTune/ProxMO-RL) | [ALFWorld](https://github.com/alfworld/alfworld), [WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| [GRPO](https://arxiv.org/abs/2402.03300) | Trajectory group | [PDF](https://arxiv.org/pdf/2402.03300) | verifiable tasks | ✅ reports |

### Hierarchical

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| [HiPER](https://arxiv.org/abs/2602.16165) | Plan–execute + HAE | [HiPER-agent](https://github.com/JonP07/HiPER-agent) | [ALFWorld](https://github.com/alfworld/alfworld), [WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| [ArCHer](https://arxiv.org/abs/2402.19446) (ICML 2024) | Hierarchical multi-turn | [YifeiZhou02/ArCHer](https://github.com/YifeiZhou02/ArCHer) | [envs in repo](https://github.com/YifeiZhou02/ArCHer) | ✅ |
| [SWEET-RL](https://arxiv.org/abs/2503.15478) | Turn-level critic | [facebookresearch/sweet_rl](https://github.com/facebookresearch/sweet_rl) | [ColBench](https://github.com/facebookresearch/sweet_rl) | ✅ |

### Hindsight / counterfactual

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| [HCAPO](https://arxiv.org/abs/2603.08754) | Hindsight critic | — | [ALFWorld](https://github.com/alfworld/alfworld), [WebShop](https://github.com/princeton-nlp/WebShop) | ◐ |
| [PBSD](https://arxiv.org/abs/2606.09348) | Privileged Bayesian turn evidence | — | BrowseComp/search agents | ◐ |
| [PivoARL](https://arxiv.org/abs/2607.03702) | Pivotal-error local retry | [yuki-younai/PivoARL](https://github.com/yuki-younai/PivoARL) | agent tasks + search QA | ✅ |
| [C3](https://github.com/EIT-EAST-Lab/C3) | Multi-agent counterfactual | [EIT-EAST-Lab/C3](https://github.com/EIT-EAST-Lab/C3) | collab tasks | ✅ |
| CCPO (2026) | Counterfactual PO | — | multi-agent | ❌/◐ |
| [CRAFT](https://arxiv.org/abs/2606.29476) | Sibling counterfactual | — | agentic | ❌/◐ |
| [CriticSearch](https://arxiv.org/abs/2511.12159) | Privileged retrospective critic | — | search agents | ❌/◐ |

### Turn-level process / MDP

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| [TRIAGE](https://arxiv.org/abs/2606.32017) | Role-typed segment | — | [ALFWorld](https://github.com/alfworld/alfworld), [WebShop](https://github.com/princeton-nlp/WebShop) | ◐ |
| [SAO](https://arxiv.org/abs/2607.07508) | Single-rollout async critic/GAE + DIS clipping | — | SWE-bench Verified, BeyondAIME, IMOAnswerBench | ◐ |
| [SPA-RL](https://arxiv.org/abs/2505.20732) | Learned additive progress redistribution | [SPA-RL-Agent](https://github.com/WangHanLinHenry/SPA-RL-Agent) | ALFWorld, WebShop, VirtualHome | ✅ |
| [SWE-TRACE](https://arxiv.org/abs/2604.14820) | Rubric PRM + memory-augmented RL | — | SWE-bench Verified | ◐ |
| [VPR](https://arxiv.org/abs/2605.10325) | Verifiable process rewards | — | agentic | ❌/◐ |
| [AgentPRM](https://arxiv.org/abs/2511.08325) | TD+GAE turn PRM | — | agents | ◐ |
| [SWEET-RL](https://arxiv.org/abs/2503.15478) | Privileged critic | [sweet_rl](https://github.com/facebookresearch/sweet_rl) | [ColBench](https://github.com/facebookresearch/sweet_rl) | ✅ |
| Turn-PPO / SORL / TARL / ITPO | Turn MDP / off-policy / judge | — | multi-turn | ❌/◐ |
| AT²PO / A²TGPO (2026) | Turn tree / turn-group | — | multi-turn | ❌/◐ |
| GVPO / CLI A³ (2026) | Coding / CLI action credit | — | coding·CLI | ❌/◐ |

### Entropy / uncertainty / adjacent

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| [DeltaBelief-RL](https://arxiv.org/abs/2602.12342) | Target-belief shift intrinsic reward | [delta-belief-rl](https://github.com/bethgelab/delta-belief-rl) | synthetic interaction + OOD | ✅ |
| [IGRPO](https://arxiv.org/abs/2607.06223) | Information-gain-directed tree rollout | [e3trange/IGRPO](https://github.com/e3trange/IGRPO) | search QA | ✅ |
| AEM / T²PO / PAPO (2026) | Entropy / uncertainty | — | multi-turn | ❌/◐ |
| APPO / OPID / Progress Advantage (2026) | Procedure / skill / progress | — | agentic | ❌/◐ |

### Multi-agent

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| C3 | Counterfactual multi-agent | [C3](https://github.com/EIT-EAST-Lab/C3) | LLM collab | ✅ |
| CCPO / related MAS CA | Structural / Shapley-style | — | multi-agent | ❌/◐ |

### Memory / provenance / context

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| [ECHO](https://arxiv.org/abs/2606.31650) | Source-indexed evidence routing | [xiezijun714-lang/Echo](https://github.com/xiezijun714-lang/Echo) | BrowseComp-Plus | ✅ |
| [Memory-R2](https://arxiv.org/abs/2605.21768) | Global + shared-memory-state local rerollout | [ahmedehabb/Memory-R2](https://github.com/ahmedehabb/Memory-R2) | LoCoMo, LongMemEval | ✅ |
| [CompactionRL](https://arxiv.org/abs/2607.05378) | Joint execution/summary RL + cross-trajectory GAE | — | SWE-bench Verified, Terminal-Bench 2.0 | ◐ |
| [Proactive Memory Agent](https://arxiv.org/abs/2607.08716) | Learned reminder / silence intervention | [proactive-memory-agent](https://github.com/yifannnwu/proactive-memory-agent) | Terminal-Bench 2.0, tau2-bench | ✅ |

### Industrial long-horizon

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| [GLM-5.2](https://huggingface.co/blog/zai-org/glm-52-blog) | Critic PPO + compaction | [HF weights](https://huggingface.co/zai-org) | SWE/Terminal-style | ✅ weights |
| [SAO](https://arxiv.org/abs/2607.07508) | Asynchronous single-rollout RL deployed in GLM-5.2 training | — | SWE-bench Verified, BeyondAIME, IMOAnswerBench | ◐ |
| [STRACE](https://arxiv.org/abs/2607.07702) *(analysis, not policy CA)* | Dependency graph + backward causal slice | [moomight/STRACE](https://github.com/moomight/STRACE) | VeruSAGE-Bench | ✅ |

---

## External awesome-list gap fill

Additional source-listed papers from [xxzcc/Awesome-Credit-Assignment-in-LLM-RL](https://github.com/xxzcc/Awesome-Credit-Assignment-in-LLM-RL) that were missing or only partially covered are tracked in [`awesome_gap_fill.md`](awesome_gap_fill.md). That file keeps foundational/background items separate from core training methods.

---

## Quick legend for missing code

Many 2026 preprints list methods without a public trainer yet. Marked `❌/◐` until a reproducible repo appears—PRs welcome to upgrade badges.
