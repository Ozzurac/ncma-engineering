# ModForge + Human Plane | Specification to evidence

**Question:** how does an AI-assisted, domain-specific artifact progress from a request to an effect that can actually be trusted?

ModForge is the **domain engineering and validation** workstream. Human Plane is the **operator-facing review and decision** layer. Neither a fluent generated specification nor a successful compiler response is sufficient proof.

![NCMA ModForge and Human Plane, from bounded specification through independent evidence and owner settlement](assets/modforge-human-plane.svg)

*Figma-authored editorial overview. Logical boundaries only; not deployment topology or proof of a fully integrated runtime. [Visual provenance and publication review](assets/README.md).*

**Maintained Mermaid references:** the build gate and owner settlement are separate. Rejected/unknown states return for operator review rather than creating a tangled feedback loop on the page.

<details>
<summary>Flow A: specification, generation and deterministic gates</summary>

```mermaid
flowchart TB
    INTENT["Operator intent"] --> SPEC["Bounded specification"]
    SPEC --> PROPOSAL["Architecture / generation proposal"]
    PROPOSAL --> GATE["Parse, resolve and semantic gates"]
    GATE --> CHECK{"Evidence sufficient?"}
    CHECK -->|"FAIL or UNKNOWN"| REVISE["Return to operator for revision"]
    CHECK -->|"Within scope"| BUILD["Build / validation checkpoint"]
```

*This depicts a generalized engineering sequence, not autonomous promotion to a release.*

</details>

<details>
<summary>Flow B: external observation and human settlement</summary>

```mermaid
flowchart TB
    BUILD["Bounded build checkpoint"] --> PROBE["Independent read-only observation"]
    PROBE --> MATCH{"Predicted state observed?"}
    MATCH -->|"No or unavailable"| REWORK["Rework or UNKNOWN"]
    MATCH -->|"Scoped match"| PACKAGE["Evidence package"]
    PACKAGE --> HP["Human Plane review"]
    HP --> OWNER{"Owner decision"}
    OWNER -->|"Reject or revise"| REWORK
    OWNER -->|"Approve within scope"| ACCEPT["Recorded scoped settlement"]
```

*Observed state is bounded by the probe's reach; owner settlement does not prove universal gameplay correctness.*

</details>

## Responsibility boundaries

| Responsibility | Role |
| --- | --- |
| Specification / generation | Proposed content; **not** permission to deploy |
| Deterministic gates | Enforce defined schemas, domain constraints and explicit refusals |
| Build and validation | Produce bounded technical evidence |
| Runtime observation | Independently inspect selected effective states, **not** all gameplay behaviors |
| Human Plane | Present effective state, consequences, validation evidence and guarded mutation paths |
| Owner settlement | Final human decision on the specific artifact or change |

## Why the separation matters

The V1 incident described in the [ModForge case study](../projects/modforge-v2.md) reported **210 passing checks while three mods were broken**. The V2 response was to distinguish self-consistency from external verification, preserve UNKNOWN and inject defects into validation tests. A later source test pass did **not** automatically promote a production runtime.

The workflow above is **a generalized target lifecycle**, not an assertion that every stage is release-qualified or automatically integrated end to end. Human Plane is not represented as an autonomous agent; it is a layer for **human authority, review and evidence**.

No game assets, private semantic rules, original specifications, specific record identifiers, mutation API, access grants, real build paths or operation tokens are included.

Related: [ModForge V2](../projects/modforge-v2.md) · [Human Plane](../projects/human-plane.md) · [Evidence register](../EVIDENCE.md).
