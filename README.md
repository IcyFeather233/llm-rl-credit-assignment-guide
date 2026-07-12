# LLM RL Credit Assignment Guide

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

A curated, **self-contained** guide to **credit assignment (CA) in reinforcement learning for LLMs** (2024–2026).

This repository aims to cover the field end-to-end:

1. **Taxonomy coverage** — granularity × methodology (reasoning & agentic)  
2. **Reproducibility** — Paper · Code · Data · OSS on every practical entry  
3. **Navigation** — Training / Evaluation / Analysis, date-descending, major labs featured first  

Survey reference: [From Reasoning to Agentic: CA in RL for LLMs](https://arxiv.org/abs/2604.09459) (arXiv:2604.09459).

**Legend:** `✅` open code/weights · `◐` partial · `❌` not found · `—` N/A

---

## Contents

- [Featured (major labs)](#featured-major-labs)
- [Taxonomy map](#taxonomy-map) → [`papers/taxonomy.md`](papers/taxonomy.md)
- [Training](#training) → [`papers/training.md`](papers/training.md)
- [Evaluation](#evaluation) → [`papers/evaluation.md`](papers/evaluation.md)
- [Analysis & surveys](#analysis--surveys) → [`papers/analysis.md`](papers/analysis.md)
- [Default stacks](#default-stacks-2026)
- [Full catalog](#full-catalog) → [`papers/catalog.md`](papers/catalog.md)
- [Contributing](CONTRIBUTING.md)

---

## Featured (major labs)

| Date | Work | Org | Code | Data / env | OSS |
|------|------|-----|------|------------|-----|
| 2026-06 | [GLM-5.2](https://huggingface.co/blog/zai-org/glm-52-blog) | **Zhipu AI** | [HF](https://huggingface.co/zai-org) | long-horizon coding agents | ✅ weights |
| 2026-05 | [DelTA](https://arxiv.org/abs/2605.21467) | **RUC** | [RUCBM/DelTA](https://github.com/RUCBM/DelTA) | math RLVR | ✅ |
| 2026-05 | [GraphGPO](https://arxiv.org/abs/2605.26684) | **NTU** | verl-agent ecosystem | agentic rollouts | ◐ |
| 2026-02 | [HGPO](https://arxiv.org/abs/2602.22817) | **NTU** | [hgpo recipe](https://github.com/langfengQ/verl-agent/tree/master/recipe/hgpo) | [ALFWorld](https://github.com/alfworld/alfworld) · [WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| 2026-02 | [HiPER](https://arxiv.org/abs/2602.16165) | **Northwestern / UMN / Cisco** | [HiPER-agent](https://github.com/JonP07/HiPER-agent) | ALFWorld · WebShop | ✅ |
| 2026-02 | [HCAPO](https://arxiv.org/abs/2603.08754) | **NJU** | partial | ALFWorld · WebShop | ◐ |
| 2025-05 | [GiGPO](https://arxiv.org/abs/2505.10978) | **NTU / Skywork** | verl-agent | ALFWorld · WebShop | ✅/◐ |
| 2025-04 | [Who&When](https://arxiv.org/abs/2505.00212) | **Microsoft** et al. | [code](https://github.com/ag2ai/Agents_Failure_Attribution) | [HF data](https://huggingface.co/datasets/Kevin355/Who_and_When) | ✅ |
| 2025-01 | [DeepSeek-R1](https://arxiv.org/abs/2501.12948) | **DeepSeek** | reports / models | outcome RL | ✅/◐ |
| 2024-10 | [VinePPO](https://arxiv.org/abs/2410.01679) | **McGill** | [VinePPO](https://github.com/McGill-NLP/VinePPO) | MATH · GSM8K | ✅ |
| 2024-02 | [GRPO / DeepSeekMath](https://arxiv.org/abs/2402.03300) | **DeepSeek** | report | MATH-style | ✅ |

Infra: [veRL](https://github.com/volcengine/verl) · [verl-agent](https://github.com/langfengQ/verl-agent) · [OpenRLHF](https://github.com/OpenRLHF/OpenRLHF)

---

## Taxonomy map

Two regimes × two axes (aligned with the 2026 survey):

| Regime | Granularity | Typical methods (examples) |
|--------|-------------|----------------------------|
| **Reasoning RL** | Token / Segment / Step | VinePPO, SPO, PURE, CAPO, DelTA, GRPO-λ |
| **Agentic RL** | Step / Turn / Hierarchy / Multi-agent | GiGPO, HGPO, HiPER, HCAPO, TRIAGE, C3, SWEET-RL |

| Methodology | Examples |
|-------------|---------|
| Monte Carlo / group | GRPO, VinePPO, GiGPO, SPO |
| TD / critic / GAE | PPO, AgentPRM, GLM-5.2 critic PPO |
| LLM-as-critic / GenPRM | CAPO, HCAPO, TRIAGE judge |
| Hindsight / counterfactual | HCAPO, C3, CRAFT, GraphGPO |
| Process / verifiable | PURE, PRM800K, VPR |
| Hierarchical | HiPER, ArCHer, HICRA |
| Info-theoretic / entropy | AEM, T²PO, PAPO |

Full indexed tables (with Code/Data/OSS): **[`papers/taxonomy.md`](papers/taxonomy.md)**

---

## Training

→ [`papers/training.md`](papers/training.md) (date-desc, with Code/Data/OSS)

Themes: critic-free group RL → hierarchical/hindsight → role/token reweight → industrial long-horizon critic PPO.

---

## Evaluation

→ [`papers/evaluation.md`](papers/evaluation.md) · [`resources/benchmarks.md`](resources/benchmarks.md)

| Priority | Resource | Link |
|----------|----------|------|
| P0 | ALFWorld | https://github.com/alfworld/alfworld |
| P0 | WebShop | https://github.com/princeton-nlp/WebShop |
| P1 | Who&When | https://huggingface.co/datasets/Kevin355/Who_and_When |
| P1 | QVal | https://arxiv.org/abs/2606.32034 |
| P2 | MATH / GSM8K / PRM800K | HF / https://github.com/openai/prm800k |

---

## Analysis & surveys

→ [`papers/analysis.md`](papers/analysis.md)

| Date | Work | Org | Links | OSS |
|------|------|-----|-------|-----|
| 2026-06 | GLM-5.2 | Zhipu | [blog](https://huggingface.co/blog/zai-org/glm-52-blog) | ✅ weights |
| 2026-04 | Reasoning→Agentic CA survey | — | [arXiv:2604.09459](https://arxiv.org/abs/2604.09459) | ✅ paper |

---

## Default stacks (2026)

| Piece | Common choice |
|-------|---------------|
| Agentic benches | ALFWorld + WebShop |
| Comparable policy | Qwen2.5-Instruct 1.5B / 7B |
| Newer policy | Qwen3-Instruct (e.g. 1.7B) + Qwen3-8B-thinking judge |
| Trainer | veRL / verl-agent / ProxMO-RL |
| Baselines | GRPO + GiGPO |
| Data shape | Online rollouts + verifier (rarely a dedicated CA dataset) |

→ [`resources/stacks.md`](resources/stacks.md) · [`resources/models.md`](resources/models.md)

---

## Full catalog

Complete date-sorted catalog across training / evaluation / analysis / long-tail:  
→ **[`papers/catalog.md`](papers/catalog.md)**

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). New entries should include **Paper + Code + Data + OSS** whenever possible.
