# Notes on coverage

This directory intentionally covers both:

- **High-signal reproducible stacks** (`training.md`, README Featured)  
- **Broader taxonomy surface** (`taxonomy.md`, `catalog.md`) including methods without public code yet  

Use README Featured to start; use taxonomy/catalog for completeness.

External-list coverage is tracked separately in [`awesome_gap_fill.md`](awesome_gap_fill.md), based on a 2026-07-14 cross-check against [xxzcc/Awesome-Credit-Assignment-in-LLM-RL](https://github.com/xxzcc/Awesome-Credit-Assignment-in-LLM-RL).

## SAO citation audit

Checked against [SAO: Single-Rollout Asynchronous Optimization for Agentic Reinforcement Learning](https://arxiv.org/abs/2607.07508) on 2026-07-14. Core CA/training methods are promoted to the main tables; adjacent asynchronous systems and baseline optimizers are tracked here so they are not misclassified as credit-assignment algorithms.

| Work | Link | SAO context | Repository action |
|------|------|-------------|-------------------|
| VAPO | [2504.05118](https://arxiv.org/abs/2504.05118) | Provides the length-adaptive GAE/value-model PPO baseline that SAO adopts and ablates | Added to README, `training.md`, `catalog.md`, and `taxonomy.md` |
| GSPO / Group Sequence Policy Optimization | [2507.18071](https://arxiv.org/abs/2507.18071) | Sequence-level importance weighting for GRPO/PPO-style objectives | Adjacent objective; tracked here |
| DCPO / Dynamic Clipping Policy Optimization | [2509.02333](https://arxiv.org/abs/2509.02333) | Adaptive clipping strategy related to SAO's clipping/off-policy discussion | Adjacent objective; tracked here |
| Asynchronous RLHF | [2410.18252](https://arxiv.org/abs/2410.18252) | Frames async RLHF as online-but-off-policy learning | Async RL infrastructure/background; tracked here |
| AReaL | [2505.24298](https://arxiv.org/abs/2505.24298) | Decoupled rollout/training and staleness-aware PPO-style updates | Async reasoning-RL system; tracked here |
| ROLL Flash | [2510.11345](https://arxiv.org/abs/2510.11345) | Fine-grained parallelism and rollout/train decoupling for RLVR and agentic training | Async RLVR/agentic system; tracked here |
| MobileRL | [2509.18119](https://arxiv.org/abs/2509.18119) | Online agentic RL for mobile GUI agents with difficulty-adaptive GRPO variants | Agentic RL adjacent method; tracked here |
| IcePop / Every Step Evolves | [2510.18855](https://arxiv.org/abs/2510.18855) | Related token-level clipping/masking mechanism for large thinking-model RL | Industrial training adjacent; tracked here |
| RLOO / Back to Basics | [2402.14740](https://arxiv.org/abs/2402.14740) | REINFORCE-style baseline contrasted with GRPO/PPO | Baseline optimizer; tracked here |

SAO also cites Qwen3, GPT-OSS-120B, and GLM-4.5 as model/report dependencies; those are not added as credit-assignment methods.
