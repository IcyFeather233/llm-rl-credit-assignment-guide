# Paper search and primary-reading report

Generated for the README training-method summary on 2026-07-13.

Query: `credit assignment reinforcement learning large language models reasoning agentic training`

Window: 2024-2026

Requested sources: Semantic Scholar, OpenAlex, arXiv, OpenReview, Crossref, DBLP

## Search-source results

The unified search was attempted twice. The second run surfaced the following errors verbatim:

```text
[arxiv] Error: HTTP Error 429: Unknown Error
Rate limited. Waiting 3 seconds...
```

The remaining source workers stayed in repeated rate-limit backoff and the run was terminated after several minutes. No complete structured result set was returned, so citation counts and source-level rankings below are deliberately left unavailable rather than reconstructed from memory.

### Semantic Scholar (0 captured)

No completed response was returned before the rate-limited search was terminated.

### OpenAlex (0 captured)

No completed response was returned before the rate-limited search was terminated.

### arXiv API (0 captured)

The search endpoint returned `HTTP Error 429: Unknown Error`. Primary arXiv source packages were still fetched directly by known identifiers and are listed below.

### OpenReview (0 captured)

No completed response was returned before the rate-limited search was terminated.

### Crossref (0 captured)

No completed response was returned before the rate-limited search was terminated.

### DBLP (0 captured)

No completed response was returned before the rate-limited search was terminated.

### Model Knowledge (0 papers)

No memory-only entries were added. The README summary uses verified primary sources instead.

## Primary sources read directly

| # | Work | Date | Venue/status | Primary source | Citation count |
|---|------|------|--------------|----------------|----------------|
| 1 | DeepSeekMath / GRPO | 2024-02 | arXiv | [2402.03300](https://arxiv.org/abs/2402.03300) | unavailable |
| 2 | ArCHer | 2024-02 | ICML 2024 | [2402.19446](https://arxiv.org/abs/2402.19446) | unavailable |
| 3 | VinePPO | 2024-10 | ICML 2025 | [2410.01679](https://arxiv.org/abs/2410.01679) | unavailable |
| 4 | DeepSeek-R1 | 2025-01 | arXiv | [2501.12948](https://arxiv.org/abs/2501.12948) | unavailable |
| 5 | PRIME | 2025-02 | arXiv | [2502.01456](https://arxiv.org/abs/2502.01456) | unavailable |
| 6 | SWEET-RL | 2025-03 | arXiv | [2503.15478](https://arxiv.org/abs/2503.15478) | unavailable |
| 7 | PURE | 2025-04 | arXiv | [2504.15275](https://arxiv.org/abs/2504.15275) | unavailable |
| 8 | GiGPO | 2025-05 | NeurIPS 2025 | [2505.10978](https://arxiv.org/abs/2505.10978) | unavailable |
| 9 | SPO | 2025-05 | arXiv | [2505.23564](https://arxiv.org/abs/2505.23564) | unavailable |
| 10 | CAPO | 2025-08 | arXiv | [2508.02298](https://arxiv.org/abs/2508.02298) | unavailable |
| 11 | AgentPRM | 2025-11 | arXiv | [2511.08325](https://arxiv.org/abs/2511.08325) | unavailable |
| 12 | OAR | 2026-01 | arXiv | [2601.07408](https://arxiv.org/abs/2601.07408) | unavailable |
| 13 | HiPER | 2026-02 | arXiv | [2602.16165](https://arxiv.org/abs/2602.16165) | unavailable |
| 14 | ProxMO | 2026-02 | arXiv | [2602.19225](https://arxiv.org/abs/2602.19225) | unavailable |
| 15 | HGPO | 2026-02 | arXiv | [2602.22817](https://arxiv.org/abs/2602.22817) | unavailable |
| 16 | HCAPO | 2026-03 | arXiv | [2603.08754](https://arxiv.org/abs/2603.08754) | unavailable |
| 17 | Credit Assignment Survey | 2026-04 | arXiv | [2604.09459](https://arxiv.org/abs/2604.09459) | unavailable |
| 18 | VPR | 2026-05 | arXiv | [2605.10325](https://arxiv.org/abs/2605.10325) | unavailable |
| 19 | DelTA | 2026-05 | arXiv | [2605.21467](https://arxiv.org/abs/2605.21467) | unavailable |
| 20 | SCRL | 2026-05 | arXiv | [2605.22074](https://arxiv.org/abs/2605.22074) | unavailable |
| 21 | GraphGPO | 2026-05 | arXiv | [2605.26684](https://arxiv.org/abs/2605.26684) | unavailable |
| 22 | TRIAGE | 2026-06 | arXiv | [2606.32017](https://arxiv.org/abs/2606.32017) | unavailable |
| 23 | QVal | 2026-06 | arXiv | [2606.32034](https://arxiv.org/abs/2606.32034) | unavailable |

## Summary of all searched results

### 1. Overview

The requested multi-source query did not complete because public APIs rate-limited the search. The substantive review instead used the official arXiv source packages of the repository's core training papers, covering the 2024-2026 shift from trajectory-level GRPO to token, segment, turn, hierarchy, graph, and role-conditioned credit.

### 2. Trends

Work in 2024 established critic-free group baselines and hierarchical actor-critic training. Reasoning work in 2025 concentrated on MC values, process rewards, implicit PRMs, and LLM critics. Agentic work in 2025-2026 increasingly reuses structure in collected rollouts: repeated states, historical contexts, semantic neighborhoods, state-transition graphs, subgoals, hindsight outcomes, and semantic action roles.

### 3. Key themes

1. **Outcome broadcast baseline:** GRPO and DeepSeek-R1 show that terminal-only group credit is cheap and effective, but coarse.
2. **Direct process evidence:** VinePPO, SPO, VPR, and SCRL spend rollout or verifier compute to obtain local supervision.
3. **Learned or generated critics:** PRIME, PURE, CAPO, SWEET-RL, AgentPRM, ArCHer, and HiPER trade extra models for denser advantages.
4. **Critic-free rollout structure:** GiGPO, ProxMO, HGPO, GraphGPO, OAR, and DelTA reshape credit from existing rollouts or gradients.
5. **Agentic hindsight and semantics:** HCAPO and TRIAGE use final-state information or role labels when intermediate correctness is not directly verifiable.

### 4. Keywords frequency

Manual method-tag counts over the 23 directly read sources:

| Keyword / method tag | Count |
|----------------------|-------|
| Group-relative or GRPO-based update | 12 |
| Token/step/segment process credit | 11 |
| Agentic turn or hierarchical credit | 10 |
| Learned PRM, value model, or critic | 7 |
| Extra verifier, judge, or counterfactual scoring | 6 |

### 5. Most cited by accepted paper

Citation counts were unavailable because the bibliographic APIs did not complete. No ranking is reported.

| Rank | Title | Year | Citations |
|------|-------|------|-----------|
| - | unavailable | - | - |

### 6. Most cited by first author

Citation counts were unavailable because the bibliographic APIs did not complete. No author ranking is reported.

| Rank | Author | Papers in set | Total citations |
|------|--------|---------------|-----------------|
| - | unavailable | - | - |

### 7. Recommendations for reading

1. [DeepSeekMath / GRPO](https://arxiv.org/abs/2402.03300): establishes the trajectory-level group baseline that later papers refine.
2. [VinePPO](https://arxiv.org/abs/2410.01679): cleanest demonstration that more accurate MC credit can outperform a learned PPO value model.
3. [GiGPO](https://arxiv.org/abs/2505.10978): practical critic-free bridge from episode credit to agentic step credit.
4. [SWEET-RL](https://arxiv.org/abs/2503.15478) and [HiPER](https://arxiv.org/abs/2602.16165): representative privileged-critic and hierarchical-critic alternatives.
5. [GraphGPO](https://arxiv.org/abs/2605.26684) and [TRIAGE](https://arxiv.org/abs/2606.32017): recent examples of structural and semantic credit beyond trajectory outcomes.

---

## Long-horizon supplement search (2026-07-13)

Query: `long-horizon LLM agent credit assignment memory provenance hindsight graph reinforcement learning`

Window: 2025-2026

The required multi-source paper-search script was run for this supplement. It remained in repeated `Rate limited. Waiting 3 seconds...` backoff and returned no structured records, so no citation counts are inferred. The papers below were instead verified from official arXiv source packages using the identifiers supplied in the survey and citation identities recovered from those packages.

### Newly verified primary sources

| Work | Date | CA/training role | Paper | Public implementation |
|------|------|------------------|-------|-----------------------|
| Proactive Memory Agent | 2026-07 | Memory intervention/silence policy; adjacent CA | [2607.08716](https://arxiv.org/abs/2607.08716) | [code](https://github.com/yifannnwu/proactive-memory-agent) |
| TACO | 2026-07 | Token positive-credit calibration | [2607.07976](https://arxiv.org/abs/2607.07976) | [code](https://github.com/xiuyilou/TACO) |
| STRACE | 2026-07 | Long-trace causal diagnosis; not policy-gradient CA | [2607.07702](https://arxiv.org/abs/2607.07702) | [code](https://github.com/moomight/STRACE) |
| IGRPO | 2026-07 | Information-directed tree rollout and policy target | [2607.06223](https://arxiv.org/abs/2607.06223) | [code](https://github.com/e3trange/IGRPO) |
| CompactionRL | 2026-07 | Joint execution/summary RL | [2607.05378](https://arxiv.org/abs/2607.05378) | unavailable |
| ProGPO | 2026-07 | Exact-prefix peers plus state potential | [2607.04242](https://arxiv.org/abs/2607.04242) | unavailable |
| PivoARL | 2026-07 | Pivotal-error local counterfactual retry | [2607.03702](https://arxiv.org/abs/2607.03702) | [code](https://github.com/yuki-younai/PivoARL) |
| ECHO | 2026-06 | Source-indexed memory provenance routing | [2606.31650](https://arxiv.org/abs/2606.31650) | [code](https://github.com/xiezijun714-lang/Echo) |
| CRAFT | 2026-06 | Sibling-rollout signed counterfactual token credit | [2606.29476](https://arxiv.org/abs/2606.29476) | unavailable |
| G2PO | 2026-06 | Global graph edge TD advantage | [2606.22995](https://arxiv.org/abs/2606.22995) | [code](https://github.com/Nala-YN/G2PO) |
| PBSD | 2026-06 | Privileged Bayesian turn evidence | [2606.09348](https://arxiv.org/abs/2606.09348) | unavailable |
| Memory-R2 | 2026-05 | Global/local fair memory-state comparison | [2605.21768](https://arxiv.org/abs/2605.21768) | [code](https://github.com/ahmedehabb/Memory-R2) |
| SWE-TRACE | 2026-04 | Rubric PRM and memory-augmented SWE RL | [2604.14820](https://arxiv.org/abs/2604.14820) | unavailable |
| DeltaBelief-RL | 2026-02 | Belief-shift intrinsic credit | [2602.12342](https://arxiv.org/abs/2602.12342) | [code](https://github.com/bethgelab/delta-belief-rl) |
| CARL | 2025-12 | Critical-state tree advantage/selective update | [2512.04949](https://arxiv.org/abs/2512.04949) | announced, not available in source |
| CriticSearch | 2025-11 | Privileged retrospective turn critic | [2511.12159](https://arxiv.org/abs/2511.12159) | unavailable |
| SPA-RL | 2025-05 | Learned stepwise progress redistribution | [2505.20732](https://arxiv.org/abs/2505.20732) | [code](https://github.com/WangHanLinHenry/SPA-RL-Agent) |

### Supplement synthesis

1. **Graph/group estimators:** G2PO and ProGPO extend exact repeated-state comparison with global edge TD credit or multi-depth state potentials.
2. **Privileged/counterfactual estimators:** PBSD, CRAFT, PivoARL, and CriticSearch use answers, siblings, retries, or complete traces to separate useful and harmful intermediate behavior.
3. **Active sampling:** CARL and IGRPO allocate expansion or gradient budget toward critical/high-information states, coupling exploration with uncertainty about useful decisions.
4. **Memory and context:** ECHO, Memory-R2, and CompactionRL make provenance, diverged memory states, and summaries explicit training objects. Proactive Memory Agent demonstrates the adjacent intervention/silence decision but not a complete causal credit estimator.
5. **Real long-horizon evaluation:** PBSD, ECHO, Memory-R2, CompactionRL, SWE-TRACE, and Proactive Memory Agent move beyond short household/web tasks toward hundreds of search turns, multi-session memory, SWE, and terminal environments.

The names SALT, the survey's same-state-entropy description of STAPO, Q-Evolve, ARCO, PiCA, and SGCD were not added: this search run did not return records and the inspected primary-source citation neighborhoods did not uniquely verify those identities. A different paper named [STAPO](https://arxiv.org/abs/2602.15620), *Stabilizing Reinforcement Learning for LLMs by Silencing Rare Spurious Tokens*, was verifiable, but it does not match the method description in the supplied survey.

---

## SAO targeted supplement (2026-07-14)

Query: `Single-Rollout Asynchronous Optimization for Agentic Reinforcement Learning`

Window: 2026

The paper-search skill script was run for the exact title across arXiv, Semantic Scholar, OpenAlex, Crossref, and DBLP. It remained in repeated rate-limit backoff:

```text
Rate limited. Waiting 3 seconds...
```

The run was interrupted after it failed to return structured records. Metadata and mechanism details were then verified from the official arXiv API record and arXiv source package for [2607.07508](https://arxiv.org/abs/2607.07508).

### Newly verified primary source

| Work | Date | CA/training role | Paper | Public implementation |
|------|------|------------------|-------|-----------------------|
| SAO / Single-Rollout Asynchronous Optimization | 2026-07-08 | Asynchronous single-rollout RL; value-model GAE and token-level off-policy filtering for long-horizon agentic training | [2607.07508](https://arxiv.org/abs/2607.07508) | no public trainer found |

### Mechanism notes

SAO replaces group-wise GRPO sampling with one rollout per prompt so completed trajectories can be consumed immediately in asynchronous training. To control policy lag, it uses rollout log-probabilities as the behavior policy and masks tokens outside a double-sided importance-ratio trust region. Because single-rollout training loses group-relative baselines, it relies on a trained value model, faster critic updates than policy updates, frozen-attention value-model tuning, and skip-observation token-level GAE for agentic traces. The paper reports experiments on SWE-Bench Verified, AIME2025, BeyondAIME, HMMT Nov 2025, IMOAnswerBench, and simulated online writing, and states that SAO was deployed in the agentic RL pipeline for GLM-5.2.

---

## Focused SFT/offline long-horizon search (2026-07-15)

Query family: `offline trajectory credit assignment supervised fine-tuning long-horizon LLM agents`, plus targeted queries for selective SFT, failure mining, hindsight relabeling, state-conditioned preference, causal imitation learning, and privileged SWE process supervision.

Window: 2024-2026, with AWR retained as a classical foundation.

The unified paper-search script queried arXiv, OpenAlex, Semantic Scholar, DBLP, and OpenReview. It recovered 23 candidates in the successful targeted run; the broader run entered repeated rate-limit backoff and DBLP returned HTTP 503. OpenReview credentials were unavailable. The ranked list below is based on topical relevance followed by primary arXiv-page verification, not citation count.

| Rank | Work | Date | Relevance to fixed-log long-horizon credit | Primary source |
|---:|---|---|---|---|
| 1 | Agentic-DPO | 2026-07 | Strict-offline state-conditioned expert-vs-student action preference; no environment interaction during optimization | [2607.10601](https://arxiv.org/abs/2607.10601) |
| 2 | P2T | 2026-05 | Reference-patch process graph, grounded step progress, and efficient SWE trajectory curation | [2605.21996](https://arxiv.org/abs/2605.21996) |
| 3 | SWE-Lego | 2026-01 | Long-horizon SFT-only SWE baseline with tool-error masking and a turn curriculum | [2601.01426](https://arxiv.org/abs/2601.01426) |
| 4 | HSL / Spinning Straw into Gold | 2026-07 | Hindsight achieved-goal relabeling, irrelevant-action masks, and sample weighting | [2607.04235](https://arxiv.org/abs/2607.04235) |
| 5 | ATLaS | 2025-03 | Direct hard step-level selective SFT while preserving the full context | [2503.02197](https://arxiv.org/abs/2503.02197) |
| 6 | HPL | 2025-10 | Offline trajectory/group/step preference learning for long-horizon agents | [2510.03253](https://arxiv.org/abs/2510.03253) |
| 7 | STeP | 2025-05 | Error/reflection/correction trajectories with partial target masking | [2505.20023](https://arxiv.org/abs/2505.20023) |
| 8 | EEF | 2025-04 | Recovers beneficial actions from failed expert trajectories | [2504.13145](https://arxiv.org/abs/2504.13145) |
| 9 | Q-SFT | 2024-11 | Q-learning expressed as an SFT-like offline objective | [2411.05193](https://arxiv.org/abs/2411.05193) |
| 10 | DML-IL | 2025-02 | Causal imitation learning with hidden confounding and trajectory histories | [2502.07656](https://arxiv.org/abs/2502.07656) |
| 11 | AgentHER | 2026-03 | Failure classification and hindsight goal relabeling into SFT/DPO data | [2603.21357](https://arxiv.org/abs/2603.21357) |
| 12 | ACC | 2026-05 | Compiles distant tool evidence into long-context supervised examples | [2605.21850](https://arxiv.org/abs/2605.21850) |
| 13 | InT | 2026-01 | First-error intervention followed by localized SFT; reasoning rather than agentic | [2601.14209](https://arxiv.org/abs/2601.14209) |
| 14 | IPR / Watch Every Step | 2024-06 | Monte Carlo step-value upper bound from expert-prefix rerollouts | [2406.11176](https://arxiv.org/abs/2406.11176) |
| 15 | CSO | 2026-02 | Critical-step alternatives and branch verification; semi-online upper bound | [2602.03412](https://arxiv.org/abs/2602.03412) |

The resulting synthesis and method proposal are in [`papers/sft_offline_long_horizon_credit_assignment.md`](papers/sft_offline_long_horizon_credit_assignment.md).
