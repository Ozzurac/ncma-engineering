# Case study: fine-tuning, holdout discipline and model export parity

**Historical experiments:** September 2026. **Focus:** transfer learning,
checkpoint selection, domain retention, LoRA SFT and quantization validation.

These are **two distinct research tracks**, not one training campaign. Original
models, training data, prompts, corpora and full training source stay private.

## Track A: Portuguese speech-model adaptation (2026-09-16)

**Objective:** improve transcription of technical utterances without regressing
on common Brazilian Portuguese.

- Domain training: **204 examples per epoch**, combining technical and replay data.
- Development: **40 examples**, eligible for checkpoint selection.
- Frozen domain **holdout: 80 examples**, not used for training or epoch choice.
- General-language anti-regression: **24 fixed FLEURS pt-BR clips**.
- Partial-network adaptation, mixed precision and bounded optimization.
  Exact training implementation and per-layer parameters are intentionally withheld.

The second and third epochs both scored **40/40** on the development set.
Although epoch three reduced training loss further, general-language and dev
metrics did not improve. **Epoch two was selected**, then the 80-example
holdout was evaluated once.

| Frozen domain holdout | Source baseline | Selected checkpoint |
| --- | ---: | ---: |
| WER | **21.831%** | **2.1127%** |
| Entity-exact | **22/80** | **73/80 (91.25%)** |
| Whole-transcript exact | **20/80** | **73/80** |

On the separate FLEURS anti-regression sample, **WER decreased from 3.69% to
1.845%**, while exact normalized clips remained **20/24**. That distinction
shows why multiple metrics matter.

The selected checkpoint's serialized form was reloaded independently and
reproduced heldout results, including subsequent ONNX inference checks. An
isolated live microphone qualification later accepted one bounded utterance
with **73 ms warm STT latency**. One real-microphone sample is not a latency
percentile. This gate did not itself authorize full agent release.

## Track B: local Qwen3.5-4B LoRA SFT and export parity (2026-09-27)

A separate LLM research track trained a supervised adapter and evaluated
behavior after each export boundary:

1. PEFT LoRA adapter;
2. merged Hugging Face weights;
3. F16 GGUF conversion;
4. Q4_K_M GGUF quantization.

A frozen **48-task development suite** used a bounded greedy *diagnostic*
decoding setup, not a final locked-holdout release protocol.

| State | Diagnostic tasks passed / 48 |
| --- | ---: |
| Matched unrefined control | **0** |
| Adapted PEFT | **15** |
| Merged HF | **15** |
| F16 GGUF | **15** |
| Q4_K_M GGUF | **14** |

Pass/fail differences across adapter -> merge -> F16 -> Q4 were **0, 0 and 1**.
Those are useful artifact-parity findings, **not evidence of product safety**:
**four safety-violation cases remained in each diagnostic arm**, and absolute
task success was still low. The *export* gate passed; the candidate was
**not certified for production or autonomous execution**.

Later uncommitted training experiments are deliberately excluded from this
public document until their records can be reviewed independently.

## Methodological takeaways

- Train/development selection and **locked heldout** answer different questions.
- Lower training loss alone cannot select a production checkpoint.
- General-data anti-regression is essential when specializing a model.
- Validate the same behavior after merging, serialization and quantization.
- New gains do not cancel unresolved safety or instruction-following defects.
- Report a technical gate as exactly that, **not** as product readiness.

**Public code:** The `ncma-validation-patterns` synthetic standalone examples
show split-overlap checks and dev-only checkpoint selection, but contain no
private training recipe, original harness or training data.
