# Measurement and qualification | Different gates, different proofs

**Question:** how can a fast model, a green CI run and a working user-facing system be evaluated without confusing their evidence?

![NCMA Measurement and Qualification, distinguishing model benchmarks, software CI, observed effects and product acceptance](assets/measurement-qualification.svg)

*Figma-authored editorial overview. Logical boundaries only; not deployment topology or proof of a fully integrated runtime. [Visual provenance and publication review](assets/README.md).*

**Maintained Mermaid model (detailed decision and exception paths):**

```mermaid
flowchart TD
    INPUT["Declared workload, fixtures, constraints and versions"] --> OFFLINE["Offline model / component evaluation"]
    INPUT --> CODE["Unit, negative and integration checks"]
    OFFLINE --> REPORT["Measured scores, completion and limitations"]
    CODE --> REPORT
    REPORT --> GATE{"Checkpoint and evidence complete?"}
    GATE -->|"No"| REJECT["REJECT / UNKNOWN / investigate"]
    GATE -->|"Yes, bounded"| INTEGRATE["Controlled integration run"]
    INTEGRATE --> EFFECT["Observe actual effects and capture receipts"]
    EFFECT --> OWNER{"Independent owner / product acceptance?"}
    OWNER -->|"No / missing"| HOLD["Not release-qualified"]
    OWNER -->|"Yes, within scope"| STATUS["Accepted for explicitly defined scope"]
```

## Keep the evidence categories separate

| Evidence | What it can establish | What it cannot establish alone |
| --- | --- | --- |
| Offline LLM throughput and latency | Resource and performance behavior on a specified workload | Instruction correctness or safe tool execution |
| ASR WER/CER and frozen holdout | Transcription error on declared clips and normalization | General accuracy across microphones, noise or accents |
| Train/dev/holdout separation | Bounded guard against experimental leakage and checkpoint cherry-picking | Absence of semantic contamination or deployment readiness |
| LoRA merge and quantization parity | Behavior survived selected artifact conversions | Safety; the historical diagnostic still showed violating cases |
| Unit and negative tests | A known implementation checks its specified predicates | Correctness in an external runtime |
| Runtime read-back / independent observation | A defined effect was observed within the probe's reach | Every downstream effect or a product-level acceptance |
| Human sign-off | Owner accepts a particular scope and evidence package | Unlimited future behavior outside that scope |

## Public data to inspect

- [Dated evidence register](../EVIDENCE.md)
- [Local LLM benchmark](../projects/local-llm-benchmarking.md)
- [ASR benchmarking](../projects/asr-benchmarking.md)
- [Fine-tuning and export parity](../projects/fine-tuning-evaluation.md)
- [Runnable synthetic metrics and split checks](https://github.com/Ozzurac/ncma-validation-patterns)

**Observability model:** capture a bounded causal chain of **request intent → controller decision → attempted operation → execution result → observed evidence → final claim**. This is a *logical event schema*, **not** an export of operational logs, trace IDs, dashboards or tracing configuration.

Do not treat a sum of overlapping test selections as independent coverage. Do not extrapolate from an offline model benchmark to production acceptance. Missing evidence remains an explicit unknown.
