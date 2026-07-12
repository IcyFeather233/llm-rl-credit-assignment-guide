# Models commonly used as policies

| Track | Model | Where it appears | Role |
|-------|-------|------------------|------|
| Comparable agentic tables | Qwen2.5-Instruct **1.5B / 7B** (sometimes 3B) | GiGPO, HGPO, HiPER, HCAPO, ProxMO | Policy |
| 2026 newer track | Qwen3-Instruct (**1.7B** in TRIAGE) | TRIAGE | Policy |
| Judge / GenPRM | Qwen3-**8B-thinking**; Qwen2.5-**14B/32B** | TRIAGE; CAPO | Auxiliary |
| Reasoning Math | Qwen2.5-Math-7B | PURE | Policy |
| RLVR scaling | Qwen3-8B/14B-Base | DelTA | Policy |
| Industrial | GLM-5.x MoE | GLM-5.2 | Production long-horizon |

**Tip:** keep a Qwen2.5 table for literature comparability; add a Qwen3 run if claiming 2026 relevance.
