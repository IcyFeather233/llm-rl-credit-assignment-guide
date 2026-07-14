# Benchmarks & datasets

| Name | Type | Link | Used for | OSS |
|------|------|------|----------|-----|
| ALFWorld | Env | https://github.com/alfworld/alfworld | Agentic CA | ✅ |
| WebShop | Env | https://github.com/princeton-nlp/WebShop | Agentic CA | ✅ |
| AppWorld | Env | https://github.com/stonybrooknlp/appworld | Graph/state-transition CA in complex app APIs | ✅ |
| BrowseComp | Search QA | https://huggingface.co/datasets/openai/BrowseComp | PBSD long-context search evaluation | ✅ |
| BrowseComp-Plus | Search QA | https://arxiv.org/abs/2508.06600 | ECHO memory-provenance training/eval | ✅ paper |
| Terminal-Bench 2.0 | Terminal agents | https://www.tbench.ai/ | Compaction and proactive-memory long horizon | ✅ |
| tau2-bench | Conversational tools | https://github.com/sierra-research/tau2-bench | Memory intervention and state retention | ✅ |
| SWE-bench Verified | Coding agents | https://www.swebench.com/ | CompactionRL, SWE-TRACE, and SAO coding-agent evaluation | ✅/◐ |
| BeyondAIME | Math reasoning | https://arxiv.org/abs/2504.13914 | SAO reasoning evaluation | ◐ announced |
| MathArena / AIME-HMMT | Live math competitions | https://arxiv.org/abs/2505.23281 | SAO AIME2025 and HMMT Nov 2025 evaluation | ✅ paper |
| IMOAnswerBench | Math reasoning | https://imobench.github.io/ | SAO robust math evaluation | ✅/◐ |
| LoCoMo | Multi-session memory | https://arxiv.org/abs/2402.17753 | Memory-R2 8→16→32-session curriculum | ✅ paper |
| LongMemEval | Long-term memory | https://arxiv.org/abs/2410.10813 | Memory-R2 out-of-distribution evaluation | ✅ paper |
| VeruSAGE-Bench | Formal verification agents | https://github.com/moomight/STRACE | Long-trace root-cause diagnosis | ✅ |
| Who&When | Eval set | https://huggingface.co/datasets/Kevin355/Who_and_When | Failure attribution | ✅ |
| PRM800K | Process labels | https://github.com/openai/prm800k | PRM training | ✅ |
| MATH | Reasoning bench | HF / original release | Reasoning CA | ✅ |
| GSM8K | Reasoning bench | HF | Reasoning CA | ✅ |
| SWE-bench | Coding agents | https://www.swebench.com/ | Long-horizon industrial eval | ✅/◐ |

Install tips: keep **separate conda envs** for ALFWorld vs WebShop (WebShop often needs Python ≤3.10).
