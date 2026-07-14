# Training methods

Date-descending. Link style: **[📄 Paper] · [💻 Code] · [📦 Data]**. Experiment metadata uses `—` when the main paper does not clearly report it or it is not applicable.

## 2026

| Date | Work | Org | Base model | Train data | Eval data | Links | OSS |
|------|------|-----|------------|------------|-----------|-------|-----|
| 2026-07 | Proactive Memory Agent | Meta AI | Qwen3.5-27B memory agent; Qwen3.5-122B-A10B action agent | Terminal-Bench 2.0 / tau2-bench trajectories | Terminal-Bench 2.0, tau2-bench | [📄](https://arxiv.org/abs/2607.08716) · [💻](https://github.com/yifannnwu/proactive-memory-agent) · [📦 Terminal-Bench](https://www.tbench.ai/) · [📦 tau2-bench](https://github.com/sierra-research/tau2-bench) | ✅ |
| 2026-07 | TACO | JHU/Rice et al. | Qwen3-1.7B/4B-Base; Qwen2.5-Math-7B | Math/coding RLVR rollouts | Math and coding RLVR benchmarks | [📄](https://arxiv.org/abs/2607.07976) · [💻](https://github.com/xiuyilou/TACO) · 📦 — | ✅ |
| 2026-07 | SAO | Tsinghua/Z.AI | Qwen3-30B-A3B-Thinking-2507 | GPT-OSS-120B TIR SFT; single-rollout RL on reasoning/coding/online-writing tasks; VAPO-style length-adaptive GAE | SWE-bench Verified, AIME2025, BeyondAIME, HMMT Nov 2025, IMOAnswerBench | [📄](https://arxiv.org/abs/2607.07508) · 💻 — · [📦 SWE-bench Verified](https://www.swebench.com/) · 📦 BeyondAIME/IMOAnswerBench | ◐ |
| 2026-07 | IGRPO | SJTU | Qwen2.5-3B/7B-Instruct | Information-gain tree rollouts for search QA | Search QA benchmarks | [📄](https://arxiv.org/abs/2607.06223) · [💻](https://github.com/e3trange/IGRPO) · 📦 — | ✅ |
| 2026-07 | CompactionRL | Tsinghua/Z.AI | GLM-4.7-Flash; GLM-4.5-Air-SFT | Long-horizon SWE/terminal rollouts with context compaction | SWE-bench Verified, Terminal-Bench 2.0 | [📄](https://arxiv.org/abs/2607.05378) · 💻 — · [📦 SWE-bench](https://www.swebench.com/) · [📦 Terminal-Bench](https://www.tbench.ai/) | ◐ |
| 2026-07 | ProGPO | Peking University | Qwen2.5-1.5B/3B-Instruct | Agent rollouts with exact-prefix peer groups | ALFWorld, WebShop | [📄](https://arxiv.org/abs/2607.04242) · 💻 — · [📦 ALFWorld](https://github.com/alfworld/alfworld) · [📦 WebShop](https://github.com/princeton-nlp/WebShop) | ◐ |
| 2026-07 | PivoARL | — | Qwen3-4B | Failed agent/search traces plus pivotal-state retries | Agent tasks, search QA benchmarks | [📄](https://arxiv.org/abs/2607.03702) · [💻](https://github.com/yuki-younai/PivoARL) · 📦 — | ✅ |
| 2026-06 | ECHO | PKU/USTC et al. | Qwen3-32B-Instruct; Qwen3-30B-A3B-Instruct | Source-indexed search-agent memory traces | BrowseComp-Plus | [📄](https://arxiv.org/abs/2606.31650) · [💻](https://github.com/xiezijun714-lang/Echo) · 📦 BrowseComp-Plus via code | ✅ |
| 2026-06 | CRAFT | HKUST | Qwen2.5-3B/7B/14B-Instruct; Qwen3-8B-Instruct | GRPO sibling rollouts plus privileged teacher signals | Agentic RL tasks | [📄](https://arxiv.org/abs/2606.29476) · 💻 — · 📦 — | ❌ |
| 2026-06 | G2PO | PKU/Microsoft | Qwen2.5-1.5B/7B/14B-Instruct | Agent rollout transition graphs | ALFWorld, WebShop, AppWorld | [📄](https://arxiv.org/abs/2606.22995) · [💻](https://github.com/Nala-YN/G2PO) · [📦 AppWorld](https://github.com/stonybrooknlp/appworld) | ✅ |
| 2026-06 | PBSD | — | Qwen3-30B-A3B-Thinking-2507 | Answer-conditioned search-agent rollouts | BrowseComp / long-context search | [📄](https://arxiv.org/abs/2606.09348) · 💻 — · [📦 BrowseComp](https://huggingface.co/datasets/openai/BrowseComp) | ◐ |
| 2026-06 | TRIAGE | — | Qwen2.5-7B-Instruct; Qwen3-1.7B-Instruct | Agent rollouts segmented and role-labeled by judge | ALFWorld, WebShop | [📄](https://arxiv.org/abs/2606.32017) · 💻 — · [📦 ALFWorld](https://github.com/alfworld/alfworld) · [📦 WebShop](https://github.com/princeton-nlp/WebShop) | ◐ |
| 2026-05 | Memory-R2 | LMU/TUM et al. | Qwen2.5-7B-Instruct | Multi-session memory write/update/delete curriculum | LoCoMo, LongMemEval | [📄](https://arxiv.org/abs/2605.21768) · [💻](https://github.com/ahmedehabb/Memory-R2) · [📦 LoCoMo](https://github.com/snap-research/locomo) | ✅ |
| 2026-05 | GraphGPO | NTU | Qwen2.5-1.5B/7B-Instruct | Agent rollout graphs | ALFWorld, WebShop | [📄](https://arxiv.org/abs/2605.26684) · [💻](https://github.com/langfengQ/verl-agent/tree/master/recipe/GraphGPO) · [📦 ALFWorld](https://github.com/alfworld/alfworld) · [📦 WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| 2026-05 | DelTA | RUC | Qwen3-8B/14B-Base; Olmo3-7B-Base | Positive/negative RLVR rollouts | Math RLVR benchmarks | [📄](https://arxiv.org/abs/2605.21467) · [💻](https://github.com/RUCBM/DelTA) · 📦 — | ✅ |
| 2026-05 | SCRL | — | Qwen3-4B/14B-Base; Llama3.2-3B-Instruct | Reference-solution subproblem curricula | Math reasoning benchmarks | [📄](https://arxiv.org/abs/2605.22074) · 💻 — · 📦 — | ❌ |
| 2026-05 | VPR | — | Qwen3-4B | Verifiable agentic process rewards | Verifiable agentic tasks | [📄](https://arxiv.org/abs/2605.10325) · 💻 — · 📦 — | ❌ |
| 2026-04 | SWE-TRACE | — | Qwen3-4B; Qwen3-30B-A3B | Repository-agent traces with rubric PRM rewards | SWE-bench Verified | [📄](https://arxiv.org/abs/2604.14820) · 💻 — · [📦 SWE-bench Verified](https://www.swebench.com/) | ◐ |
| 2026-03 | HCAPO | NJU | Qwen2.5-1.5B/3B/7B-Instruct | Agent rollouts with hindsight final-state scoring | ALFWorld, WebShop | [📄](https://arxiv.org/abs/2603.08754) · 💻 — · [📦 ALFWorld](https://github.com/alfworld/alfworld) · [📦 WebShop](https://github.com/princeton-nlp/WebShop) | ◐ |
| 2026-02 | DeltaBelief-RL | Tübingen | Qwen3-1.7B/4B | Synthetic interaction tasks with belief-shift reward | Synthetic interaction and OOD tasks | [📄](https://arxiv.org/abs/2602.12342) · [💻](https://github.com/bethgelab/delta-belief-rl) · 📦 synthetic interaction tasks | ✅ |
| 2026-02 | HGPO | NTU | Qwen2.5-1.5B/7B-Instruct | Agent rollouts grouped by state/history | ALFWorld, WebShop | [📄](https://arxiv.org/abs/2602.22817) · [💻](https://github.com/langfengQ/verl-agent/tree/master/recipe/hgpo) · [📦 ALFWorld](https://github.com/alfworld/alfworld) · [📦 WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| 2026-02 | ProxMO | — | Qwen2.5-1.5B/7B-Instruct | Agent rollouts with proximity baselines | ALFWorld, WebShop | [📄](https://arxiv.org/abs/2602.19225) · [💻](https://github.com/FlyTune/ProxMO-RL) · [📦 ALFWorld](https://github.com/alfworld/alfworld) · [📦 WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| 2026-02 | HiPER | Northwestern/UMN/Cisco | Qwen2.5-1.5B/7B-Instruct | Hierarchical plan/execute agent rollouts | ALFWorld, WebShop | [📄](https://arxiv.org/abs/2602.16165) · [💻](https://github.com/JonP07/HiPER-agent) · [📦 ALFWorld](https://github.com/alfworld/alfworld) · [📦 WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| 2026-01 | AT²PO | — | Qwen3-4B/8B; Qwen2.5-7B | Multi-turn turn-tree rollouts | Multi-turn tasks | [📄](https://arxiv.org/abs/2601.04767) · 💻 — · 📦 — | ❌ |
| 2026-01 | OAR | — | Qwen2.5-7B-Base; Qwen2.5-Math-7B | RLVR rollouts with token-importance reshaping | Math reasoning benchmarks | [📄](https://arxiv.org/abs/2601.07408) · 💻 — · 📦 — | ❌ |

## 2025

| Date | Work | Org | Base model | Train data | Eval data | Links | OSS |
|------|------|-----|------------|------------|-----------|-------|-----|
| 2025-12 | CARL | — | Qwen2.5-3B/7B-Instruct; Qwen3-4B-Instruct | Critical-state search QA rollouts | Search QA benchmarks | [📄](https://arxiv.org/abs/2512.04949) · 💻 announced · 📦 search QA | ❌ |
| 2025-11 | CriticSearch | — | Qwen2.5-3B/7B-Base policy; Qwen2.5-3B/7B-Instruct critic | Search traces with gold-answer retrospective critic | Multi-hop QA / search benchmarks | [📄](https://arxiv.org/abs/2511.12159) · 💻 — · 📦 multi-hop QA | ❌ |
| 2025-11 | AgentPRM | Fudan/Ant | Qwen2.5-0.5B/3B/7B-Instruct; Llama-3.1-8B-Instruct | Agent trajectories with TD/GAE PRM targets | Agent search/control tasks | [📄](https://arxiv.org/abs/2511.08325) · 💻 — · 📦 — | ◐ |
| 2025-08 | CAPO | — | Llama-3-1B/3B-Instruct; Qwen2.5-1.5B/7B-Base | RLVR rollouts with GenPRM critiques | Math reasoning and OOD benchmarks | [📄](https://arxiv.org/abs/2508.02298) · [💻](https://github.com/andyclsr/CAPO) · 📦 — | ✅ |
| 2025-06 | Reward-Weighted Fine-Tuning | — | Llama-3.1-8B-Instruct | Offline conversation trajectories with scalar rewards | Short-horizon QA / conversation optimization | [📄](https://arxiv.org/abs/2506.06964) · 💻 — · 📦 — | ❌ |
| 2025-05 | SPO | — | RhoMath 1.1B; DeepSeek-R1-Distill-Qwen-1.5B; DeepSeekMath 7B | Math CoT segment rollouts | Math reasoning benchmarks | [📄](https://arxiv.org/abs/2505.23564) · [💻](https://github.com/AIFrameResearch/SPO) · 📦 — | ✅ |
| 2025-05 | STeP | — | LLaMA2-7B-Chat student; Qwen1.5-110B-Chat teacher | Synthetic self-reflected trajectories with partial masking | ALFWorld, WebShop, SciWorld | [📄](https://arxiv.org/abs/2505.20023) · 💻 — · 📦 — | ❌ |
| 2025-05 | SPA-RL | — | Llama-3.2-3B-Instruct | Agent rollouts with learned progress attribution | ALFWorld, WebShop, VirtualHome | [📄](https://arxiv.org/abs/2505.20732) · [💻](https://github.com/WangHanLinHenry/SPA-RL-Agent) · [📦 ALFWorld](https://github.com/alfworld/alfworld) · [📦 WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| 2025-05 | GiGPO | NTU/Skywork | Qwen2.5-1.5B/7B-Instruct | Agent rollouts with repeated anchor states | ALFWorld, WebShop | [📄](https://arxiv.org/abs/2505.10978) · [💻](https://github.com/langfengQ/verl-agent) · [📦 ALFWorld](https://github.com/alfworld/alfworld) · [📦 WebShop](https://github.com/princeton-nlp/WebShop) | ✅ |
| 2025-04 | EEF | — | Llama-3-8B-Instruct; Mistral-7B-v0.3 ablation | Successful and failed expert trajectories; beneficial actions mined from failures | WebShop, SciWorld | [📄](https://arxiv.org/abs/2504.13145) · 💻 — · 📦 — | ❌ |
| 2025-04 | VAPO | ByteDance Seed | Qwen-32B | Long-CoT verifier-based reasoning RL rollouts; value pretraining from fixed-policy MC returns | AIME 2024 | [📄](https://arxiv.org/abs/2504.05118) · 💻 — · 📦 — | ❌ |
| 2025-04 | PURE | — | Qwen2.5-7B; Qwen2.5-Math-7B/1.5B | PRM800K / process-reward supervision plus RLVR rollouts | Math reasoning benchmarks | [📄](https://arxiv.org/abs/2504.15275) · [💻](https://github.com/CJReinforce/PURE) · [📦 PRM800K](https://github.com/openai/prm800k) | ✅ |
| 2025-03 | SWEET-RL | Meta/FAIR | Llama-3.1-8B-Instruct | ColBench interaction pairs with privileged labels | ColBench | [📄](https://arxiv.org/abs/2503.15478) · [💻](https://github.com/facebookresearch/sweet_rl) · [📦 ColBench](https://github.com/facebookresearch/sweet_rl) | ✅ |
| 2025-03 | ATLaS | — | Llama-3.1-8B-Instruct | Expert agent trajectories with critical-step masks | Multi-domain agent tasks | [📄](https://arxiv.org/abs/2503.02197) · 💻 — · 📦 — | ❌ |
| 2025-01 | DeepSeek-R1 | DeepSeek | DeepSeek-V3-Base | Cold-start SFT plus large-scale RL on reasoning data | Math, code, and reasoning benchmarks | [📄](https://arxiv.org/abs/2501.12948) · [💻](https://huggingface.co/deepseek-ai) · 📦 — | ✅ |
| 2025-01 | PRIME | PRIME-RL | Qwen2.5-Math-7B-Base | Outcome-labeled reasoning rollouts for implicit PRM/RL | Math reasoning benchmarks | [📄](https://arxiv.org/abs/2502.01456) · [💻](https://github.com/PRIME-RL/PRIME) · [📦](https://huggingface.co/PRIME-RL) | ✅ |

## 2024

| Date | Work | Org | Base model | Train data | Eval data | Links | OSS |
|------|------|-----|------------|------------|-----------|-------|-----|
| 2024-11 | Q-SFT | UC Berkeley | Pretrained LLMs/VLMs | Static offline transition/reward datasets | Dialogue, robotic manipulation, visual navigation | [📄](https://arxiv.org/abs/2411.05193) · 💻 — · 📦 — | ❌ |
| 2024-10 | VinePPO | McGill | DeepSeekMath-7B-RL; Rho-Math-1.1B | Math reasoning continuations from intermediate prefixes | MATH, GSM8K | [📄](https://arxiv.org/abs/2410.01679) · [💻](https://github.com/McGill-NLP/VinePPO) · [📦](https://github.com/McGill-NLP/VinePPO) | ✅ |
| 2024-06 | IPR / Watch Every Step | — | Llama-2-7B | Expert trajectories plus MC rollouts from expert prefixes | Three complex agent tasks | [📄](https://arxiv.org/abs/2406.11176) · 💻 — · 📦 — | ❌ |
| 2024-02 | ArCHer | Berkeley et al. | GPT-2 actor; RoBERTa-base critic; Mistral-7B scaling | Replay-buffer agent trajectories | WebShop / ALFWorld-style agent environments | [📄](https://arxiv.org/abs/2402.19446) · [💻](https://github.com/YifeiZhou02/ArCHer) · [📦](https://github.com/YifeiZhou02/ArCHer) | ✅ |
| 2024-02 | GRPO/DeepSeekMath | DeepSeek | DeepSeekMath-Base 7B | Math instruction/RL data | GSM8K, MATH, CMATH and related math benchmarks | [📄](https://arxiv.org/abs/2402.03300) · [💻 PDF](https://arxiv.org/pdf/2402.03300) · 📦 — | ✅ |

## Infra

| Stack | Links | OSS |
|-------|-------|-----|
| veRL | [💻](https://github.com/volcengine/verl) | ✅ |
| verl-agent (GiGPO/HGPO/GraphGPO) | [💻](https://github.com/langfengQ/verl-agent) | ✅ |
| OpenRLHF | [💻](https://github.com/OpenRLHF/OpenRLHF) | ✅ |
| ProxMO-RL | [💻](https://github.com/FlyTune/ProxMO-RL) | ✅ |

## Foundations

| Date | Work | Role | Link |
|------|------|------|------|
| 2019-10 | AWR | Advantage-weighted behavior cloning / weighted SFT foundation for offline RL | [📄](https://arxiv.org/abs/1910.00177) |
