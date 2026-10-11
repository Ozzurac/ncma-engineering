# Hybrid systems | Logical architecture

**Question:** how can local interaction, optional external inference and private service components coexist without giving a model unrestricted operational authority?

This is a **sanitized capability-zone diagram**, not a literal network, runtime service-discovery map or deployment inventory. Boxes describe classes of responsibility, not individual hosts. Arrows describe intended information flow and control boundaries; cross-workstream integration must be separately proven.

![NCMA Hybrid Systems architecture, showing local interaction, bounded controller and separated private service domains](assets/hybrid-systems.svg)

*Figma-authored editorial overview. Logical boundaries only; not deployment topology or proof of a fully integrated runtime. [Visual provenance and publication review](assets/README.md).*

**Technical references (Mermaid):** two compact flows below separate **model selection** from **action authority**. They deliberately avoid one giant inferred deployment graph.

<details>
<summary>Flow A: local-first interaction and optional inference</summary>

```mermaid
flowchart TB
    INPUT["Voice, text or screen"] --> JARVIS["Jarvis session"]
    JARVIS --> ROUTER["Model selection"]
    ROUTER --> LOCAL["Local inference"]
    ROUTER -.-> HOSTED["Hosted inference (optional)"]
```

*The hosted path represents a selected fallback/profile, not unrestricted model access.*

</details>

<details>
<summary>Flow B: controlled capability and verification</summary>

```mermaid
flowchart TB
    PROPOSE["Proposed operation"] --> CHECK["Typed scope and policy check"]
    CHECK --> AUTH{"Authorized?"}
    AUTH -->|"No or unknown"| DENY["DENIED / UNKNOWN"]
    AUTH -->|"Yes"| ADAPTER["Bounded domain capability"]
    ADAPTER --> EVIDENCE["Receipt and observed evidence"]
    EVIDENCE --> REVIEW["Human review where required"]
```

*Consequential changes require trusted owner confirmation. A model suggestion or adapter acknowledgement is not evidence of a verified effect.*

</details>

**Service capabilities and what can be inspected**

| Logical capability | Allowed role when separately integrated | Evidence boundary |
| --- | --- | --- |
| Search and extraction | Bounded discovery | Source URLs and extracted references |
| Knowledge retrieval | Read-only discovery/ranking | Provenance and retrieved candidates; not domain truth |
| Domain validation / build | Domain-scoped checks and operations | Validation receipts and independently observed effects where available |

These are **distinct workstreams and responsibility classes**, not proof that Jarvis, Knowledge Runtime, Web Research and ModForge share one deployed gateway or completed end-to-end integration.

## Verified component boundaries

| Workstream | Publicly supportable claim | What is *not* established |
| --- | --- | --- |
| [Jarvis V2](../projects/jarvis-v2.md) | Local-first runtime, voice/model components and controller-governed execution have documented implementation and test checkpoints | Release-ready general autonomous control |
| [Web Research V1](../projects/web-research-v1.md) | Privately hosted search/extraction components were deployed and tested at the cited checkpoint | Accepted full cited-synthesis loop |
| [Knowledge Runtime](../projects/knowledge-runtime.md) | Durable evidence history and a retrieval profile have read-only operational health evidence | Retrieval as final domain truth or proof of cross-product integration |
| [ModForge V2](../projects/modforge-v2.md) | Deterministic validation and independent observation are documented | General proof that every gameplay effect or build is correct |
| [Human Plane](../projects/human-plane.md) | Owner-controlled review and bounded mutation are architecture responsibilities | A standalone autonomous operator |

The exact geographic placement, virtual-machine composition, addresses, access configuration and trust policy are private. **Hybrid** refers here to *classes of computing and model-serving boundaries*, not disclosure of the actual infrastructure diagram.

## Design trade-offs

- Local inference provides an option for resource-aware and privacy-conscious interaction; resource limits may require a separately approved remote model path.
- Hosted inference can assist synthesis, but model output remains **untrusted input** to controllers.
- Private search and retrieval can collect evidence, but evidence *availability* is not evidence *correctness*.
- A backend success response is not proof of the external effect. Verification and human acceptance remain separate stages.
- Failure, missing source or inaccessible validation must preserve **UNKNOWN / INCONCLUSIVE**, not manufacture a PASS.

**Public illustrations deliberately omit operational infrastructure.** See [publication policy](../PUBLICATION.md) and [publication review](PUBLICATION_REVIEW.md).
