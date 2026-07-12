# Evaluation & benchmarks

Date-descending where applicable. Prefer resources with **public data or env installers**.

## Evaluation papers

| Date | Paper | Org | What it evaluates | Links | OSS |
|------|-------|-----|-------------------|-------|-----|
| 2026-06 | [QVal](https://arxiv.org/abs/2606.32034) | U. Tübingen | Dense supervision vs reference Q | [📄 Paper](https://arxiv.org/abs/2606.32034) · 💻 — · 📦 — | ◐ |
| 2025-04 | [Who&When](https://arxiv.org/abs/2505.00212) (ICML 2025) | Microsoft et al. | Which agent failed and when | [📄 Paper](https://arxiv.org/abs/2505.00212) · [💻 Code](https://github.com/ag2ai/Agents_Failure_Attribution) · [📦 Data](https://huggingface.co/datasets/Kevin355/Who_and_When) | ✅ |

## Environment / task suites (widely used)

| Suite | Domain | Links | Typical use in CA papers | OSS |
|-------|--------|-------|--------------------------|-----|
| **ALFWorld** | Text embodied household | [📦](https://github.com/alfworld/alfworld) | Agentic CA default | ✅ |
| **WebShop** | Simulated shopping site | [📦](https://github.com/princeton-nlp/WebShop) | Agentic CA default | ✅ |
| **ColBench** | Collaborative SWE / multi-turn | [📦 sweet_rl](https://github.com/facebookresearch/sweet_rl) | SWEET-RL turn-level CA | ✅ |
| Search / multi-hop QA | Tool-use QA | TriviaQA / HotpotQA etc. | Optional third domain | ✅ datasets |
| MATH / GSM8K | Math reasoning | HF mirrors | Reasoning CA | ✅ |
| PRM800K | Process labels | [📦](https://github.com/openai/prm800k) | Train PRMs then RL | ✅ |
| SWE-bench family | Software engineering | [📦](https://www.swebench.com/) | Industrial long-horizon (e.g. GLM-5.2) | ✅/◐ |

See also [`../resources/benchmarks.md`](../resources/benchmarks.md).
