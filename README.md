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

- [Featured (major labs)](#featured-major-labs)
- [Recently added / gap-fill](#recently-added--gap-fill)
- [Taxonomy](#taxonomy-map) → [`papers/taxonomy.md`](papers/taxonomy.md)
- [Training](#training) → [`papers/training.md`](papers/training.md)
- [Evaluation](#evaluation) → [`papers/evaluation.md`](papers/evaluation.md)
- [Analysis](#analysis--surveys) → [`papers/analysis.md`](papers/analysis.md)
- [Default stacks](#default-stacks-2026)
- [Full catalog](#full-catalog) → [`papers/catalog.md`](papers/catalog.md)

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

## Taxonomy map

| Regime | Granularity | Examples |
|--------|-------------|---------|
| Reasoning RL | Token / Segment / Step | VinePPO, SPO, PURE, CAPO, DelTA, PRIME |
| Agentic RL | Step / Turn / Hierarchy / Multi-agent | GiGPO, HGPO, GraphGPO, HiPER, HCAPO, SWEET-RL, ArCHer, TRIAGE, C3 |

| Methodology | Examples |
|-------------|---------|
| Monte Carlo / group | GRPO, VinePPO, GiGPO, SPO |
| TD / critic / GAE | PPO, AgentPRM, GLM-5.2 critic PPO, SWEET-RL |
| LLM-as-critic / GenPRM | CAPO, HCAPO, TRIAGE |
| Hindsight / counterfactual / graph | HCAPO, C3, CRAFT, GraphGPO |
| Process / verifiable / implicit PRM | PURE, PRIME, PRM800K, VPR |
| Hierarchical | HiPER, ArCHer |

→ [`papers/taxonomy.md`](papers/taxonomy.md)

---

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
