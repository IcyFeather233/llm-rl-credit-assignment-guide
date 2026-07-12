# Benchmarks & datasets

| Name | Type | Link | Used for | OSS |
|------|------|------|----------|-----|
| ALFWorld | Env | https://github.com/alfworld/alfworld | Agentic CA | ✅ |
| WebShop | Env | https://github.com/princeton-nlp/WebShop | Agentic CA | ✅ |
| Who&When | Eval set | https://huggingface.co/datasets/Kevin355/Who_and_When | Failure attribution | ✅ |
| PRM800K | Process labels | https://github.com/openai/prm800k | PRM training | ✅ |
| MATH | Reasoning bench | HF / original release | Reasoning CA | ✅ |
| GSM8K | Reasoning bench | HF | Reasoning CA | ✅ |
| SWE-bench | Coding agents | https://www.swebench.com/ | Long-horizon industrial eval | ✅/◐ |

Install tips: keep **separate conda envs** for ALFWorld vs WebShop (WebShop often needs Python ≤3.10).
