# Case study: Jarvis V2

**Area:** Local-first AI, desktop agents, speech, model serving, controlled execution

**Status:** Active engineering project; **not release-ready** (source checkpoint: 2026-10-03).

## Problem

A useful desktop assistant must do more than generate plausible tool calls. It must bind a user's intent to the correct target, obtain authorization, observe an outcome, recover safely and explain what actually happened. A fluent model is not a trusted system controller.

## System design

```text
Voice / text / screen context
            |
            v
  Unified assistant runtime
            |
            v
Planner -> bounded capability registry
            |
            v
Controller-owned policy and confirmation
            |
            v
Execute -> observe -> verify
            |
            +----> verified result, repair or refusal
            |
            v
Session context and user-facing report
```

The architecture separates model-generated proposals from the authority to run an action. Typed capabilities, exact target identities, bounded execution and verifier receipts prevent a successful-looking chat response from being mistaken for a completed operation.

## Implemented work

- Unified runtime foundations for conversation, session context and tool-facing execution.
- Controller-governed SAFE / CONFIRM / DENY decisions with owner confirmation for consequential actions.
- Windows desktop observation and browser/UI automation components at different maturity gates.
- Local Qwen 4B inference with controlled profile selection and fallback; model refinement research remains independently qualified.
- Voice capture, wake phrase, STT/TTS integration and product-oriented GUI work.
- Product smoke acceptance recording that distinguishes observed runtime events from owner acceptance.

## Evaluation: offline Brazilian Portuguese speech recognition

A dated comparison on 2026-09-15 used the **same 24 FLEURS pt-BR clips (148.62 seconds)** with independent model runs:

| Candidate | WER | Normalized exact clips | Warm median latency |
| --- | ---: | ---: | ---: |
| Parakeet TAGARELA ONNX | **3.69%** | **20/24** | ~89 ms |
| faster-whisper small CUDA/FP16 | 7.38% | 13/24 | ~200 ms |
| Qwen3-ASR 0.6B | 5.90% | 13/24 | ~1.24 s |

These are **small-corpus laboratory measurements**, not general population accuracy, cold-start SLAs or complete system performance. The results did not by themselves qualify a model for production. No original audio or personally identifiable transcripts are distributed here.

## Verification and limits

The 2026-10-03 product smoke recorder change had a **1,016-test / 291-subtest expanded deterministic regression pass** in the private engineering environment, plus focused and UI runs. Test selections overlap and must not be added together.

Several physical owner acceptance gates remained pending; the broader autonomous agent acceptance gate was blocked. **Neither a successful regression suite nor a model benchmark is a product-release claim.**

## Engineering lessons

- Use a single visible product loop instead of accumulating disconnected modules.
- Preserve the distinction between **model output**, **authorized action**, **execution receipt**, and **verified effect**.
- Treat voice and UI as a real user-facing acceptance path, not only an integration-test fixture.
- Record failures honestly and keep historical experiments out of the active product baseline.

**Related public code:** [Agent Governance Lab](https://github.com/Ozzurac/agent-governance-lab), a standalone educational reference. It is not the Jarvis production code.