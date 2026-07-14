# Awesome list gap-fill

Cross-check source: [Awesome-Credit-Assignment-in-LLM-RL](https://github.com/xxzcc/Awesome-Credit-Assignment-in-LLM-RL), checked on 2026-07-14.

This file records papers from that list that were not fully covered in this repository's main tables at the time of comparison. Entries imported from the external list are marked as `source-listed`; they are not all primary-read yet. `-` means the external list or the paper metadata did not make the field clear.

Note: the external list maps PURE to `2504.08662`, but title search and the current repository both identify PURE as [2504.15275](https://arxiv.org/abs/2504.15275). The mismatched id was not imported as a separate PURE entry.

## Core and near-core CA methods

| Date | Work | Type | Granularity | Org | Base model | Train data | Eval data | Link | Coverage note |
|------|------|------|-------------|-----|------------|------------|-----------|------|---------------|
| 2026-06 | APPO: Agentic Procedural Policy Optimization | Near-core | Decision / Procedure | - | - | Agent procedure rollouts | Agentic tasks | [2606.12384](https://arxiv.org/abs/2606.12384) | source-listed |
| 2026-06 | OPID: On-Policy Skill Distillation for Agentic RL | Near-core | Token / Skill | - | - | On-policy trajectories with hindsight skills | Agentic tasks | [2606.26790](https://arxiv.org/abs/2606.26790) | source-listed |
| 2026-06 | Progress Advantage for LLM Agents | Related signal | Step / Turn | - | - | Policy/reference log-prob progress signals | Agent tasks / failure attribution | [2606.26080](https://arxiv.org/abs/2606.26080) | source-listed |
| 2026-06 | STARE: Surprisal-Guided Token-Level Advantage Reweighting | Related signal | Token / Entropy | - | - | RLVR rollouts with surprisal reweighting | RLVR reasoning tasks | [2606.19236](https://arxiv.org/abs/2606.19236) | source-listed |
| 2026-05 | AEM: Adaptive Entropy Modulation for Multi-Turn Agentic RL | Core | Response / Turn | - | - | Multi-turn agentic rollouts | Multi-turn agent tasks | [2605.00425](https://arxiv.org/abs/2605.00425) | source-listed |
| 2026-05 | T2PO: Uncertainty-Guided Exploration Control | Core | Token / Turn | - | - | Multi-turn rollouts with uncertainty interventions | Multi-turn agent tasks | [2605.02178](https://arxiv.org/abs/2605.02178) | source-listed |
| 2026-05 | A2TGPO: Agentic Turn-Group Policy Optimization | Core | Turn | - | - | Turn-group agent rollouts | Multi-turn agent tasks | [2605.06200](https://arxiv.org/abs/2605.06200) | already in catalog; expanded here |
| 2026-05 | Learning CLI Agents with Structured Action Credit | Core | Turn / Action | - | - | CLI-agent trajectories with structured action chains | Coding / CLI tasks | [2605.08013](https://arxiv.org/abs/2605.08013) | already in catalog; expanded here |
| 2026-05 | Rubric-Grounded RL | Adjacent | Criterion / Step | - | - | Structured judge/rubric rewards | Reasoning tasks | [2605.08061](https://arxiv.org/abs/2605.08061) | source-listed |
| 2026-05 | PAPO: Entropy Polarity in Reinforcement Fine-Tuning | Near-core | Token / Entropy | - | - | RL fine-tuning rollouts with entropy-polarity weights | RLFT reasoning tasks | [2605.11775](https://arxiv.org/abs/2605.11775) | source-listed |
| 2026-05 | SDAR: Self-Distilled Agentic Reinforcement Learning | Related | Distillation / Agentic | - | - | Self-distilled agent trajectories | Agentic tasks | [2605.15155](https://arxiv.org/abs/2605.15155) | source-listed |
| 2026-05 | Not only where, But when: Temporal Scheduling for RLVR | Related | Scheduling | - | - | RLVR rollouts with scheduled criteria | RLVR reasoning tasks | [2605.25381](https://arxiv.org/abs/2605.25381) | source-listed |
| 2026-05 | In-Context Credit Assignment via the Core | Related | Contributor / Coalition | - | - | In-context contribution examples | AI-generated content attribution | [2605.06920](https://arxiv.org/abs/2605.06920) | game-theoretic adjacent |
| 2026-05 | Reinforcement Learning for LLM-based Multi-Agent Systems through Orchestration Traces | Related | Trace / Agent / Team | - | - | Multi-agent orchestration traces | Multi-agent tasks | [2605.02801](https://arxiv.org/abs/2605.02801) | source-listed |
| 2026-03 | C3: Contextual Counterfactual Credit Assignment | Core | Turn / Multi-agent | - | - | LLM collaboration traces with leave-one-out counterfactuals | Collaboration tasks | [2603.06859](https://arxiv.org/abs/2603.06859) | taxonomy placeholder now linkable |
| 2026-03 | CCPO: Counterfactual Credit Policy Optimization | Core | Turn / Multi-agent | - | - | Multi-agent collaboration trajectories | Collaboration tasks | [2603.21563](https://arxiv.org/abs/2603.21563) | taxonomy placeholder now linkable |
| 2026-03 | ITPO: Implicit Turn-Wise Policy Optimization | Core | Turn | - | - | Proactive user-LLM interaction trajectories | Proactive interaction tasks | [2603.23550](https://arxiv.org/abs/2603.23550) | source-listed |
| 2026-02 | SHARP: Shapley Credit-based Optimization for Multi-Agent System | Core | Multi-agent | - | - | Multi-agent trajectories with Shapley decomposition | Multi-agent benchmarks | [2602.08335](https://arxiv.org/abs/2602.08335) | source-listed |
| 2026-02 | Dr. MAS: Stable RL for Multi-Agent LLM Systems | Core | Multi-agent | - | - | Multi-agent GRPO rollouts | Multi-agent LLM tasks | [2602.08847](https://arxiv.org/abs/2602.08847) | source-listed |
| 2026-01 | PRL: Process Reward Learning | Core | Step | - | - | Entropy-regularized process reward learning | Reasoning tasks | [2601.10201](https://arxiv.org/abs/2601.10201) | source-listed |
| 2026-01 | InT: Self-Proposed Interventions Enable Credit Assignment | Core | Step | - | - | Self-proposed intervention traces | Reasoning tasks | [2601.14209](https://arxiv.org/abs/2601.14209) | source-listed |
| 2026-01 | FinePO: Fine-Grained Process Reward via SketchVL | Core | Sub-step | - | - | Domain-specific visual reasoning process rewards | Visual reasoning tasks | [2601.05688](https://arxiv.org/abs/2601.05688) | source-listed |
| 2026-01 | SCRIBE: Structured Mid-Level Supervision for Tool-Using LMs | Related | Skill / Step | - | - | Skill-prototype supervision | Tool-use tasks | [2601.03555](https://arxiv.org/abs/2601.03555) | source-listed |
| 2026-01 | MAPPA: Scaling Multiagent Systems with Process Rewards | Core | Multi-agent | - | - | Per-action process rewards from AI feedback | AIME / multi-agent tasks | [2601.23228](https://arxiv.org/abs/2601.23228) | source-listed |
| 2025-12 | Turn-PPO: Turn-Level Optimized Policy Optimization | Core | Turn | - | - | Multi-turn trajectories with turn-level MDP objective | Multi-turn agent tasks | [2512.17008](https://arxiv.org/abs/2512.17008) | source-listed |
| 2025-12 | PRS: Progressive Reward Shaping and VSPO | Core | Step | - | - | Progressive reward densification | Agentic RL tasks | [2512.07478](https://arxiv.org/abs/2512.07478) | source-listed |
| 2025-11 | SORL: Stabilizing Off-Policy RL for Long-Horizon Agent Training | Core | Turn | - | - | Off-policy long-horizon agent trajectories | Long-horizon agent tasks | [2511.20718](https://arxiv.org/abs/2511.20718) | source-listed |
| 2025-11 | M-GRPO: Multi-Agent Deep Research | Core | Multi-agent | - | - | Deep-research multi-agent rollouts | Deep research tasks | [2511.13288](https://arxiv.org/abs/2511.13288) | source-listed |
| 2025-10 | ACPO: Attribution-based Credit Assignment | Core | Step | - | - | Verifiable RL rollouts with attribution | Verifiable reasoning tasks | [2510.08899](https://arxiv.org/abs/2510.08899) | source-listed |
| 2025-10 | IGPO: Information Gain-based Policy Optimization | Core | Turn | - | - | Turn-level information-gain rollouts | Agentic tasks | [2510.14967](https://arxiv.org/abs/2510.14967) | distinct from IGRPO |
| 2025-09 | HICRA: Hierarchical Reasoning in LLMs through RL | Core | Step | - | - | Reasoning traces with planning/procedural token split | Reasoning tasks | [2509.03646](https://arxiv.org/abs/2509.03646) | source-listed |
| 2025-09 | TARL: Process-Supervised RL for Interactive Multimodal Tool-Use Agents | Core | Turn | - | - | Multimodal tool-use trajectories with judge labels | Interactive multimodal tool-use tasks | [2509.14480](https://arxiv.org/abs/2509.14480) | source-listed |
| 2025-09 | TEMPO: Exploiting Tree Structure for Credit Assignment | Core | Token / Segment | - | - | Tree-structured reasoning paths | Math/reasoning tasks | [2509.18314](https://arxiv.org/abs/2509.18314) | source-listed |
| 2025-09 | iStar: Agentic RL with Implicit Step Rewards | Core | Step | - | - | Trajectory-level DPO / implicit step rewards | Agentic tasks | [2509.19199](https://arxiv.org/abs/2509.19199) | source-listed |
| 2025-08 | PilotRL: Global Planning-Guided Progressive RL | Core | Plan / Step / Token | - | - | Progressive plan-step-token training | Agentic tasks | [2508.00344](https://arxiv.org/abs/2508.00344) | source-listed |
| 2025-08 | Agent Lightning: Train ANY AI Agents with RL | Infrastructure | Step | Microsoft Research | - | Decoupled agent-training traces | Agent tasks | [2508.03680](https://arxiv.org/abs/2508.03680) | source-listed |
| 2025-07 | SPRO: Self-Guided Process Reward Optimization | Core | Step | - | - | Self-guided masked step advantages | Math reasoning tasks | [2507.01551](https://arxiv.org/abs/2507.01551) | source-listed |
| 2025-05 | SCAR: Shapley Credit Assignment Rewards | Core | Segment | - | - | Reasoning segment Shapley estimates | Math reasoning tasks | [2505.20417](https://arxiv.org/abs/2505.20417) | source-listed |
| 2025-05 | Reinforcing Multi-Turn Reasoning via Turn-Level Reward Design | Core | Turn | - | - | Hybrid verifiable/judge turn rewards | Multi-turn reasoning tasks | [2505.11821](https://arxiv.org/abs/2505.11821) | source-listed |
| 2025-04 | RAGEN: Understanding Self-Evolution in LLM Agents | Infrastructure | Step / Uncertainty | - | - | StarPO multi-turn RL agent training | RAGEN / StarPO benchmark | [2504.20073](https://arxiv.org/abs/2504.20073) | source-listed |
| 2025-04 | QLLM: LLM-generated Credit Assignment Function | Related | Multi-agent | - | - | LLM-generated mixing/credit functions | Multi-agent RL tasks | [2504.12961](https://arxiv.org/abs/2504.12961) | source-listed |
| 2025-02 | LLM-MCA: LLMs for Explainable Multi-Agent Credit Assignment | Core | Multi-agent | - | - | Full-history centralized critic traces | Multi-agent tasks | [2502.16863](https://arxiv.org/abs/2502.16863) | source-listed |
| 2024-12 | T-REG: Preference Optimization with Token-Level Reward Regularization | Core | Token | - | - | Contrastive self-prompted preference data | Preference/reasoning tasks | [2412.02685](https://arxiv.org/abs/2412.02685) | source-listed |
| 2024-12 | LaRe: Latent Reward | Core | Step | - | - | Natural-language credit explanations to scalar rewards | Episodic RL tasks | [2412.16989](https://arxiv.org/abs/2412.16989) | source-listed |
| 2024-11 | RED: Token-Level Rewards from Holistic Feedback | Core | Token | - | - | Reward-model internals / holistic feedback | Preference/reward-modeling tasks | [2411.08302](https://arxiv.org/abs/2411.08302) | source-listed |
| 2024-11 | StepAgent: Step-wise Reinforcement Learning | Core | Step | - | - | Expert demonstrations plus novice-to-expert curriculum | Agent tasks | [2411.03817](https://arxiv.org/abs/2411.03817) | source-listed |
| 2024-11 | Adaptive Segment-Level Reward | Core | Segment | - | - | Semantic segment-level reward alignment | Alignment tasks | [2411.00809](https://arxiv.org/abs/2411.00809) | source-listed |
| 2024-05 | POAD: Policy Optimization with Action Decomposition | Core | Token / Turn | - | - | Action-decomposed agent trajectories | Agent tasks | [2405.15821](https://arxiv.org/abs/2405.15821) | source-listed |
| 2024-04 | From r to Q*: Your Language Model is Secretly a Q-Function | Core | Token | - | - | Preference pairs / implicit Q learning | Preference optimization tasks | [2404.12358](https://arxiv.org/abs/2404.12358) | source-listed |
| 2026 | GVPO: Group Verification-based Policy Optimization for Interactive Coding Agents | Core | Step / Action | - | - | Outcome- and process-verifiable coding-agent feedback | Interactive coding-agent tasks | [OpenReview](https://openreview.net/forum?id=RY47Tq0VsV) | source-listed |

## Foundational and background items from the external list

These are useful context but are not counted as new LLM credit-assignment training methods in this repository.

| Date | Work | Role | Link |
|------|------|------|------|
| 2025 | The Landscape of Agentic Reinforcement Learning for LLMs | Survey | [2509.02547](https://arxiv.org/abs/2509.02547) |
| 2025 | A Survey of Reinforcement Learning for Large Reasoning Models | Survey | [2509.08827](https://arxiv.org/abs/2509.08827) |
| 2024 | Math-Shepherd | PRM foundation | [2312.08935](https://arxiv.org/abs/2312.08935) |
| 2024 | OmegaPRM / automated process supervision | PRM foundation | [2406.06592](https://arxiv.org/abs/2406.06592) |
| 2024 | Toolformer | Tool-use foundation | [2302.04761](https://arxiv.org/abs/2302.04761) |
| 2024 | WebArena | Agent benchmark foundation | [2307.13854](https://arxiv.org/abs/2307.13854) |
| 2023 | Temporal Credit Assignment in Deep RL survey | Classical CA survey | [2312.01072](https://arxiv.org/abs/2312.01072) |
| 2023 | Direct Preference Optimization | Preference optimization foundation | [2305.18290](https://arxiv.org/abs/2305.18290) |
| 2022 | InstructGPT / RLHF | PPO-based LLM alignment foundation | [2203.02155](https://arxiv.org/abs/2203.02155) |
| 2019 | RUDDER | Return decomposition foundation | [1806.07857](https://arxiv.org/abs/1806.07857) |
| 2017 | PPO | RLHF optimization foundation | [1707.06347](https://arxiv.org/abs/1707.06347) |
| 2016 | GAE | Advantage-estimation foundation | [1506.02438](https://arxiv.org/abs/1506.02438) |
