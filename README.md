# LLM RL Credit Assignment — Field Guide

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

A **practical field guide** to credit assignment (CA) in reinforcement learning for large language models: what major labs ship, what is reproducible, which benchmarks/models people actually use, and where the code/data live.

> **Not a taxonomy dump.** For the exhaustive granularity × methodology living list and survey companion, see  
> **[xxzcc/Awesome-Credit-Assignment-in-LLM-RL](https://github.com/xxzcc/Awesome-Credit-Assignment-in-LLM-RL)** ([survey arXiv:2604.09459](https://arxiv.org/abs/2604.09459)).  
> This repo is complementary: **reproducibility-first**, **org-first**, **date-desc**, with explicit **Paper / Code / Data / OSS** columns.

---

## How this differs from the Awesome taxonomy list

| | [Awesome-CA (xxzcc)](https://github.com/xxzcc/Awesome-Credit-Assignment-in-LLM-RL) | **This field guide** |
|--|--|--|
| Goal | Complete method catalog + survey taxonomy | Reproduce / compare / ship experiments |
| Primary axis | Granularity × methodology | **Training / Evaluation / Analysis** |
| Ordering | Taxonomy sections + recent additions | **Date descending**; **major orgs first on the homepage** |
| Entry fields | Paper + short relevance note | **Paper · Code · Data · OSS · Model · Bench · Org** |
| Coverage | Broad (47+ methods, near-core, adjacent) | Curated core + pointers; long-tail in Further reading |
| Extra | Taxonomy figures | Default stacks, model tracks, “what to run first” |

---

## Contents

- [Featured: major labs & venues](#featured-major-labs--venues)
- [Training methods](#training-methods) → [`papers/training.md`](papers/training.md)
- [Evaluation & benchmarks](#evaluation--benchmarks) → [`papers/evaluation.md`](papers/evaluation.md)
- [Analysis & surveys](#analysis--surveys) → [`papers/analysis.md`](papers/analysis.md)
- [Default stacks (2026)](#default-stacks-2026)
- [Further reading](#further-reading)
- [Contributing](CONTRIBUTING.md)

**Legend:** `✅` open code or weights · `◐` partial / unofficial · `❌` not found / closed · `—` not applicable

---

## Featured: major labs & venues

Homepage highlights only **well-known orgs / strong venues**. Full tables (with code & data links) live under `papers/`.

| Date | Work | Org | Type | Code | Data / env | OSS |
|------|------|-----|------|------|------------|-----|
| 2026-06 | [GLM-5.2](https://huggingface.co/blog/zai-org/glm-52-blog) | **Zhipu AI** | Analysis / industrial | [HF weights](https://huggingface.co/zai-org) | long-horizon coding agents | ✅ weights (MIT) |
| 2026-05 | [DelTA](https://arxiv.org/abs/2605.21467) | **RUC** | Training (RLVR token CA) | [RUCBM/DelTA](https://github.com/RUCBM/DelTA) | math RLVR online | ✅ |
| 2026-05 | [GraphGPO](https://arxiv.org/abs/2605.26684) | **NTU** | Training (agentic) | watch verl-agent ecosystem | agentic rollouts | ◐ |
| 2026-02 | [HGPO](https://arxiv.org/abs/2602.22817) | **NTU** | Training (agentic) | [verl-agent/hgpo](https://github.com/langfengQ/verl-agent/tree/master/recipe/hgpo) | [ALFWorld](https://github.com/alfworld/alfworld) · [WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| 2026-02 | [HiPER](https://arxiv.org/abs/2602.16165) (ICML 2026) | **Northwestern / UMN / Cisco** | Training (hierarchical) | [JonP07/HiPER-agent](https://github.com/JonP07/HiPER-agent) | ALFWorld · WebShop | ✅ |
| 2026-02 | [HCAPO](https://arxiv.org/abs/2603.08754) | **NJU** | Training (hindsight) | partial scripts | ALFWorld · WebShop · search QA | ◐ |
| 2025-05 | [GiGPO](https://arxiv.org/abs/2505.10978) (NeurIPS 2025) | **NTU / Skywork** | Training (strong baseline) | verl-agent ecosystem | ALFWorld · WebShop · search QA | ✅/◐ |
| 2025-04 | [Who&When](https://arxiv.org/abs/2505.00212) (ICML 2025) | **Microsoft** et al. | Evaluation | [Agents_Failure_Attribution](https://github.com/ag2ai/Agents_Failure_Attribution) | [Who_and_When](https://huggingface.co/datasets/Kevin355/Who_and_When) | ✅ |
| 2025-01 | [DeepSeek-R1](https://arxiv.org/abs/2501.12948) | **DeepSeek** | Training / industrial | model cards / reports | outcome RL at scale | ✅/◐ |
| 2024-10 | [VinePPO](https://arxiv.org/abs/2410.01679) (ICML 2025) | **McGill** | Training (reasoning) | [McGill-NLP/VinePPO](https://github.com/McGill-NLP/VinePPO) | MATH · GSM8K | ✅ |
| 2024-02 | [DeepSeekMath / GRPO](https://arxiv.org/abs/2402.03300) | **DeepSeek** | Training (foundation) | reports | MATH-style verifiable | ✅ reports |

Also foundational for practitioners: **veRL** ([volcengine/verl](https://github.com/volcengine/verl)), **verl-agent**, OpenRLHF.

---

## Training methods

Curated training/method papers with **Paper · Code · Data · OSS · Model · Bench · Org**, newest first:  
→ **[`papers/training.md`](papers/training.md)**

Major themes in 2025–2026:

1. **Critic-free group RL** (GRPO → GiGPO / HGPO) for agents  
2. **Hierarchical / hindsight / soft baselines** (HiPER, HCAPO, ProxMO)  
3. **Role / process / token reweighting** (TRIAGE, DelTA, PRM lines)  
4. **Industrial long-horizon** (GLM-5.2 critic PPO + compaction)

---

## Evaluation & benchmarks

→ **[`papers/evaluation.md`](papers/evaluation.md)** · **[`resources/benchmarks.md`](resources/benchmarks.md)**

| Priority | Resource | Link | Notes |
|----------|----------|------|-------|
| P0 | ALFWorld | https://github.com/alfworld/alfworld | Default agentic CA env |
| P0 | WebShop | https://github.com/princeton-nlp/WebShop | Paired with ALFWorld in most papers |
| P1 | Who&When | https://huggingface.co/datasets/Kevin355/Who_and_When | Failure attribution |
| P1 | QVal | https://arxiv.org/abs/2606.32034 | Dense-signal diagnostics |
| P2 | MATH / GSM8K / PRM800K | HF / OpenAI | Reasoning CA |

---

## Analysis & surveys

→ **[`papers/analysis.md`](papers/analysis.md)**

| Date | Work | Org | Code / list | OSS |
|------|------|-----|-------------|-----|
| 2026-06 | GLM-5.2 blog | Zhipu AI | [blog](https://huggingface.co/blog/zai-org/glm-52-blog) · [weights](https://huggingface.co/zai-org) | ✅ weights |
| 2026-04 | Reasoning→Agentic CA survey | — | [arXiv](https://arxiv.org/abs/2604.09459) · [Awesome list](https://github.com/xxzcc/Awesome-Credit-Assignment-in-LLM-RL) | ✅ list |

---

## Default stacks (2026)

| Piece | Common choice | Why |
|-------|---------------|-----|
| Agentic benches | ALFWorld + WebShop | Shared by NTU line & follow-ups |
| Comparable policy | Qwen2.5-Instruct 1.5B / 7B | Matches GiGPO→HGPO tables |
| Newer policy track | Qwen3-Instruct (e.g. 1.7B) + Qwen3-8B-thinking judge | Used in TRIAGE (2026-06) |
| Trainer | veRL / verl-agent / ProxMO-RL | Open recipes |
| Strong baseline | GRPO + **GiGPO** | What reviewers expect |
| Train data shape | Online env rollouts + verifier | Rare dedicated “CA datasets” |

Details: [`resources/stacks.md`](resources/stacks.md) · [`resources/models.md`](resources/models.md)

---

## Further reading

Long-tail / smaller-team / near-core papers (still useful, not homepage-front):  
→ [`papers/further_reading.md`](papers/further_reading.md)

For exhaustive taxonomy coverage, prefer the [xxzcc Awesome list](https://github.com/xxzcc/Awesome-Credit-Assignment-in-LLM-RL).

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Prefer PRs that add **working Code / Data / OSS** fields for major-org or highly cited works.
