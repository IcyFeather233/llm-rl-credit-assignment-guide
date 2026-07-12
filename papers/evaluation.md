# Evaluation & benchmarks

Date-descending where applicable. Prefer resources with **public data or env installers**.

## Evaluation papers

| Date | Paper | Org | What it evaluates | Data / code | OSS |
|------|-------|-----|-------------------|-------------|-----|
| 2026-06 | [QVal](https://arxiv.org/abs/2606.32034) | **U. Tübingen** (Bethge et al.) | Whether dense supervision aligns with reference Q | paper / protocol | ◐ |
| 2025-04 | [Who&When](https://arxiv.org/abs/2505.00212) (ICML 2025) | **Microsoft** et al. | Which agent failed and when | [Dataset](https://huggingface.co/datasets/Kevin355/Who_and_When) · [Code](https://github.com/ag2ai/Agents_Failure_Attribution) | ✅ |

## Environment / task suites (widely used)

| Suite | Domain | Link | Typical use in CA papers | OSS |
|-------|--------|------|--------------------------|-----|
| **ALFWorld** | Text embodied household | https://github.com/alfworld/alfworld | Agentic CA default | ✅ |
| **WebShop** | Simulated shopping site | https://github.com/princeton-nlp/WebShop | Agentic CA default | ✅ |
| Search / multi-hop QA | Tool-use QA | TriviaQA / HotpotQA etc. | Optional third domain | ✅ datasets |
| MATH / GSM8K | Math reasoning | HF mirrors | Reasoning CA | ✅ |
| PRM800K | Process labels | https://github.com/openai/prm800k | Train PRMs then RL | ✅ |
| SWE-bench family | Software engineering | https://www.swebench.com/ | Industrial long-horizon (e.g. GLM-5.2 narrative) | ✅/◐ harnesses |

See also [`../resources/benchmarks.md`](../resources/benchmarks.md).
