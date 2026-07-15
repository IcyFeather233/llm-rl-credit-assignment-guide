# SFT / Offline Credit Assignment for Long-Horizon LLM Agents

> Research memo and method proposal. Literature checked through **2026-07-15**.
> Scope: learn a better agent policy from a **frozen set of logged trajectories**, with no environment interaction during policy optimization. Teacher annotation, reference answers, and replay are treated as separate, explicitly labeled regimes.

## Executive summary

Standard agent SFT treats every action token in a demonstration as equally correct:

$$
\mathcal L_{\mathrm{SFT}}
=-\sum_i\sum_{t=1}^{T_i}\log \pi_\theta(a_{i,t}\mid h_{i,t}).
$$

This is an implicit credit assignment rule, $w_{i,t}=1$. It is particularly fragile for long trajectories: a successful trace can contain loops, unsupported guesses, accidental tool calls, or mistakes repaired much later; a failed trace can contain the right plan, useful evidence, and several completed subgoals. Full-trajectory SFT reinforces the former and discards or penalizes the latter.

The central difficulty is not how to write a weighted SFT loss. It is **identification**: from one logged action and one eventual outcome, the outcome under an unobserved alternative action is generally unknowable. Therefore, a credible offline method should not fabricate a precise score for every step. It should:

1. compare only behaviorally similar states with genuine action overlap;
2. represent delayed dependencies through evidence and memory provenance;
3. return an **interval-valued credit estimate** and abstain when its sign is not identifiable;
4. compile positive, negative, and uncertain segments into different training targets.

This memo proposes **Conservative Provenance Credit Distillation (CPCD)**. Its operative rule is simple:

> Give positive SFT credit only when the lower confidence bound is positive; construct a negative preference only when the upper bound is negative and an observed better alternative exists; otherwise keep the segment in context but mask its imitation loss.

The intended contribution is not a claim to recover the true causal value of every logged action. It is a conservative, auditable way to turn heterogeneous long-horizon logs into selective SFT and offline preference data, with explicit support, confounding, and falsification tests.

---

## 1. Problem, motivation, and challenges

### 1.1 What “offline credit assignment” means here

Let a logged agent trajectory be

$$
\tau_i=(x_i,o_{i,1},a_{i,1},o_{i,2},a_{i,2},\ldots,o_{i,T_i},a_{i,T_i},y_i),
$$

where $x_i$ is the task, $h_{i,t}$ is the prefix available before action $a_{i,t}$, and $y_i\in[0,1]$ is a terminal verifier score. The available dataset is

$$
\mathcal D=\{(\tau_i,y_i,b_i)\}_{i=1}^{N},
$$

where $b_i$ optionally records the behavior policy, checkpoint, sampling temperature, and logged action probabilities.

The desired training objective is

$$
\mathcal L_{\mathrm{weighted\text{-}SFT}}
=-\sum_i\sum_t w_{i,t}\log \pi_\theta(a_{i,t}\mid h_{i,t}),
$$

possibly augmented with preference losses. Credit assignment is the procedure that determines $w_{i,t}$, the masked tokens, and any preferred/dispreferred action pairs.

Three regimes must be kept separate:

| Regime | Environment calls during optimization | Extra model generation or labels | Examples |
|---|---:|---:|---|
| **Strict fixed-log offline** | No | Optional, but no new environment transitions | ATLaS, Q-SFT, HPL, Agentic-DPO, SWE-Lego |
| **Offline curation / privileged annotation** | No | Strong teacher, reference solution, patch, or hindsight labeler | STeP, EEF, AgentHER, P2T, ACC |
| **Semi-online or replay-based** | Yes, before or during each iteration | Often yes | IPR, CSO, HSL as evaluated, SWE-TRACE |

The proposed main setting is the first row. The second row is an optional stronger-supervision variant and the third row is used only as an upper bound.

### 1.2 Why long horizons make ordinary SFT worse

#### Delayed effects

An observation gathered at turn 7 may support a decision at turn 63. Turn distance is a poor proxy for relevance, so recency masks and uniform discounting miss the actual dependency.

#### Successful trajectories are not clean demonstrations

A terminal success label says that the whole interaction eventually worked. It does not certify every search query, file edit, memory update, or reasoning step. Recovery can hide damaging actions.

#### Failed trajectories are not uniformly bad

A failure can be caused by one pivotal action after a long correct prefix. Outcome filtering throws away useful exploration and rare subskills precisely on the hard tasks where successful expert data are sparse.

#### Partial observability creates confounding

The teacher may have seen a reference answer, hidden scratchpad, richer browser state, or repository metadata that is absent from the student history. An action can correlate with success because of that hidden information rather than because the action is useful from the student's observable state.

#### Exact state recurrence is rare

Two 80-turn histories almost never match token for token. Semantic state abstraction is necessary for comparison, but a coarse abstraction may merge states with different constraints, memory contents, or environment side effects.

#### A long trace can dominate the optimizer

Without trajectory-level normalization, a 200-turn trace contributes far more supervised tokens than a 20-turn trace. This confounds “better credit” with simply changing the amount of gradient mass per task.

#### Credit quality is rarely measured directly

Most work reports only downstream success. A method may improve because it regularizes training or removes tokens, even if its step scores have little relationship to intervention-derived contribution.

### 1.3 Why an offline approach is still worth pursuing

Offline agent logs are attractive because expensive environments, browsers, containers, and proprietary tools need not be active during training. Historical deployments also contain diverse failures, recoveries, and behavior policies that ordinary expert-only SFT ignores. Tool metadata, source URLs, file paths, test results, memory source indices, and timestamps provide structural signals unavailable in a plain reasoning transcript.

The opportunity is therefore not “infer everything from one trajectory.” It is to combine:

- **cross-trajectory variation** from multiple policies or stochastic rollouts;
- **prefix-grounded provenance** that records which evidence was later used;
- **local verifiers** such as tool schemas, tests, and state diffs;
- **conservative uncertainty estimates** that turn missing support into abstention rather than a guessed label.

This can reduce the cost and instability of online RL while producing an SFT-compatible artifact that can be inspected before training.

### 1.4 The unavoidable identifiability limit

Suppose every logged trajectory reaching abstract state $c$ takes action $u$ and succeeds. The observed data are compatible with both of these worlds:

- every alternative action would fail, so $u$ is pivotal;
- an alternative action would also succeed, so $u$ is unnecessary.

No estimator can distinguish these worlds from the fixed logs. A point credit score in this case is a modeling assumption, not an identified causal effect.

Consequently, CPCD requires either repeated action variation in comparable states or returns a vacuous interval. Its causal interpretation is conditional on:

1. **consistency:** the abstract action corresponds to a stable intervention;
2. **overlap:** plausible alternatives occur with non-negligible probability;
3. **sequential ignorability:** the recorded prefix representation contains the important common causes of action and outcome, up to an explicit sensitivity allowance;
4. **stable continuation:** after the compared segment, value is defined under the logged continuation-policy mixture.

Without these assumptions, the output should be called **observational utility credit**, not causal credit.

### 1.5 Minimum useful data contract

| Field | Status | Why it matters |
|---|---|---|
| Task ID and task-family ID | Required | Prevents train/test leakage and enables task-clustered uncertainty |
| Ordered actions and observations | Required | Defines prefixes and delayed dependencies |
| Terminal verifier outcome | Required | Supplies the final utility being attributed |
| Multiple trajectories per task or support cell | Required for signed credit | Provides action overlap; a single expert trace supports masking heuristics only |
| Tool-call IDs, result IDs, file/URL/test metadata | Strongly recommended | Enables deterministic provenance edges |
| Behavior policy/checkpoint/temperature | Strongly recommended | Makes propensity modeling and policy-mixture analysis more credible |
| Logged action probabilities | Recommended | Reduces propensity-model error; not mandatory if action classes are discrete |
| Restorable state snapshots | Evaluation only | Enables intervention-derived credit labels without making training online |
| Reference answer or patch | Optional privileged variant | Useful for comparison, but excluded from the primary fixed-log estimator |

---

## 2. Related work and reusable ideas

### 2.1 Direct SFT and offline trajectory methods

| Work | Regime | Credit or curation signal | Training target | What it contributes | Main boundary for this project |
|---|---|---|---|---|---|
| [AWR](https://arxiv.org/abs/1910.00177) (2019) | Offline RL foundation | Learned advantage | Advantage-weighted behavior cloning | Canonical weighted maximum-likelihood view | Scalar value estimates can extrapolate outside support; not an LLM-agent method |
| [Q-SFT](https://arxiv.org/abs/2411.05193) (2024) | Static transition/reward data | Bellman/Q target encoded in token probability | Modified SFT without a separate value head | Shows that offline value learning can retain an SFT-like objective | Requires transition rewards and inherits offline Q-learning assumptions |
| [IPR / Watch Every Step](https://arxiv.org/abs/2406.11176) (2024) | Semi-online | Monte Carlo continuations from expert prefixes | SFT plus step/outcome preferences | Direct intervention-style estimate of expert-step value | Intermediate environment rollout is expensive and violates strict offline training |
| [ATLaS](https://arxiv.org/abs/2503.02197) (2025) | Fixed trajectories plus LLM selector | Binary critical-step labels for planning, key observations/actions, and self-correction | Full context, loss only on selected steps | Clean separation between “visible as context” and “learned as target” | Importance is judged, not estimated from counterfactual outcomes; hard mask has no uncertainty |
| [EEF](https://arxiv.org/abs/2504.13145) (2025) | Offline data curation | Beneficial plans/actions mined from failed expert traces | Add useful failure fragments to fine-tuning | Establishes that failed trajectories should not be rejected wholesale | Credit depends on the failure-analysis heuristic and is not calibrated |
| [STeP](https://arxiv.org/abs/2505.20023) (2025) | Teacher-generated offline traces | Error, reflection, and correction labels | Keep errors in context but mask their loss | Demonstrates target/context separation for self-correction traces | Requires synthetic reflection quality; mostly explicit error masking |
| [Reward-Weighted Fine-Tuning](https://arxiv.org/abs/2506.06964) (2025) | Offline outcome-labeled traces | One trajectory reward | Reward-weighted SFT | Strong trajectory-level offline baseline | Broadcasts the same reward to every action, so it does not solve within-trace credit |
| [HPL](https://arxiv.org/abs/2510.03253) (2025) | Offline preference learning | Trajectory-, action-group-, and step-level preferences | Hierarchical DPO with curriculum | Useful multi-scale segmentation and preference formulation | Requires contrasting groups and gives no support-aware abstention |
| [DML-IL](https://arxiv.org/abs/2502.07656) (2025) | Causal imitation-learning foundation | Conditional moment restrictions; histories as instruments | History-dependent imitation policy | Makes hidden confounding and trajectory history mathematically explicit | Validated in classical control, not language-agent traces; instrumental assumptions remain strong |
| [SWE-Lego](https://arxiv.org/abs/2601.01426) (2026) | SFT-only SWE training | Explicit tool-error masking and difficulty curriculum | Masked SFT on validated trajectories | Direct long-horizon SFT baseline with 18K validated traces and turn-length curriculum | Tool errors catch visible failures, not semantic detours or delayed evidence misuse |
| [InT](https://arxiv.org/abs/2601.14209) (2026) | On-policy reasoning traces plus references | First-error localization and one-step corrective intervention | SFT on correct prefix plus correction, then RL | Precise error-boundary supervision | Reasoning-only, reference-dependent, and not fixed-log agent training |
| [AgentHER](https://arxiv.org/abs/2603.21357) (2026) | Offline hindsight relabeling | Failure type, actually achieved outcome, confidence gate | Relabeled SFT/DPO examples | Converts some failures into goal-conditioned successes | Changes the task label rather than estimating contribution to the original goal |
| [P2T](https://arxiv.org/abs/2605.21996) (2026) | Privileged SWE curation | Reference patch to latent process graph; grounded progress and length | SFT on shortest effective segments | Closest provenance/process-graph inspiration for realistic SWE horizons | Needs a reference patch, executable tests, teacher continuations, and LLM judges |
| [ACC](https://arxiv.org/abs/2605.21850) (2026) | Offline trajectory compilation | Distant tool observations become long-context QA evidence | Direct-answer SFT | Shows how logged evidence can supervise long-range integration | Trains context reasoning, not action-level utility credit |
| [HSL / Spinning Straw into Gold](https://arxiv.org/abs/2607.04235) (2026) | Hindsight relabeling; evaluated with iterative rollouts | Achieved goals, irrelevant-action mask, sample weight | SFT or DPO on relabeled goals | Uses unintended successes and reports larger gains on long horizons | The evaluated pipeline collects new trajectories and optimizes relabeled goals, so it is not a strict fixed-log answer by itself |
| [Agentic-DPO](https://arxiv.org/abs/2607.10601) (2026) | No environment interaction during optimization | Expert action vs one-step student negative at each expert state | DPO plus SFT anchor and policy-preserving augmentation | Closest recent strict-offline state-conditioned action baseline; tested on tau-bench retail and Mind2Web | Every expert action is presumed preferred; student-only states and useful failure segments are not covered |

### 2.2 Adjacent work that should shape the method or evaluation

| Work | Reusable idea | How it affects this proposal |
|---|---|---|
| [ECHO](https://arxiv.org/abs/2606.31650) | Source-indexed memory and credit routing to evidence turns and selection actions | Motivates a provenance graph, but CPCD estimates utility from fixed logs and can abstain rather than routing only positive terminal credit |
| [CSO](https://arxiv.org/abs/2602.03412) | Identify critical steps, generate expert alternatives, branch from those states, verify outcomes | Serves as a semi-online upper bound and a recipe for evaluation-only intervention labels |
| [SWE-TRACE](https://arxiv.org/abs/2604.14820) | Rubric process reward and long-token SWE evaluation | Supplies a realistic process-judge baseline and horizon-stratified evaluation |
| [HORIZON](https://arxiv.org/abs/2604.11978) | Failure attribution over 3,100+ multi-domain trajectories | Motivates direct credit-quality and root-cause metrics rather than success-only reporting |
| [OpenResearcher](https://arxiv.org/abs/2603.20278) | Large offline deep-research corpus with a long tail beyond 100 tool calls | Provides a candidate data source and a genuinely long-horizon stress test |
| [CFT](https://arxiv.org/abs/2510.10974) and [DFT](https://arxiv.org/abs/2508.05629) | Selective or dynamically reweighted token SFT | Useful optimization controls, but token salience is not delayed action contribution |
| [CurateEvo](https://arxiv.org/abs/2607.06140) | Held-out failures drive iterative data-curation programs | Useful system-level baseline; it optimizes the curator rather than identifying step effects in one frozen log set |

### 2.3 What is still missing

The direct literature covers hard step selection, explicit error masking, failure mining, Q/advantage weighting, hindsight goal relabeling, and state-conditioned preferences. The remaining gap is the combination of:

1. **strictly offline, long-horizon agent traces** rather than short reasoning responses;
2. **failure and recovery-aware signed credit**, not an assumption that every expert action is positive;
3. **semantic provenance** for dependencies spanning many turns;
4. **support-aware uncertainty and abstention**, instead of a scalar score for every step;
5. **direct causal-credit evaluation** using held-out interventions.

### 2.4 Closest-work comparison

| Dimension | ATLaS | Agentic-DPO | P2T | DML-IL | Proposed CPCD |
|---|---|---|---|---|---|
| Main supervision | LLM criticality label | Expert vs sampled action | Reference-patch process graph | Conditional moments from demonstrations | Outcome variation plus logged provenance |
| Uses failed traces | Not central | No | Teacher failures can inform curation | General demonstrations | Yes, if matched support exists |
| Negative credit | Mask unselected steps | Preference against sampled negative | Remove inefficient/ungrounded segments | Implicit through policy estimation | Only when interval is strictly negative and a positive logged alternative exists |
| Long-delay representation | Full text context | Expert state prefix | Privileged process graph | History-dependent policy | Source-to-use provenance DAG |
| Handles no support | No explicit mechanism | Samples a plausible negative | Judge/privileged score | Depends on identification assumptions | Returns a vacuous interval and masks loss |
| Causal-credit metric | No | No | Progress/grounding analyses | Imitation gap | Intervention sign, risk-coverage, and calibration |

This comparison defines the novelty boundary. CPCD should not claim that selective SFT, process graphs, doubly robust estimation, or DPO are individually new.

---

## 3. Proposed method: Conservative Provenance Credit Distillation

### 3.1 Research question and hypotheses

**Research question.** Can a frozen, heterogeneous set of long-horizon agent logs support reliable signed segment credit, and can conservative use of that credit outperform full SFT and offline preference baselines without additional environment interaction?

Pre-registered hypotheses:

- **H1, selective utility:** certified-positive segments improve Success@1 over full SFT and random masks at the same supervised-token budget.
- **H2, long-delay structure:** provenance improves credit precision and task performance increasingly with source-to-use distance and total trajectory length.
- **H3, conservatism:** interval abstention lowers false-sign credit and improves the risk-coverage curve relative to point estimates.
- **H4, failure reuse:** positive segments recovered from failed trajectories provide gains on hard/OOD tasks beyond success-only training.

### 3.2 One central principle

The method is a compiler from frozen logs to training targets:

```text
frozen trajectories
        |
        v
typed segments + prefix-grounded provenance DAG
        |
        v
semantic state/action support cells
        |
        v
cross-fitted outcome/propensity models
        |
        v
sensitivity-aware credit interval [L, U]
        |
        +----------------+------------------+
        |                |                  |
      L > 0            U < 0          interval crosses 0
        |                |                  |
 weighted SFT     observed-pair DPO     context-only mask
```

The graph, estimator, and verifier are training-time components only. Inference uses the fine-tuned policy without a critic or provenance extractor.

### 3.3 Step 1: build a typed provenance DAG

For each trajectory, construct a directed acyclic graph $G_i=(V_i,E_i)$ with timestamped nodes:

- task constraints and subgoals;
- tool actions and environment state changes;
- observations and evidence spans;
- memory writes, updates, retrievals, and summaries;
- claims or decisions that use earlier evidence;
- locally verified milestones;
- terminal outcome.

Edges are typed as `produces`, `supports`, `uses`, `updates`, `retrieves`, `enables`, or `verifies`.

Construction follows a strict priority:

1. deterministic metadata edges from tool-call/result IDs, file paths, URLs, test IDs, and memory source indices;
2. schema rules for state transitions and milestone checks;
3. a prefix-only semantic extractor for support/use edges.

The extractor may see the current prefix but not future observations, the terminal reference answer, or the developer patch in the primary setting. Every semantic edge must cite an earlier source span. Edges that point backward in time or lack a source are rejected.

The DAG is an **eligibility structure**, not proof of causality. It answers “could this earlier segment support this later decision?”; outcome variation determines whether the eligible segment receives positive or negative utility credit.

### 3.4 Step 2: define semantic segments and treatments

Turn-level credit is too fine for multi-call subroutines and too coarse for mixed thought/action turns. Collapse the trace into semantic segments $z_{i,j}$ using deterministic boundaries:

- a tool call and its returned observation;
- a memory operation and its cited sources;
- an edit followed by a local test;
- a search/read sequence that yields one evidence item;
- a plan/subgoal transition;
- a final decision or answer claim.

For each segment define:

$$
c_{i,j}=\phi(h_{i,j}), \qquad u_{i,j}=\psi(z_{i,j}),
$$

where $c$ is a support-cell representation and $u$ is a semantic action class.

$\phi(h)$ includes task constraints, verified milestones, available tools, environment fingerprints, current memory contents, behavior-policy ID, and compact provenance features. $\psi(z)$ includes an event type and normalized effect signature, for example `read(file, symbol-family)`, `run(test-scope)`, `search(query-intent)`, `edit(component, operation-type)`, or `retrieve(memory-topic)`.

Exact output tokens remain the SFT target. The abstraction is used only to find comparable logged decisions.

#### Action-class audit

A semantic class is invalid if its members lead to materially different next-state effects under otherwise similar prefixes. On a held-out fold:

1. compute normalized next-observation or state-diff signatures;
2. measure within-class disagreement after conditioning on $c$;
3. split a class whose disagreement exceeds the held-out 90th-percentile noise floor;
4. if no stable split has adequate support, assign $[-1,1]$ credit to the class.

This prevents a broad class such as `read(file)` from being treated as one stable action when the chosen file is decisive.

### 3.5 Step 3: define the estimand

For a support cell $c$ and semantic action $u$, define the natural-continuation value

$$
V(u,c)=\mathbb E\left[Y\mid \operatorname{do}(U=u),C=c,
\text{future follows the logged policy mixture}\right].
$$

Credit is a contrast against observed alternative actions:

$$
\Delta(u,c)=V(u,c)-
\sum_{v\ne u}\bar e(v\mid c,U\ne u)V(v,c),
$$

where $\bar e$ is the behavior-policy mixture restricted to alternatives. This estimand asks whether choosing $u$ was better than the alternatives actually represented in the logs. It does not estimate an unconstrained optimal action, and it does not hold future actions fixed token by token.

### 3.6 Step 4: estimate credit with cross-fitted doubly robust learning

Split data by task or repository, never by segment. For each held-out fold, fit on the remaining folds:

- a behavior propensity model $\hat e(u\mid c)$;
- an outcome model $\hat Q(c,u)\approx\mathbb E[Y\mid C=c,U=u]$.

For action $u$ in a local support cell, use the doubly robust estimate

$$
\hat V(u,c)=\frac{1}{|I(c)|}\sum_{k\in I(c)}
\left[
\hat Q(c_k,u)+
\frac{\mathbf 1[u_k=u]}{\hat e(u\mid c_k)}
\left(y_k-\hat Q(c_k,u)\right)
\right].
$$

The contrast $\hat\Delta(u,c)$ uses the same observed-alternative mixture as the estimand. Cross-fitting prevents the segment being scored by nuisance models trained on its own task outcome. Standard errors and bootstrap resampling are clustered by task because all segments in a trajectory share a terminal outcome.

Under overlap and sequential ignorability, a doubly robust estimator is consistent if either the propensity model or outcome model is correctly specified. This protection does **not** eliminate hidden confounding or repair an invalid action abstraction.

#### Support gates

Initial conservative defaults, to be calibrated in the pilot:

- each compared action has $\hat e(u\mid c)\ge 0.05$;
- inverse-propensity effective sample size is at least 20;
- at least two behavior-policy sources contribute to a pooled support cell when possible;
- no single task contributes more than 10% of the local weight.

If a gate fails, the interval is set to the vacuous range $[-1,1]$ rather than extrapolating.

### 3.7 Step 5: produce sensitivity-aware credit intervals

For every supported segment, construct $[L_{i,j},U_{i,j}]$ using:

1. task-clustered bootstrap uncertainty;
2. simultaneous max-$t$ correction across the segment classes being selected;
3. disagreement among matched semantic neighbors;
4. an odds-ratio hidden-confounding sensitivity parameter $\Gamma$.

Report $\Gamma\in\{1,1.25,1.5,2\}$; use $\Gamma=1.5$ as the pre-registered main setting only after a held-out calibration pilot. A result that disappears at $\Gamma=1.25$ should be described as fragile observational evidence.

The interval has three states:

| Interval | Interpretation | Allowed training use |
|---|---|---|
| $L>0$ | Reliably positive under the stated support/sensitivity model | Weighted SFT if also grounded |
| $U<0$ | Reliably negative | Preference against it only when a positive observed alternative exists |
| $L\le 0\le U$ | Ambiguous or unsupported | Context only; mask semantic action/reasoning loss |

### 3.8 Step 6: separate groundedness from utility

A high-outcome correlation can still reward an unsupported guess. Compute a prefix-only groundedness score $g_{i,j}\in[0,1]$ from:

- tool/schema validity;
- consistency with prior environment observations;
- evidence entailment for emitted claims;
- absence of future/reference leakage;
- local state-effect verification where available.

Independently, compute provenance flow $\rho_{i,j}\in[0,1]$ by routing normalized mass backward from verified milestones and the terminal output through source-to-use paths. A segment with no path to any verified milestone receives $\rho=0$ in the primary version. Infrastructure tokens are protected separately by a small schema anchor.

This factorization distinguishes two questions:

- **Was the action supported by information available at the time?** $g$
- **Did the logged evidence suggest that it improved the eventual outcome?** $[L,U]$

### 3.9 Step 7: compile credit into SFT and preference targets

For a reliably positive segment,

$$
w_{i,j}=\min(c_{\max},\max(0,L_{i,j}))\,g_{i,j}\rho_{i,j},
$$

where $c_{\max}$ is the 95th percentile of positive lower bounds on the training split. Normalize or cap total weight per trajectory so long traces do not dominate.

For a reliably negative segment $z^-$, create a preference pair only if the same support cell contains a grounded positive alternative $z^+$. Pair construction is allowed only when both alternatives came from the exact same restorable state, or when a verified schema can render both under one canonical prompt. Semantic similarity alone is not enough to make a valid DPO pair.

The pair loss is

$$
\mathcal L_{\mathrm{pair}}=-\log\sigma\left(
\beta\left[
\log\frac{\pi_\theta(z^+\mid h)}{\pi_{\mathrm{ref}}(z^+\mid h)}-
\log\frac{\pi_\theta(z^-\mid h)}{\pi_{\mathrm{ref}}(z^-\mid h)}
\right]\right).
$$

Do not synthesize a “better” action solely from a judge for the primary experiment. If no observed positive alternative exists, mask the negative segment instead of applying unlikelihood training.

The complete objective is

$$
\mathcal L_{\mathrm{CPCD}}=
\mathcal L_{\mathrm{positive\text{-}SFT}}
+\lambda_{\mathrm{pair}}\mathcal L_{\mathrm{pair}}
+\lambda_{\mathrm{schema}}\mathcal L_{\mathrm{schema}}.
$$

Use $\lambda_{\mathrm{schema}}=0.05$ as an initial anchor on formatting, tool schema, and mandatory control tokens; tune it in $\{0,0.05,0.1\}$. Positive but ungrounded/privileged segments and ambiguous segments stay in the input context but receive no semantic target loss.

### 3.10 Algorithm sketch

```text
Input: frozen logs D, terminal outcomes, tool metadata

1. Split by task/repository into K folds.
2. For each trajectory:
   a. segment tool, memory, edit/test, and decision episodes;
   b. build deterministic provenance edges;
   c. add prefix-only semantic support/use edges;
   d. derive state representation c and semantic action u.
3. For each held-out fold:
   a. fit propensity e(u|c) and outcome Q(c,u) on other folds;
   b. audit semantic action equivalence;
   c. estimate doubly robust contrasts on the held-out fold;
   d. compute clustered, simultaneous, sensitivity-aware [L, U].
4. Compute prefix-groundedness g and provenance flow rho.
5. Compile examples:
   a. L > 0 and grounded -> weighted SFT;
   b. U < 0 plus observed positive alternative -> DPO pair;
   c. otherwise -> context-only masked segment.
6. Cap loss mass per trajectory and train the policy.
7. At evaluation, discard all credit models and run the policy normally.
```

### 3.11 What can be guaranteed

If the simultaneous intervals are valid at family-wise level $\alpha$, and an update is made only when an interval excludes zero, then conditional on the causal assumptions:

$$
\Pr(\text{any selected segment has the wrong true sign})\le \alpha.
$$

This is a conservative **sign-selection guarantee**, not a guarantee of exact causal-effect recovery or downstream policy improvement. It fails if the support representation omits important confounders, semantic action classes are inconsistent, or the interval procedure is miscalibrated.

### 3.12 Practical MVP and full version

#### CPCD-Lite: first paper-quality pilot

- Use deterministic tool/memory provenance only.
- Use hand-specified action classes for ALFWorld/WebShop or tau-bench.
- Require exact task/milestone support cells.
- Fit cross-fitted logistic propensity and outcome models.
- Use task bootstrap intervals and overlap abstention.
- Train positive weighted SFT; add DPO only after positive-credit calibration succeeds.

This version tests the central claim without depending on a learned graph encoder or a large LLM judge.

#### CPCD-Full: long-horizon extension

- Add prefix-only semantic provenance extraction.
- Learn state/action embeddings with task-clustered cross-fitting.
- Add hidden-confounding sensitivity intervals.
- Include memory write/retrieve/update and evidence source/use episodes.
- Add observed-pair negative preference training.
- Scale to SWE and frozen-corpus deep research.

### 3.13 Expected failure modes and built-in responses

| Failure mode | Diagnostic | Response |
|---|---|---|
| Only one action per state | Low propensity/ESS | Abstain; collect a more diverse log set before claiming offline CA |
| State abstraction merges incompatible histories | High within-class next-state disagreement | Split class or make interval vacuous |
| Teacher-only hidden information | Credit collapses under policy-ID or $\Gamma$ sensitivity | Exclude privileged traces or report privileged variant separately |
| Provenance extractor uses future evidence | Prefix-leakage audit | Reject edge and rerun labels |
| Long success traces dominate | Per-task gradient mass imbalance | Cap and normalize loss mass by trajectory |
| Negative credit has no better action | No supported positive pair | Mask only; do not hallucinate a preference target |
| Policy leaves logged support after tuning | OOD action/state rate rises | Stronger SFT anchor, conservative decoding, or iterative data collection as a separate semi-online extension |

---

## 4. Experimental plan

### 4.1 Research questions

1. **RQ1: credit validity.** Do offline intervals predict the sign and ranking of evaluation-only intervention effects?
2. **RQ2: policy value.** Does certified selective training improve task success at matched data, token, and compute budgets?
3. **RQ3: long-horizon value.** Does provenance matter more for long source-to-use distances, memory operations, and trajectories beyond 50 or 100 turns?
4. **RQ4: failure reuse.** Are useful segments from failed traces responsible for OOD/hard-task gains?
5. **RQ5: conservatism.** What precision/coverage trade-off is obtained as overlap, confidence, and $\Gamma$ thresholds change?

### 4.2 Benchmark stack

Use a staged stack; do not start with the most expensive SWE setting before proving that the credit labels mean anything.

| Stage | Domain and split | Why it is needed | Proposed frozen log construction | Primary evaluation |
|---|---|---|---|---|
| A: controlled attribution | ALFWorld + WebShop | Restorable states and multiple alternatives make intervention credit measurable | 16-32 trajectories per training task from several checkpoints/temperatures, then freeze | Success; intervention sign/precision on held-out branch points |
| B: structured tool agent | tau-bench retail, then tau2-bench | Realistic tool schemas, business state, mixed recoveries, medium horizons | 8-16 trajectories per task from base/SFT/teacher mixtures | Task accuracy, policy violations, turns, credit risk-coverage |
| C: true long horizon | SWE-Gym logs -> SWE-bench Verified | Repository state, delayed edit/test effects, 50-100+ turn traces | 4-8 diverse trajectories per issue where affordable; pool only audited semantic cells | Pass@1, cost, turns, tests passed, horizon-stratified credit |
| D: optional deep research | OpenResearcher-style frozen corpus -> BrowseComp-Plus/GAIA | Evidence provenance, citations, bounded context, 100+ calls | Frozen search corpus and source-indexed logs; no live-web dependence | Answer accuracy, citation support, source-to-use distance |

Use task-level splits for household/web/customer-service tasks and repository-level splits for SWE. Near-duplicate tasks, issue variants, and trajectories from the same environment seed must remain in one split.

### 4.3 Offline log design

The quality of the study depends more on behavior diversity than raw trajectory count. Construct the frozen corpus from:

- the base model, an SFT checkpoint, and at least one stronger teacher;
- two or three sampling temperatures;
- successful, failed, and recovered trajectories;
- explicit policy/checkpoint identifiers and, where possible, action log-probabilities.

Freeze the logs before fitting any credit model. The primary CPCD training run makes no environment calls. State restoration and branching are confined to the held-out credit-evaluation set.

Evaluate four data regimes independently:

1. expert successes only;
2. mixed expert successes and failures;
3. heterogeneous base/SFT/teacher logs;
4. cross-policy transfer, where one behavior source is held out from credit estimation.

### 4.4 Baselines

#### Strict-offline and SFT baselines

| Baseline | Question it controls |
|---|---|
| Full-trajectory SFT | Does any selection beat standard imitation? |
| Success-only RFT/SFT | Is using failed data actually useful? |
| Reward-weighted SFT | Is within-trajectory credit better than trajectory weighting? |
| Same-token random mask | Are gains merely regularization or fewer supervised tokens? |
| Perplexity/entropy mask | Is causal structure better than model uncertainty? |
| Explicit tool-error mask / SWE-Lego recipe | Does CPCD improve beyond obvious error removal? |
| ATLaS-style critical-step selector | Is outcome-supported credit better than LLM-perceived importance? |
| STeP/EEF-style masks or segment mining | Does calibrated signed credit beat heuristic reflection/failure reuse? |
| AWR or Q-SFT | Does interval selection beat scalar value weighting? |
| HPL | Is provenance-aware support better than multi-scale preference alone? |
| Agentic-DPO | Does using outcomes, failures, and abstention beat expert-state one-step preferences? |

#### Upper bounds and diagnostic controls

- evaluation-only Monte Carlo branch credit, following the IPR/CSO idea;
- an oracle mask derived from branch effects;
- P2T on SWE when reference patches and tests are available;
- flat-history doubly robust estimation without provenance;
- DML-IL-inspired history representation where an implementable instrument exists.

The semi-online methods are upper bounds, not apples-to-apples offline baselines.

### 4.5 Direct credit evaluation

For 300-500 held-out branch points per domain:

1. restore the environment immediately before a segment;
2. execute the logged semantic action and one or more observed alternative classes;
3. continue with the same fixed continuation policy for $K=16$ stochastic rollouts;
4. estimate the intervention contrast $\Delta^{\mathrm{branch}}$ and its uncertainty;
5. never feed these branch outcomes back into the offline credit estimator.

This measures the same natural-continuation estimand used by CPCD. In SWE, restore a container snapshot and branch only at actions whose state effects can be replayed. In deep research, use a frozen document corpus to avoid live-web non-stationarity.

Primary credit metrics:

- sign precision and recall for positive and negative credit;
- causal precision at 10%, 30%, and 50% coverage;
- area under the risk-coverage curve;
- Spearman correlation with branch-effect magnitude;
- interval coverage and average interval width;
- false reward of harmful steps in successful traces;
- false suppression of helpful steps in failed traces;
- metrics stratified by trajectory length and source-to-use distance.

The main endpoint should be **precision at a pre-registered useful coverage**, not accuracy on a dataset dominated by ambiguous steps.

### 4.6 Downstream policy evaluation

Report:

- Success@1 / Pass@1 and, secondarily, Pass@k;
- turns, generated tokens, tool calls, wall time, and dollar-equivalent environment cost;
- invalid action/schema rate and safety/policy violations;
- hard-task and OOD generalization;
- performance in horizon bins `<20`, `20-50`, `50-100`, and `>100` turns;
- supervised-token fraction and effective gradient mass per task;
- post-training state/action OOD rate relative to the frozen corpus.

Use equal raw tasks, equal supervised action-token count, and equal optimizer FLOPs as three separate comparisons. A selective method should not receive credit for simply training on fewer tokens or taking fewer optimizer steps.

### 4.7 Critical ablations

#### Attribution structure

- remove provenance and use flat turn-recency/history features;
- randomly permute provenance edges while preserving node degree;
- use turn, fixed-length segment, and semantic-segment units;
- remove memory write/retrieve/update nodes;
- use deterministic edges only vs deterministic plus semantic edges.

#### Identification and uncertainty

- point estimate without intervals;
- no cross-fitting;
- no propensity model;
- no outcome model;
- no overlap/ESS gate;
- $\Gamma\in\{1,1.25,1.5,2\}$;
- exact cells vs learned semantic matching;
- same-policy vs mixed-policy logs.

#### Training compiler

- positive SFT only;
- negative DPO only;
- no groundedness gate;
- no provenance-flow weight;
- no schema anchor;
- no trajectory mass cap;
- replace lower bound $L$ with point estimate $\hat\Delta$;
- permute $L$ within each task while preserving its histogram and total loss mass.

The last permutation is the load-bearing falsifier: if performance remains unchanged, the estimated credit values are not doing useful work.

### 4.8 Leakage and judge controls

- Credit-model inputs end at the segment prefix; future observations and references are inaccessible.
- Reference patches/answers are used only in named privileged baselines or held-out evaluation.
- Any semantic extractor is frozen before policy training and audited on human-labeled edges.
- Credit-estimator folds, policy-training tasks, and final evaluation tasks are disjoint.
- Calibrate judge/groundedness labels on a stratified human sample, including successes with regressions and failures with good prefixes.
- Report inter-annotator agreement and judge false-positive rates.
- Record all behavior-policy IDs to test whether “teacher identity” is acting as a hidden success label.

### 4.9 Statistical protocol

- Use at least three policy-training seeds.
- Treat task, not segment, as the independent unit.
- Report paired task-bootstrap 95% confidence intervals for success differences.
- Use cluster bootstrap for credit metrics and correct the small set of co-primary domain comparisons.
- Pre-register two co-primary endpoints: causal precision at 30% coverage and Success@1 at equal supervised-token budget.
- Determine final sample size from the variance observed in a 10% pilot; do not power the study using correlated segment counts.

### 4.10 Resource plan

These are planning bounds, not performance claims:

| Scope | Models/domains | Approximate budget |
|---|---|---|
| MVP | One 4B model, ALFWorld/WebShop, CPCD-Lite, 3 seeds | 12-20 H100-equivalent GPU-days plus branch-evaluation environment time |
| Main study | 4B and 8B, controlled + tau-bench + SWE, full ablations | 60-90 H100-equivalent GPU-days |
| Semantic annotation | Prefix-only edge/grounding extraction and human audit | Cap paid-model usage near USD 4K; replace with open model where quality is adequate |

Benchmark throughput and label cost on 5% of the data before committing to the full study. The dominant costs are likely branch-based evaluation and long-context SWE fine-tuning, not the doubly robust estimator.

### 4.11 Success and kill criteria

#### Evidence supporting the idea

- at least 80% positive-sign precision at 30% coverage on controlled interventions;
- a reproducible Success@1 gain of at least 3 absolute points over full SFT and same-token random masking on two domains;
- a larger provenance ablation gap in the `>50`-turn or long source-to-use bucket;
- positive value from failed-trace segments after matching token and compute budgets;
- graceful precision/coverage behavior as $\Gamma$ increases.

#### Results that should stop or substantially revise the project

- fewer than 10% of segments have non-vacuous intervals after reasonable pooling;
- the equal-mass permuted-credit mask matches CPCD;
- flat-history estimation matches provenance on long-delay examples;
- gains disappear under equal supervised-token or equal-FLOP controls;
- credit precision collapses under $\Gamma=1.25$;
- Agentic-DPO or explicit tool-error masking matches CPCD across all domains;
- improvements occur only when future/reference information leaks into labels;
- no benefit appears in trajectories beyond 50 turns.

These criteria make the work informative even if the proposed method fails: the resulting evidence would show whether fixed logs contain enough overlap to support long-horizon signed credit at all.

---

## 5. Recommended research shape

### 5.1 Primary paper claim

A defensible claim would be:

> Long-horizon offline agent credit should be treated as conservative sign identification rather than dense score prediction. Provenance-defined semantic segments plus support-aware intervals can selectively compile frozen logs into SFT and preference targets, improving both intervention-level credit precision and downstream policy learning.

Avoid the stronger claim that CPCD recovers the true causal contribution of every step.

### 5.2 Minimum publishable contribution

The smallest coherent paper has three pieces:

1. a benchmark protocol with intervention-derived credit signs and risk-coverage metrics;
2. CPCD-Lite with overlap-aware interval abstention and context/target separation;
3. controlled evidence that the credit signal, not token reduction, causes the policy gain.

The learned semantic graph, memory operations, SWE scaling, and negative DPO can be added only after this core survives falsification.

### 5.3 Main novelty risks

- **P2T overlap:** process graphs and grounded segment selection are already demonstrated with privileged SWE patches. The distinction must be fixed-log observational variation, signed intervals, and abstention.
- **Agentic-DPO overlap:** state-conditioned offline preference learning is already a strong baseline. The distinction must come from failures, delayed provenance, and supported negative/ambiguous cases.
- **ATLaS overlap:** merely replacing a selector prompt with a score model is insufficient. Direct intervention calibration is necessary.
- **DML-IL overlap:** generic doubly robust or instrumental-variable imitation learning is not new. The contribution must be the long-horizon semantic treatment definition, compiler, and empirical causal-credit benchmark.
- **Fast-moving 2026 literature:** rerun a primary-source and OpenReview search immediately before claiming novelty or submission.

### 5.4 Implementation order

1. Freeze a multi-policy ALFWorld/WebShop log set and define restorable branch points.
2. Implement deterministic segmentation, support cells, and branch-effect evaluation.
3. Run full SFT, random mask, ATLaS-style mask, and CPCD-Lite before building a semantic graph model.
4. Add failure traces and test whether positive credit can recover useful prefixes.
5. Move to tau-bench only if intervention precision is calibrated.
6. Add provenance and long-delay buckets.
7. Add SWE and observed-pair DPO last.

---

## 6. Literature-search notes

The focused search used combinations of `offline trajectory`, `selective SFT`, `critical step`, `expert failure`, `hindsight relabeling`, `state-conditioned preference`, `privileged process supervision`, and `long-horizon agent` over 2024-2026. arXiv, OpenAlex, and Semantic Scholar returned a useful candidate set, followed by primary arXiv-page verification. The unified search experienced repeated rate limits and OpenReview could not be exhaustively queried in this environment, so this memo is a focused research review rather than a formal systematic review. Numerical gains reported by individual papers are not compared as a leaderboard.

### Suggested reading order

1. [ATLaS](https://arxiv.org/abs/2503.02197): cleanest selective-SFT formulation.
2. [STeP](https://arxiv.org/abs/2505.20023) and [EEF](https://arxiv.org/abs/2504.13145): context/target separation and useful failure fragments.
3. [Q-SFT](https://arxiv.org/abs/2411.05193) and [AWR](https://arxiv.org/abs/1910.00177): value-weighted likelihood foundations.
4. [HPL](https://arxiv.org/abs/2510.03253) and [Agentic-DPO](https://arxiv.org/abs/2607.10601): offline preference learning at group and state granularity.
5. [SWE-Lego](https://arxiv.org/abs/2601.01426) and [P2T](https://arxiv.org/abs/2605.21996): realistic long-horizon SWE SFT and privileged process curation.
6. [HSL](https://arxiv.org/abs/2607.04235) and [AgentHER](https://arxiv.org/abs/2603.21357): hindsight use of unintended or failed outcomes.
7. [DML-IL](https://arxiv.org/abs/2502.07656): the causal-identification warning and theoretical foundation.
8. [IPR](https://arxiv.org/abs/2406.11176) and [CSO](https://arxiv.org/abs/2602.03412): intervention-based upper bounds and evaluation design.
9. [ECHO](https://arxiv.org/abs/2606.31650): long-delay memory provenance and credit routing.
