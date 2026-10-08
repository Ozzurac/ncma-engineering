# Case study: local multimodal LLM benchmark

**Focus:** inference profiling, hardware-aware model selection, completion accounting, and behavioral evaluation.

**Snapshot:** 2026-09-13. **Status:** historical offline evaluation. Not a production acceptance or a universal model ranking.

## Question

Which local model offered the best balance of speed, resource headroom, and practical multimodal quality within a 12 GB GPU budget?

A controlled daily-assistant workload compared **Qwen3.5-4B**, **Ministral-3-8B-Instruct-2512**, and **Phi-4-multimodal-instruct**.

## Method

- Same frozen Portuguese-focused cases: **25 text**, **3 screen-image**, and **3 audio** cases where supported.
- Context length **4,096**, output ceiling **768 tokens**; image tasks had a separate **256-token** ceiling.
- Fixed sampling: temperature 0.7, top-p 0.8, top-k 20, repetition penalty 1, and seed 42 where available.
- Prompt cache disabled. No tools, web, retrieval, external memory, repair, or model-output retries.
- One model/process load and **one sequential evaluation pass per candidate**.
- Tracked load time, response latency, time to first token (TTFT), decode tokens/s, memory, termination/completion, and separate quality-rubric scores.
- Qwen and Ministral used a pinned llama.cpp runtime; Phi used a separate ONNX Runtime GenAI worker. Cross-backend comparisons therefore require caution.

## Observations

| Candidate | Median decode tokens/s | Median response latency | Peak observed GPU memory | Completed text outputs |
| --- | ---: | ---: | ---: | ---: |
| Qwen3.5-4B | **108.39** | **1.631 s** | **4,971 MiB** | 22/25 |
| Ministral-3-8B-Instruct | 77.23 | 2.310 s | 7,601 MiB | 20/25 (one skipped) |
| Phi-4-multimodal-instruct | 63.54 | 2.479 s | 11,869 MiB | **25/25** |

**Completion is not correctness.** Qwen's remaining text cases hit the output cap. All three Qwen image responses were **truncated at 256 tokens**, despite comparatively useful visual grounding within those partial responses. Phi exercised audio, but its recorded responses were not sufficiently faithful transcriptions to justify defaulting to integrated audio.

Subjective text-quality ratings from one engineering rubric were **3.5/5** for Qwen, **3.2/5** for Ministral, and **2.5/5** for Phi. These are not statistically validated, blinded preference results.

## Interpretation and limitations

**Qwen3.5-4B was the provisional daily-tier choice** on this workload: throughput, available GPU memory, and visual grounding favored it. The decision did not authorize production integration.

- **TTFT from the llama.cpp and ONNX interfaces is not directly comparable.**
- Memory figures are observed local peaks, not portable model-exclusive VRAM allocations.
- One pass per candidate is a screening experiment, **not a confidence interval**.
- The common corpus was small and not a general-purpose academic leaderboard.
- A malformed or truncated answer stayed a failure; it was not re-prompted into a pass.
- Correct JSON format cannot establish safe action selection. One ambiguous-action case specifically motivated a mandatory clarification/controller boundary.

The historical larger-model reference was **not** rerun under this same candidate matrix, so it is not included in the comparison table.

## Public reproducibility

Private model adapters, raw prompts, screenshots, run transcripts and model files are withheld. The new [public model-evaluation examples](https://github.com/Ozzurac/ncma-validation-patterns/blob/main/MODEL_EVALUATION.md) demonstrate bounded completion and latency accounting with synthetic data; they do **not** reconstruct this run.

**Related:** [ASR benchmarking](asr-benchmarking.md) | [Fine-tuning](fine-tuning-evaluation.md) | [Jarvis V2](jarvis-v2.md).
