# Taxonomy index (granularity × methodology)

Self-contained coverage of reasoning & agentic CA methods.  
Homepage features major labs; this file indexes the broader method space with **Paper · Code · Data · OSS** when known.

---

## Reasoning RL

### Token-level

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| [VinePPO](https://arxiv.org/abs/2410.01679) (ICML 2025) | MC vine | [McGill-NLP/VinePPO](https://github.com/McGill-NLP/VinePPO) | MATH, GSM8K | ✅ |
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
| [GiGPO](https://arxiv.org/abs/2505.10978) | Group-in-group | verl-agent | ALFWorld, WebShop | ✅/◐ |
| [HGPO](https://arxiv.org/abs/2602.22817) | Hierarchy-of-groups | [hgpo](https://github.com/langfengQ/verl-agent/tree/master/recipe/hgpo) | ALFWorld, WebShop | ✅ |
| [GraphGPO](https://arxiv.org/abs/2605.26684) | Graph step credit | ◐ | agentic rollouts | ◐ |
| [ProxMO](https://arxiv.org/abs/2602.19225) | Soft proximity baseline | [ProxMO-RL](https://github.com/FlyTune/ProxMO-RL) | ALFWorld, WebShop | ✅ |
| [GRPO](https://arxiv.org/abs/2402.03300) | Trajectory group | DeepSeek reports | verifiable tasks | ✅ reports |

### Hierarchical

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| [HiPER](https://arxiv.org/abs/2602.16165) | Plan–execute + HAE | [HiPER-agent](https://github.com/JonP07/HiPER-agent) | ALFWorld, WebShop | ✅ |
| ArCHer (ICML 2024) | Hierarchical multi-turn | — | agent tasks | ❌/◐ |

### Hindsight / counterfactual

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| [HCAPO](https://arxiv.org/abs/2603.08754) | Hindsight critic | partial | ALFWorld, WebShop | ◐ |
| [C3](https://github.com/EIT-EAST-Lab/C3) | Multi-agent counterfactual | [EIT-EAST-Lab/C3](https://github.com/EIT-EAST-Lab/C3) | collab tasks | ✅ |
| CCPO (2026) | Counterfactual PO | — | multi-agent | ❌/◐ |
| [CRAFT](https://arxiv.org/abs/2606.29476) | Sibling counterfactual | — | agentic | ❌/◐ |
| CriticSearch (2025) | Retrospective critic | — | search agents | ❌/◐ |

### Turn-level process / MDP

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| [TRIAGE](https://arxiv.org/abs/2606.32017) | Role-typed segment | veRL-based | ALFWorld, Search-QA, WebShop | ◐ |
| [VPR](https://arxiv.org/abs/2605.10325) | Verifiable process rewards | — | agentic | ❌/◐ |
| AgentPRM (2025) | TD+GAE turn PRM | — | agents | ❌/◐ |
| SWEET-RL (2025) | Privileged critic | — | collaborative | ❌/◐ |
| Turn-PPO / SORL / TARL / ITPO | Turn MDP / off-policy / judge | — | multi-turn | ❌/◐ |
| AT²PO / A²TGPO (2026) | Turn tree / turn-group | — | multi-turn | ❌/◐ |
| GVPO / CLI A³ (2026) | Coding / CLI action credit | — | coding·CLI | ❌/◐ |

### Entropy / uncertainty / adjacent

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| AEM / T²PO / PAPO (2026) | Entropy / uncertainty | — | multi-turn | ❌/◐ |
| APPO / OPID / Progress Advantage (2026) | Procedure / skill / progress | — | agentic | ❌/◐ |

### Multi-agent

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| C3 | Counterfactual multi-agent | [C3](https://github.com/EIT-EAST-Lab/C3) | LLM collab | ✅ |
| CCPO / related MAS CA | Structural / Shapley-style | — | multi-agent | ❌/◐ |

### Industrial long-horizon

| Paper | Method tag | Code | Data | OSS |
|-------|------------|------|------|-----|
| [GLM-5.2](https://huggingface.co/blog/zai-org/glm-52-blog) | Critic PPO + compaction | [HF weights](https://huggingface.co/zai-org) | SWE/Terminal-style | ✅ weights |

---

## Quick legend for missing code

Many 2026 preprints list methods without a public trainer yet. Marked `❌/◐` until a reproducible repo appears—PRs welcome to upgrade badges.
