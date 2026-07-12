# Default stacks

## Recommended starting combination (agentic CA)

| Component | Choice | Link |
|-----------|--------|------|
| Envs | ALFWorld + WebShop | [alfworld](https://github.com/alfworld/alfworld) · [WebShop](https://github.com/princeton-nlp/WebShop) |
| Policy (comparable tables) | Qwen2.5-Instruct 1.5B & 7B | Model Hub / local cache |
| Policy (2026 transfer) | Qwen3-Instruct (e.g. 1.7B) | Used in TRIAGE |
| Baselines | GRPO, GiGPO | veRL / verl-agent |
| Trainer | veRL or verl-agent or ProxMO-RL | links below |

## Open training frameworks

| Framework | Link | OSS | Notes |
|-----------|------|-----|-------|
| veRL | https://github.com/volcengine/verl | ✅ | Common LLM RL backbone |
| verl-agent | https://github.com/langfengQ/verl-agent | ✅ | GiGPO / HGPO recipes |
| ProxMO-RL | https://github.com/FlyTune/ProxMO-RL | ✅ | Plug-in CA; ALFWorld/WebShop scripts |
| HiPER-agent | https://github.com/JonP07/HiPER-agent | ✅ | Hierarchical CA |
| VinePPO | https://github.com/McGill-NLP/VinePPO | ✅ | Reasoning MC credit |
| PURE | https://github.com/CJReinforce/PURE | ✅ | PRM + min-form |
| CAPO | https://github.com/andyclsr/CAPO | ✅ | GenPRM |
| DelTA | https://github.com/RUCBM/DelTA | ✅ | Token discriminative CA |
| OpenRLHF | https://github.com/OpenRLHF/OpenRLHF | ✅ | Alt stack (e.g. C3) |

## Data shape note

Most agentic CA papers **do not** release a dedicated offline “credit assignment dataset”. Training signal = **environment rollouts + outcome verifier** (sometimes process/role judges). Reasoning lines may use **PRM800K** or math corpora.
