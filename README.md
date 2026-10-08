# NCMA Engineering

**Applied AI, agentic systems, deterministic validation and operational engineering.**

This repository is the public engineering portfolio of **Valter Lourenço Junior**. It describes selected projects built within the NCMA Systems lab, with explicit boundaries between **implemented**, **measured**, **pending**, and **unreleased** work.

> The aim is not to claim that every subsystem is production-ready. It is to show engineering decisions, failure analysis, verification methods and the reasoning behind them.

**[Professional portfolio](https://ncmasystems.com) · [GitHub profile](https://github.com/Ozzurac) · [LinkedIn](https://www.linkedin.com/in/valter-l-junior/)**

## Projects

| Case study | What it demonstrates | Public artifact |
| --- | --- | --- |
| [Jarvis V2: Local-first AI agent](projects/jarvis-v2.md) | Desktop agency, local model/voice integration, human authorization, execution receipts, product acceptance | Architecture and dated evidence; **not a release** |
| [ModForge V2: Validation against reality](projects/modforge-v2.md) | Parsing, inheritance resolution, schema proof, defect injection and independent runtime probes | Failure analysis and verification methodology |
| [NCMA MCP Gateway](projects/mcp-gateway.md) | Fixed tool allowlists, bounded actions, risk classification, prepare/confirm and auditability | Security-oriented system design |
| [Web Research V1](projects/web-research-v1.md) | Search/extraction pipeline, cited research and integration constraints | Work-in-progress technical case |
| [Local LLM benchmark](projects/local-llm-benchmarking.md) | Controlled Qwen3.5-4B / Ministral / Phi-4 inference comparison, resource profiling and completion accounting | **Historical offline case study with explicit limits** |
| [Brazilian Portuguese ASR benchmark](projects/asr-benchmarking.md) | Fixed 24-clip comparison, WER/CER, latency and runtime qualification | **Historical offline model benchmark** |
| [Fine-tuning and evaluation](projects/fine-tuning-evaluation.md) | Held-out ASR adaptation, LoRA export parity and safety limitations | **Dated training/evaluation case study** |
| [Agent Governance Lab](https://github.com/Ozzurac/agent-governance-lab) | Small Python implementation of a governed tool loop | **Runnable source + automated tests** |
| [NCMA Validation Patterns](https://github.com/Ozzurac/ncma-validation-patterns) | Safe error contracts, asymmetric regression comparison and quality metrics | **Standalone runnable Python + automated tests** |

## Common engineering principles

1. **Authority belongs to the controller.** LLM output is a proposal, never authorization.
2. **Verify effects.** An API acknowledgement is not proof that a requested outcome happened.
3. **Never turn missing evidence into PASS.** An unknown result must stay unknown or fail closed.
4. **Design for observability.** Capture identity, intent, policy decisions, execution receipts, and verification.
5. **Prefer a working vertical slice.** A partial but usable product beats a sprawling architecture without a working user path.
6. **Separate claims by maturity.** Unit tests, model evaluation, live integration and user acceptance prove different things.

## Read the evidence

[Evidence register](EVIDENCE.md) documents dated measurements and their limits. [Publication policy](PUBLICATION.md) explains what is intentionally withheld.

## Scope and licensing

This is a **documentation-first portfolio**, not a mirror of private repositories. Operational endpoints, authentication material, infrastructure topology, private messages, raw user media, copyrighted game data, model weights and unreleased internal source are excluded.

The separate [Agent Governance Lab](https://github.com/Ozzurac/agent-governance-lab) provides a self-contained public demonstration. Other original project sources remain private pending explicit line-by-line review.

For professional or technical contact: [LinkedIn](https://www.linkedin.com/in/valter-l-junior/).