# Evidence register

These are **dated internal engineering observations**, reproduced here in sanitized form. They are not continuously updated service-level guarantees. Experimental gains and export parity are distinct from product acceptance; none of the model experiments below certifies safety or release readiness.

| Date | System / scope | Observation | Limits |
| --- | --- | --- | --- |
| 2026-08-11 | ModForge V1 validation | 210 checks passed; three target mods still broken | Evidence of test-to-product mismatch, not V2 performance |
| 2026-09-05 | ModForge V2 source checkpoint | 845 passed / 13 skipped / 0 failed | Source milestone, not proof of production-runtime promotion |
| 2026-09-13 | [Local LLM benchmark](projects/local-llm-benchmarking.md) | Qwen3.5-4B / Ministral / Phi-4: median decode 108.39 / 77.23 / 63.54 tokens/s; median response 1.631 / 2.310 / 2.479 s; observed GPU peaks 4,971 / 7,601 / 11,869 MiB; completed text outputs 22/25 / 20/25 (one skipped) / 25/25 | One sequential pass per candidate; cross-backend TTFT not directly comparable; memory not model-exclusive; Qwen's remaining text cases hit the cap and all 3 image outputs were truncated; completion is not correctness or product acceptance |
| 2026-09-15 | [Jarvis offline STT bake-off](projects/asr-benchmarking.md) | 24 FLEURS pt-BR clips, 148.62 s; Parakeet 3.69% WER; faster-whisper small 7.38%; Qwen3-ASR 5.90% | Laboratory corpus, warm host effects, no full-product claim |
| 2026-09-16 | [Track A: Portuguese speech-model adaptation](projects/fine-tuning-evaluation.md#track-a-portuguese-speech-model-adaptation-2026-09-16) | Frozen 80-example domain holdout: WER 21.831% -> 2.1127%; entity-exact 22/80 -> 73/80 (91.25%); separate 24-clip FLEURS anti-regression WER 3.69% -> 1.845% | Epoch two selected on development evidence before one holdout evaluation; bounded adaptation success, not general deployment accuracy or full-agent release; a later single-microphone 73 ms warm result is not a latency percentile |
| 2026-09-27 | [Track B: Qwen3.5-4B LoRA SFT and export parity](projects/fine-tuning-evaluation.md#track-b-local-qwen35-4b-lora-sft-and-export-parity-2026-09-27) | Diagnostic task passes /48: unrefined control 0, adapter 15, merged HF 15, F16 GGUF 15, Q4_K_M 14; adjacent export pass/fail differences 0 / 0 / 1; export gate passed | Development diagnostics, not locked-holdout release evaluation; four safety-violation cases remained in each diagnostic arm and absolute success was low; parity does not certify safety, production or autonomous execution |
| 2026-10-03 | Jarvis smoke-recorder change | Expanded deterministic regression: 1,016 tests + 291 subtests passed | Overlaps focused selections; physical owner acceptance still pending |
| 2026-10 | Web Research V1 | Search/extraction deployed and component tests recorded | End-to-end cited synthesis not yet accepted |

## Evidence quality hierarchy

1. Observed runtime effects with independent verification.
2. Deterministic integration checks and bounded reproduction records.
3. Unit/component tests.
4. Measured offline model benchmarks on stated datasets.
5. Architecture documents and plans.

A higher test count alone does not imply a higher maturity level.

For sensitive reasons, raw internal traces, executable environments, private datasets, original training code, model weights, internal prompts, private infrastructure details and personal recordings are not distributed. Project claims are intentionally scoped to the date and test surface recorded.