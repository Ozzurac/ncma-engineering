# Evidence register

These are **dated internal engineering observations**, reproduced here in sanitized form. They are not continuously updated service-level guarantees.

| Date | System / scope | Observation | Limits |
| --- | --- | --- | --- |
| 2026-08-11 | ModForge V1 validation | 210 checks passed; three target mods still broken | Evidence of test-to-product mismatch, not V2 performance |
| 2026-09-05 | ModForge V2 source checkpoint | 845 passed / 13 skipped / 0 failed | Source milestone, not proof of production-runtime promotion |
| 2026-09-15 | Jarvis offline STT bake-off | 24 FLEURS pt-BR clips, 148.62 s; Parakeet 3.69% WER; faster-whisper small 7.38%; Qwen3-ASR 5.90% | Laboratory corpus, warm host effects, no full-product claim |
| 2026-10-03 | Jarvis smoke-recorder change | Expanded deterministic regression: 1,016 tests + 291 subtests passed | Overlaps focused selections; physical owner acceptance still pending |
| 2026-10 | Web Research V1 | Search/extraction deployed and component tests recorded | End-to-end cited synthesis not yet accepted |

## Evidence quality hierarchy

1. Observed runtime effects with independent verification.
2. Deterministic integration checks and bounded reproduction records.
3. Unit/component tests.
4. Measured offline model benchmarks on stated datasets.
5. Architecture documents and plans.

A higher test count alone does not imply a higher maturity level.

For sensitive reasons, raw internal traces, executable environments, model weights and personal recordings are not distributed. Project claims are intentionally scoped to the date and test surface recorded.