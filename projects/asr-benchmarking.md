# Case study: Brazilian Portuguese ASR model benchmark

**Date:** 2026-09-15. **Scope:** bounded offline qualification, not production.

## Method

Three ASR candidates used the **same fixed 24 FLEURS Portuguese (Brazil)
test clips**, totaling **148.62 seconds** (4.80-6.96 seconds each). Evaluation
progressed through 1, 3 and 24 clips. Models did not receive prompt hints,
reference answers or scoring exemptions.

The lab recorded process load, first inference, subsequent/warm latency, word
and character errors, normalized exact-utterance agreement and aggregate
real-time factor. CUDA provider availability and waveform-format handling were
qualified before interpreting model performance.

WER and CER use aggregate edit distance over reference word/character totals.
RTF = sum(inference seconds) / sum(audio seconds); smaller is faster.

## Recorded result (same 24 clips)

| Candidate | WER | CER | Exact transcripts | Warm median | RTF |
| --- | ---: | ---: | ---: | ---: | ---: |
| Parakeet TAGARELA ONNX | **3.69%** | 2.20% | **20/24** | **89 ms** | **0.0179** |
| faster-whisper small CUDA/FP16 | 7.38% | 2.66% | 13/24 | 200 ms | 0.0360 |
| Qwen3-ASR 0.6B | 5.90% | **1.94%** | 13/24 | 1,242 ms | 0.3222 |

Qwen's lower CER did not imply better word-level accuracy or whole-utterance
exactness. Parakeet was the **offline priority candidate**, but microphone,
integration and overall product gates remained separate.

The platform was an RTX 5070 12 GB Windows workstation. The approximate
whole-GPU memory deltas observed above a sampled baseline (3,641 MiB,
855 MiB and 2,010 MiB, respectively) are **not exclusive model VRAM readings**.
Cold/cache effects also prevent interpreting these numbers as cold-boot SLAs.

## Lessons

A test harness can fail before a model does. The experiment uncovered GPU
library-loader and floating-point WAV ingestion defects in the isolated test
stack, and corrected them before qualification. Timings, correctness,
model-load readiness and production authorization were recorded as distinct
claims rather than collapsed into one pass/fail indicator.

This 24-clip sample is not a universal Portuguese accent/noise benchmark and
is not statistically sufficient to promise a general deployment accuracy.

**Reproducibility:** Original audio, model snapshots, internal runner, transcripts
and host setup stay private. The public synthetic-data implementation in
`ncma-validation-patterns` is separately authored and does not reproduce this run.
