# Beyond Fine-Tuning: What We Learned Building AI Systems

> **NCMA Engineering / Applied research notes / September–October 2026**  
> **Status:** published-evidence synthesis, not production certification.  
> **Author:** Valter Lourenço Junior, independent NCMA Systems laboratory.

**Em português:** Esta publicação revisita nosso whitepaper de setembro de 2026, mas troca a lista de técnicas por resultados reais: uma adaptação de ASR que funcionou, um LoRA que preservou o comportamento mas não atingiu qualidade operacional, benchmarks rápidos que terminaram em truncamento e um sistema híbrido que concluiu uma tarefa sintética que uma chamada monolítica não conseguiu resolver.

**Read in Portuguese:** [Além do Fine-Tuning — edição completa](alem-do-fine-tuning-2026.md).

---

## We started with a map. The experiments forced us to redraw it.

In September, our *From a Raw LLM to an Operational AI System* whitepaper arranged the field into more than thirty topics: checkpoints, quantization, continued pretraining, SFT, LoRA, decoding, memory, retrieval, tools, agents, perception, governance and evaluation.

That map was helpful for deciding *where* a capability belongs. It did not tell us which changes would matter in practice, nor which failures would survive a technically successful training run.

Over the following weeks, our independent NCMA lab worked through several uncomfortable distinctions:

- Training a model is not the same as improving an application.
- Preserving behavior across export formats is not the same as achieving useful behavior.
- Fast decoding is not the same as a complete, correct result.
- A large context window is not the same as completing a long task.
- A plausible tool call is not permission to cause an external effect.
- Passing an isolated test is not evidence of end-to-end product readiness.

These distinctions became more valuable than a longer catalog of techniques.

## Case 01 / A narrowly defined fine-tune can work extremely well

We adapted a speech recognition model for Brazilian Portuguese technical utterances. This was **not** LLM LoRA research; it was a separate speech-model adaptation track.

The split was deliberate: 204 domain/replay examples per epoch for training, 40 development examples for checkpoint selection, a frozen 80-example domain holdout, and a separate fixed 24-clip FLEURS pt-BR sample for general-language regression.

Training loss continued to decrease by epoch three, but development and general-language metrics did not improve. We therefore selected **epoch two**, then evaluated the holdout once.

| Frozen domain holdout | Source baseline | Selected checkpoint |
| --- | ---: | ---: |
| Word Error Rate (WER) | **21.831%** | **2.1127%** |
| Entity-exact utterances | 22 / 80 | **73 / 80** |
| Entire transcript exact | 20 / 80 | **73 / 80** |

On the separate 24-clip FLEURS sample, WER changed from 3.69% to 1.845%; the count of normalized exact clips remained 20/24. This is why one accuracy number is insufficient. The serialized checkpoint was independently reloaded and checked, including ONNX behavior.

**What changed in our practice:** state the target capability before training, freeze the holdout, choose the checkpoint on development evidence, and guard against regressions outside the narrow domain. We did **not** generalize an 80-utterance result into a universal transcription guarantee.

**Evidence:** [Fine-tuning and holdout case](../projects/fine-tuning-evaluation.md), [24-clip ASR comparison](../projects/asr-benchmarking.md).

## Case 02 / The adapter survived the export pipeline. The defects survived too.

A different track used supervised LoRA on a local Qwen3.5-4B model. We checked behavior at four boundaries: PEFT adapter, merged Hugging Face weights, F16 GGUF conversion, and Q4_K_M quantization.

The frozen 48-task suite was a **development diagnostic**, not a locked-holdout release protocol.

| Artifact under test | Tasks passed / 48 |
| --- | ---: |
| Matched unrefined control | 0 |
| PEFT LoRA adapter | **15** |
| Merged HF weights | **15** |
| F16 GGUF | **15** |
| Q4_K_M GGUF | **14** |

The adjacent export stages changed pass/fail outcomes by 0, 0 and 1. That is useful evidence of **artifact parity**.

But four safety-violation cases remained in every arm. The absolute task pass rate was poor. In other words, the export procedure worked and the candidate still failed the operational bar.

We stopped treating an adapter that merges cleanly, loads successfully, or emits valid structured output as if it were automatically ready to operate a tool.

**Evidence:** [Fine-tuning and export parity case](../projects/fine-tuning-evaluation.md).

## Case 03 / Fast generation can produce an unfinished answer

A September local-model screening compared Qwen3.5-4B, Ministral-3-8B-Instruct and Phi-4-multimodal-instruct on a small fixed workload under stated GPU constraints.

| Candidate | Median decode rate | Completed text outputs |
| --- | ---: | ---: |
| Qwen3.5-4B | **108.39 tok/s** | 22 / 25 |
| Ministral-3-8B-Instruct | 77.23 tok/s | 20 / 25 (one skipped) |
| Phi-4-multimodal-instruct | 63.54 tok/s | **25 / 25** |

Qwen's three image tasks reached their 256-token generation cap. These were not corrected by secret extra prompts and counted as incomplete.

The models also used different backends in part of this matrix; the observed resource figures and time-to-first-token readings are not universal, directly portable rankings.

**What changed:** our evaluation reports separately track *speed*, *whether the response ended properly*, *whether the answer was correct*, and *whether it can be used safely downstream*.

**Evidence:** [Dated local LLM benchmark](../projects/local-llm-benchmarking.md).

## Case 04 / A 96K context allocation did not finish the task

In October we tested a local GPT-OSS 20B MXFP4 stack on a synthetic graph-route audit task. This became our clearest controlled illustration of **context capacity versus task progress**.

The G3 monolithic run reserved 98,304 context tokens. It consumed **77,103 input tokens** and **16,384 output tokens**, hit the generation limit, and produced **no final JSON result**. It returned zero of the twelve requested synthetic cases.

The conclusion was not that long-context models are useless. It was more specific: in this workload, a large prompt and a large output allowance still failed to deliver a finite, verifiable artifact.

A model that can attend to a body of evidence is not necessarily a system that can reliably traverse it and keep a durable record of what has already been established.

**Evidence:** [G2 → G3 → G4H comparison](https://github.com/Ozzurac/ncma-local-agent-lab/blob/main/docs/G2_G3_G4H_COMPARISON.md).

## Case 05 / Instead of increasing the budget again, we changed the shape of work

In G4H we kept the **same twelve-case synthetic graph-audit corpus** as G3, but replaced the monolithic procedure with a bounded hybrid coordinator.

The system retrieved only the source rows needed for the current step, asked the model for limited candidates, used a HIGH-effort draft and MEDIUM-effort JSON finalizer, verified candidates **against independent source rules**, and persisted only accepted transitions in atomic checkpoints.

```text
task + indexed synthetic source
    -> focused retrieval
    -> HIGH bounded draft
    -> MEDIUM JSON proposal
    -> independent source validator
       -> reject unsupported hop / preserve prior state
       -> accept verified hop / commit checkpoint
    -> resume / repeat
    -> independent final synthetic oracle (after inference)
```

**Observed G4H outcome:**

| Observation | Recorded result |
| --- | ---: |
| Cases matching the independent oracle | **12 / 12** |
| Exact result fields | **60 / 60** |
| Accepted source-verified hops | 78 |
| Rejected candidate hops | 28 |
| Checkpoint rounds | 22 |
| Model calls | 44 |

A new Python process could resume the checkpoint rather than starting the task over.

There are important qualifications. G4H did **not** send 96K input tokens in a single successful inference. The improvement belongs to the **whole coordinator plus model plus validators**, not to a suddenly enhanced standalone model. This is a synthetic, task-specific demonstration; it establishes neither general reasoning accuracy nor safe autonomous computer operation.

G4H also required more calls and engineering machinery. The accumulated recorded model-call time was 1,531.6 seconds, excluding host overhead. We did not discover a free speed optimization; we chose **verifiable completion and recovery** over one-shot simplicity for this fixture.

**Evidence and reproduction:** [Public local agent lab](https://github.com/Ozzurac/ncma-local-agent-lab), [G2/G3/G4H methodology](https://github.com/Ozzurac/ncma-local-agent-lab/blob/main/docs/G2_G3_G4H_COMPARISON.md), [G4H sequence](https://github.com/Ozzurac/ncma-local-agent-lab/blob/main/docs/G4H_SEQUENCE.md).

## What changed in our architecture decisions

The lessons from these separate tracks formed a shared engineering approach:

1. **Make the model's responsibility small and explicit.** Ask it to interpret, hypothesize or propose, not to impersonate the authorization layer.
2. **Choose evidence before scale.** Retrieve the relevant authoritative material rather than feeding every document into every prompt.
3. **Treat model output as untrusted input.** JSON validity, fluent explanations and confidence do not prove source correctness.
4. **Verify effects with software that can say no.** Schemas, source rules, tests, permissions and observed runtime behavior remain independent.
5. **Persist verified progress, not merely conversation history.** A checkpoint is a recovery artifact, not a motivational summary of what an agent thinks it did.
6. **Compare against frozen baselines and preserve negative results.** A failed run is a measured observation, not clutter to remove from a presentation.
7. **Separate lab qualification from user acceptance.** Component tests, integration behavior, physical interaction and release authorization are different gates.

These are not claims that determinism can solve every semantic task. Independent validators only verify properties for which we can establish reliable rules or evidence. When truth remains uncertain, the correct state can be **unknown**.

## What we have *not* proven

The broader NCMA laboratory continues exploring semantic copilots, voice interfaces, on-screen assistance, tool-hosted agents and Agent Client Protocol (ACP) integration patterns. Some of these areas are only prototypes or investigations and are **not** being presented here as accepted or production systems.

In particular, the published G4H result must not be confused with an agent that independently controls a workstation, passes all real-world workflows, or has proven safety under adversarial input.

A useful architecture diagram is a map. A unit test is a local fact. A benchmark is a bounded observation. None substitutes for evidence that the *intended product outcome* occurred.

## The conclusion we did not expect to emphasize

Our original whitepaper asked what techniques could transform a raw model into an operational AI system. The subsequent experiments shifted the question:

> **What architecture lets a probabilistic component contribute to work that can be completed, verified, interrupted, resumed and audited?**

Sometimes the answer is better data and a targeted fine-tune. Sometimes it is a smaller context, a stricter controller, source-grounded validation or a better recovery boundary.

The aim is not to make the model sound certain. It is to make the system's **actual level of certainty observable**.

---

### Evidence register and publication boundaries

- [Evidence register](../EVIDENCE.md) — dated checkpoints and their limitations.
- [Publication policy](../PUBLICATION.md) — exclusions, ownership, sanitization and claim levels.
- [NCMA Architecture Atlas](../architecture/README.md) — sanitized logical views, not live private infrastructure topology.
- [Local Agent Lab](https://github.com/Ozzurac/ncma-local-agent-lab) — reproducible synthetic fixtures, checkpoint methodology and negative evidence.

This article is an independent engineering retrospective, not a general model leaderboard, release announcement or description of confidential employer systems. Datasets, weights, private runtime logs, operational endpoints, secrets and personal content remain excluded.

**NCMA Systems • Applied AI, agentic systems and operational engineering • October 20, 2026**
