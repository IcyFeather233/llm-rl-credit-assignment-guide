# Training methods

Entries are **date-descending**. Homepage only features major orgs; this file is the working catalog.

Columns: **Date · Paper · Org · Model · Bench · Data/signal · Code · OSS**

---

## 2026

| Date | Paper | Org | Model | Bench | Data / signal | Code | OSS |
|------|-------|-----|-------|-------|---------------|------|-----|
| 2026-06 | [TRIAGE](https://arxiv.org/abs/2606.32017) | — | Qwen2.5-7B-Inst + **Qwen3-1.7B-Inst**; judge **Qwen3-8B-thinking** | ALFWorld, Search-QA, WebShop | GRPO rollouts + role-typed segment rewards | veRL-based; watch author release | ◐ |
| 2026-05 | [GraphGPO](https://arxiv.org/abs/2605.26684) | **NTU** | agentic LLM | agentic suite | rollout → state graph → step credit | verl-agent ecosystem | ◐ |
| 2026-05 | [DelTA](https://arxiv.org/abs/2605.21467) | **RUC** | Qwen3-8B/14B-Base (paper) | math RLVR (+ code OOD) | online RLVR + discriminative token coeffs | [RUCBM/DelTA](https://github.com/RUCBM/DelTA) | ✅ |
| 2026-03 | [HCAPO](https://arxiv.org/abs/2603.08754) | **NJU** | Qwen2.5-1.5B/7B-Inst | ALFWorld, WebShop, search QA | online RL + hindsight LLM critic | partial public scripts | ◐ |
| 2026-02 | [HGPO](https://arxiv.org/abs/2602.22817) | **NTU** | Qwen2.5-1.5B/7B-Inst | ALFWorld, WebShop | group RL; hierarchical groups | [recipe/hgpo](https://github.com/langfengQ/verl-agent/tree/master/recipe/hgpo) | ✅ |
| 2026-02 | [ProxMO](https://arxiv.org/abs/2602.19225) | — | Qwen2.5-1.5B/7B-Inst | ALFWorld, WebShop | online RL; soft proximity baseline | [FlyTune/ProxMO-RL](https://github.com/FlyTune/ProxMO-RL) | ✅ |
| 2026-02 | [HiPER](https://arxiv.org/abs/2602.16165) | **Northwestern / UMN / Cisco** | Qwen2.5-1.5B/7B-Inst | ALFWorld, WebShop | plan–execute + hierarchical advantage | [JonP07/HiPER-agent](https://github.com/JonP07/HiPER-agent) | ✅ |

Env upstream data:

- ALFWorld: https://github.com/alfworld/alfworld (`alfworld-download`)
- WebShop: https://github.com/princeton-nlp/WebShop

---

## 2025

| Date | Paper | Org | Model | Bench | Data / signal | Code | OSS |
|------|-------|-----|-------|-------|---------------|------|-----|
| 2025-08 | [CAPO](https://arxiv.org/abs/2508.02298) | — | Llama/Qwen; GenPRM Qwen2.5-14/32B | math + OOD | RLVR + generative step critiques | [andyclsr/CAPO](https://github.com/andyclsr/CAPO) | ✅ |
| 2025-05 | [SPO](https://arxiv.org/abs/2505.23564) | — | LLM CoT | math / CoT | segment advantage (MC-style) | [AIFrameResearch/SPO](https://github.com/AIFrameResearch/SPO) | ✅ |
| 2025-05 | [GiGPO](https://arxiv.org/abs/2505.10978) | **NTU / Skywork** | Qwen2.5-1.5B/3B/7B-Inst | ALFWorld, WebShop, search QA | episode + step group advantage | verl-agent | ✅/◐ |
| 2025-04 | [PURE](https://arxiv.org/abs/2504.15275) | — | Qwen2.5-Math-7B | MATH etc. | [PRM800K](https://github.com/openai/prm800k) + min-form process credit | [CJReinforce/PURE](https://github.com/CJReinforce/PURE) | ✅ |
| 2025-01 | [DeepSeek-R1](https://arxiv.org/abs/2501.12948) | **DeepSeek** | DeepSeek series | math/code at scale | large-scale outcome RL / GRPO-family | reports + model releases | ✅/◐ |

---

## 2024 foundations

| Date | Paper | Org | Model | Bench | Data / signal | Code | OSS |
|------|-------|-----|-------|-------|---------------|------|-----|
| 2024-10 | [VinePPO](https://arxiv.org/abs/2410.01679) | **McGill** | LLM reasoning | MATH, GSM8K | RL + MC rewind in language env | [McGill-NLP/VinePPO](https://github.com/McGill-NLP/VinePPO) | ✅ |
| 2024-02 | [DeepSeekMath (GRPO)](https://arxiv.org/abs/2402.03300) | **DeepSeek** | DeepSeekMath | MATH-style | group-relative trajectory advantage | technical report | ✅ reports |

Infrastructure often used with the above:

| Stack | Link | OSS |
|-------|------|-----|
| veRL | https://github.com/volcengine/verl | ✅ |
| verl-agent | https://github.com/langfengQ/verl-agent | ✅ |
| OpenRLHF | https://github.com/OpenRLHF/OpenRLHF | ✅ |
